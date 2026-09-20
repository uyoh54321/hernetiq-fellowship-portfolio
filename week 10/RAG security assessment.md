### 1. Poisoning Vectors

**Retrieval-time poisoning**

* **What the attacker does:** The attacker puts a fake or changed invoice into PayGuard's information system.
* **What the user experiences:** PayGuard uses that false information and gives the customer the wrong answer about their invoice or payment.
* **What the security team would notice:** They may find an unapproved document or suspicious content that should not have been added to the system.

**Training-time poisoning**

* **What the attacker does:** The attacker gets false financial information into the data used to train the AI.
* **What the user experiences:** The AI keeps giving the wrong financial advice, even when the correct information is available.
* **What the security team would notice:** They find suspicious information in the training data or discover that the source of some of the data cannot be trusted.

### 2. Embedding Model Audit

I would **not approve the model immediately**.

Before using it for PayGuard, I would check five things:

1. Who created the model, and can we trust them?
2. Where did the information used to train it come from?
3. Has the model been properly tested?
4. Was it actually designed for this type of financial information?
5. Is the model still being maintained and updated?

If these checks give me enough confidence in the model, then I would approve it. The main point is that a model can be safe to install but still give poor or unreliable results.

### 3. Filter Enforcement Gap

The filter looks like it is working because the application checks the customer's company before showing the information.

The problem is that the database has already brought back information from different companies **before the filter is applied**.

So, if an attacker finds a way to access the database directly and bypasses the application, they can see information belonging to other companies.

The attacker therefore needs a way to **bypass the application and access the database directly**.

### 4. Recommendations

1. **Only allow trusted sources to submit documents.**
   PayGuard should know where each document came from and reject documents from unknown sources.

2. **Check documents before adding them to the system.**
   Look for suspicious instructions or other signs that someone may have tampered with the document. Anything suspicious should be checked before it is added.

3. **Make the database separate each company's information.**
   The database itself should make sure Company A can only get Company A's information. Don't depend only on the application to remove the wrong information afterward.
