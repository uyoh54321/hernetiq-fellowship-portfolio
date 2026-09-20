### Filter Enforcement: Application Layer vs Database Layer

The application-layer filter checks the tenant ID of the person making the request. It then retrieves the documents and removes any documents that do not belong to that tenant. This looks like the right fix because the user should only see information belonging to their own company.

The problem is that the filtering happens **after the database has already returned the information**. This becomes a security problem if someone is able to access the database without going through the application.

This was what I saw in the demo. The normal process was:

**User → Application → Vector Database**

But in the bypass, the attacker went directly to the database:

**Attacker → Vector Database**

Because the request did not go through the application, the application-level filter was never applied. The database returned information belonging to different companies. This showed that the filter was protecting the application, but it was not actually protecting the data at the database level.

The database-layer fix works differently. Instead of retrieving everything and filtering afterward, the tenant ID is included in the database search itself. The database only returns information that matches the tenant ID.

So even if someone tries to bypass the application and access the database directly, the database still applies the tenant restriction. The attacker cannot simply remove the protection by skipping the application.

### My Analogy

Imagine an office building where each company has its own room.

The application-layer filter is like a receptionist checking your company ID **after you have already been allowed into the building**. It can stop you from entering the wrong room, but if you find another way into the building without passing the receptionist, that protection is gone.

The database-layer fix is different. The doors to the rooms themselves are controlled by company ID. Even if you somehow enter the building without speaking to the receptionist, you still cannot open another company's room.

That is why the database-layer approach provides stronger protection: the restriction is enforced where the data actually lives, rather than depending only on the application.
