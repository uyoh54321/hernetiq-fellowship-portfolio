# DataForge ML Lab - Security Review & Architecture Analysis

This document provides a comprehensive security review of the DataForge ML Lab genomics analysis pipeline, analyzing data flows, trust boundaries, threat paths, and answers to core lab assignment questions based on the deployment repository implementation (`model_loader.py` vs. `model_loader_hardened.py`).

---

## 1. Architecture, Data Flow, and Threat Analysis

* **Architecture & Data Flow:** The application runs as a cloud-hosted service (Streamlit/Codespace backend) functioning as an AI model integration layer for a genomics analysis pipeline. When an analysis is triggered, the app queries an external model repository, downloads the model weights (`genomics_analyzer_v2.pkl` or `.safetensors`) into a temporary local directory (`/tmp/`), loads them into memory via a loader script, and processes patient sample data using `.predict()`.
* **Model & Context Flow:** In the insecure setup, the flow directly fetches an unverified `.pkl` file and deserializes it. Because Python’s `pickle` format executes arbitrary code upon loading, this represents a severe supply-chain vector. The hardened setup interposes a security gate (`picklescan`) and migrates the source format to `.safetensors` via `safe_open` to restrict execution to raw tensor mapping.
* **Tenant Boundaries & Authorization Controls:** The application functions as a single-tenant or shared container pipeline interface. Without robust multi-tenant data isolation or containerized sandboxing, execution happens within the main runtime context.
* **Indirect Prompt Injection & Cross-Tenant Leakage Paths:**
  * **Indirect Prompt Injection / RCE Path:** Malicious code embedded within a compromised Hugging Face pickle model file executes automatically during deserialization (`pickle.load`), granting full read/write/execution access to the container environment.
  * **Cross-Tenant Leakage Path:** If multiple user inputs or files (`sample_data`) are processed sequentially within the same temporary directory or shared memory space without strict session scoping or data cleansing, sensitive genomics input payloads could leak between sessions.

---

## 2. Answers to the DataForge ML Lab Assignment Questions

### Question 1: Where did the content/model come from?
* **Source:** The model weights originate from an external Hugging Face repository (`logix-community/genomics-analyzer-v2`) via direct URL resolution (`https://huggingface.co/.../resolve/main/`).
* **Provenance Status:** In the original repository view, the model is flagged as an unverified account ("Account verified: No") with no checksum provided and an unknown license, making its supply chain untrusted.

### Question 2: What is trusted versus untrusted?
* **Trusted Components:**
  * Your own application source code infrastructure (the deployment repository and hardened Python loader scripts).
  * The container runtime environment before external fetches occur.
* **Untrusted Components:**
  * The external Hugging Face model repository (`logix-community`) and any third-party artifact publishers.
  * Legacy model file formats (`.pkl`) which allow arbitrary code execution.
  * Incoming patient data payloads (`sample_data`) if not properly schema-validated against injection or malformed payloads.

### Question 3: Where should trust boundaries exist?
* **The Ingestion Gateway Boundary:** A trust boundary must be established at the point where external model files are fetched. No external model file should be downloaded and executed blindly.
* **Deserialization / Security Gate Boundary:** Enforce mandatory pre-load security scanning (such as `picklescan`) to catch malicious opcodes before any processing occurs.
* **Format Enforcement Boundary:** Completely deprecate legacy execution formats like `pickle` in favor of declarative, safe tensor formats (such as `.safetensors`) where data structure parsing replaces code execution.
* **Runtime Isolation Boundary:** Isolate the model inference environment from core application secrets and system utilities using ephemeral containers or restricted-privilege sandboxes.
