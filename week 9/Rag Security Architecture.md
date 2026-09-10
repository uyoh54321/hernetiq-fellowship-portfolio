# RAG Security Architecture & Threat Model

## Overview

This diagram illustrates the architecture of a Retrieval-Augmented Generation (RAG) system, showing both the knowledge ingestion pipeline and the user retrieval flow.

It also identifies three key security attack surfaces:

* **Knowledge Base Poisoning** — at the document injection point
* **Embedding Model Supply Chain Attack** — at the embedding model
* **Cross-Tenant Data Leakage** — at the vector database

## RAG Architecture

```text
                         KNOWLEDGE INGESTION FLOW

┌──────────────┐
│  Documents   │
└──────┬───────┘
       │
       │
       │  ⚠ KNOWLEDGE BASE POISONING
       │    Malicious document injection
       ▼
┌──────────────┐
│   Chunking   │
└──────┬───────┘
       │
       ▼
┌──────────────────────┐
│   Embedding Model    │
│                      │
│ ⚠ EMBEDDING MODEL    │
│   SUPPLY CHAIN ATTACK│
└──────────┬───────────┘
           │
           ▼
┌──────────────────────────┐
│    Vector Database       │
│                          │
│ ⚠ CROSS-TENANT DATA      │
│   LEAKAGE                │
└──────────────────────────┘


                         RETRIEVAL FLOW

┌─────────────────┐
│  User Question  │
└────────┬────────┘
         │
         ▼
┌──────────────────────┐
│   Embedding Model    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────────┐
│    Vector Database       │
│       Retrieval          │
└──────────┬───────────────┘
           │
           ▼
┌──────────────────────┐
│      AI Model        │
└──────────┬───────────┘
           │
           ▼
┌─────────────────┐
│    Response     │
└─────────────────┘
```

## Attack Surfaces

### 1. Knowledge Base Poisoning

**Attack surface:** Document injection point

An attacker may introduce malicious, misleading, or manipulated documents into the RAG knowledge base. When these documents are retrieved, their content can influence the context provided to the AI model and potentially manipulate its response.

**Security controls:**

* Validate and sanitize documents before ingestion
* Restrict who can upload or modify knowledge-base content
* Maintain document provenance and integrity checks
* Scan uploaded content for malicious or suspicious instructions
* Monitor changes to the knowledge base

### 2. Embedding Model Supply Chain Attack

**Attack surface:** Embedding Model

A compromised embedding model, model dependency, or model artifact could manipulate how documents and user questions are converted into vectors. This can affect retrieval accuracy and potentially cause malicious content to be preferentially retrieved.

**Security controls:**

* Use trusted model sources
* Verify model integrity and provenance
* Pin and validate model versions
* Scan dependencies and model artifacts
* Restrict model download and deployment permissions
* Monitor unexpected changes in embedding behaviour

### 3. Cross-Tenant Data Leakage

**Attack surface:** Vector Database

If multiple customers or tenants share a vector database, inadequate access controls or tenant isolation could allow one tenant's query to retrieve another tenant's documents or embeddings.

**Security controls:**

* Enforce tenant-level access control
* Apply metadata-based filtering to every retrieval request
* Validate authorization before querying the vector database
* Separate sensitive tenants where appropriate
* Test for cross-tenant retrieval
* Log and monitor unauthorized retrieval attempts

## Security Objective

The primary security objective is to ensure that:

> **Only trusted knowledge is ingested, trusted embedding infrastructure is used, and users can retrieve only the information they are authorized to access.**

This protects the RAG pipeline against **knowledge manipulation, model supply-chain compromise, and unauthorized data exposure**.

