https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator

| Idx | Focus Area                                  | Topics Covered                                                                |
| --- | ------------------------------------------- | ----------------------------------------------------------------------------- |
| 1   | **Manage Azure identities and governance**  | RBAC, dynamic groups, Entra ID roles, locks, policies, tags, subscriptions    |
| 2   | **Implement and manage storage**            | Storage tiers, blob vs file, immutability, lifecycle, AzCopy, identity access |
| 3   | **Deploy and manage Azure compute**         | VMs, VMSS, containers, App Services, backup, templates, Spot, Bastion         |
| 4   | **Implement and manage virtual networking** | NSGs, DNS, Private DNS, peering, VPN, Azure Bastion, Load Balancer            |
| 5   | **Monitor and maintain Azure resources**    | Azure Monitor, Alerts, Network Watcher, IP flow, Recovery Services Vault      |
| 6   | **Practice Exam & Gap Review**              | Take another official practice test + fix any weak spots                      |


# 1- Implement and manage storage
## 🔍 Big Picture

Azure Storage isn’t just blobs. Here’s the lay of the land:
| Storage Type | Description                                     | Use Case                       |
| ------------ | ----------------------------------------------- | ------------------------------ |
| **Blob**     | Unstructured data (text, images, backups, etc.) | App data, backups, media, logs |
| **File**     | SMB protocol shares                             | Lift-and-shift, shared drives  |
| **Table**    | NoSQL key-value store                           | Metadata, app configs          |
| **Queue**    | Message storage (FIFO)                          | Decoupling apps                |
| **Disk**     | Attached to VMs                                 | Persistent VM storage          |

🎯 Key Topics for the Exam:

* Storage tiers (Hot, Cool, Archive)
* Immutability & Retention
* Lifecycle Management
* Identity-based access (Entra ID, RBAC vs Keys)
* AzCopy basics
* Replication types (LRS, GRS, ZRS, etc.)


SMB = Server Message Block, It’s a Windows-based protocol that allows: File sharing Folder access Network printing


#### Blob vs File:

* Blob: Like storing photos in Google Drive — you upload and download, but you don’t “mount” the folder.

* File: Like a company network drive — you can open it in Windows Explorer as Z:\SharedDocs.

| Feature               | **Blob Storage**                       | **File Storage (Azure Files)**               |
| --------------------- | -------------------------------------- | -------------------------------------------- |
| Protocol              | **HTTP/HTTPS**, REST API, AzCopy       | **SMB (Server Message Block)**               |
| Structure             | Flat: containers → blobs               | Hierarchical: shares → folders → files       |
| Access Style          | App-driven (like object storage on S3) | Like a network drive (Windows File Share)    |
| Use Cases             | Backups, media, logs, big data         | Shared folders, lift-and-shift legacy apps   |
| Mountable             | ❌ (not directly by OS)                 | ✅ (mount as drive letter on Windows/Linux)   |
| Identity-based access | Yes (RBAC, SAS, etc.)                  | Yes (via Entra ID for domain-joined clients) |


### Storage Tiers 

You choose a tier per blob or per container (default). Pricing varies by: 

* Storage cost (cheap in Archive)
* Access cost (expensive in Archive)

| Tier        | Storage Cost 💸 | Access Cost 📈 | Intended Use                          | Access Speed |
| ----------- | --------------- | -------------- | ------------------------------------- | ---          |
| **Hot**     | High            | Low            | Frequently accessed                   | Milliseconds |
| **Cool**    | Medium          | Medium         | Infrequently accessed (30+ days)      | Milliseconds |
| **Archive** | Very low        | Very high      | Rarely accessed (180+ days retention) | Hours (up to 15 hrs!) |


### Immutability & Retention in Azure Blob Storage

    Sometimes you need to freeze your data — for compliance, legal, or safety reasons

Azure Blob Storage supports Immutability Policies, which prevent modification or deletion of data for a specified period.

| Type                     | What It Means                                | Use Case                            |
| ------------------------ | -------------------------------------------- | ----------------------------------- |
| **Time-based retention** | Locks data for a fixed duration (`x` days)   | Financial records, audits           |
| **Legal hold**           | Locks data until you **manually release** it | Ongoing litigation or investigation |

Set on containers

Can be Append Blobs only for certain scenarios (e.g., logging)

Enforced at the storage layer — even Owner can’t delete it

🧠 Think: "Once set, even God can’t delete this until the timer ends."

❗Related Concept: *Version-level immutability* -> You can apply immutability per version of a blob — this gives even finer control. Especially useful when paired with Blob Versioning + Change Feed.

🎯 Common Exam Triggers:
* "Prevent data from being deleted or modified for 6 months" → Use immutability policy
* "Prevent deletion only, but allow updates" → ❌ Not possible — use RBAC or lock instead
* "Comply with legal investigation and hold data" → Legal hold


| Feature                       | Blocks Delete | Blocks Modify | Time-based | Manual Release |
| ----------------------------- | ------------- | ------------- | ---------- | -------------- |
| **Immutability (Time-based)** | ✅ Yes         | ✅ Yes         | ✅ Yes      | ❌ No           |
| **Legal Hold**                | ✅ Yes         | ✅ Yes         | ❌ No       | ✅ Yes          |
| **Locks (`CanNotDelete`)**    | ✅ Yes         | ❌ No          | ❌ No       | ✅ Yes          |
| **Azure Policy**              | ❌ No          | ❌ No          | ❌ No       | N/A            |


### Azure Blob Lifecycle Management – "Automatic Clean-Up Crew": M.A.D

    You define rules that move or delete blobs based on how old or unused they are.

Lifecycle policies let you:

 * Move blobs to Cool or Archive storage
 * Delete blobs after a set number of days
 * Apply rules based on: Last modified date | Last access date (optional)

 M.A.D.: Move Archive Delete


⚙️ How to Use It (Exam-level detail):
 * Enable last access tracking (if using last accessed instead of last modified) 🟡 This costs extra
 * Define a rule in JSON or portal
 * Apply it to containers or filter by blob prefix/tags

❗If the rule depends on when a blob was last read, but access tracking is not enabled → the rule won't work!*

### Identity-Based Access vs SAS vs Keys
| Method                            | Best For                          | Identity-based? | Can be time-limited? | Granular? | Pros | Cons
| --------------------------------- | --------------------------------- | --------------- | -------------------- | --------- | ---- |----
| **RBAC (Azure AD)**               | Apps, users, automation pipelines | ✅ Yes           | ✅ Yes (via PIM etc.) | ✅ Yes     | Secure, auditable, no secret token, least privilege | Only works with supported tools (AzCopy, SDKs, managed VMs)
| **Shared Access Signature (SAS)** | Temporary public/shared access    | ❌ No            | ✅ Yes                | ✅ Yes     | Can be account-level, service level, or user delegation, timed|
| **Account Keys**                  | Admin/root-level access           | ❌ No            | ❌ No                 | ❌ No      | Full control over the account — like a master password|

* User Delegation SAS is a type of SAS that uses Entra ID + RBAC under the hood. It’s perfect for scenarios like:
 * Apps that authenticate via Entra ID (e.g., login via token)
 * Need to limit access to one container for a short time
 * Avoid using account keys

#### SAS Tokens vs Stored Access Policy

* *SAS*: A URL with a query string that grants access to Azure Storage. Has Expiry time, Allowed operations (read, write, delete), IP ranges, and Protocols (https only)
* *Stored Access Policy?* It's a named template for SAS settings — stored on the container itself.

Why Use Stored Access Policies? You can revoke or rotate access without regenerating all SAS tokens, and Define reusable permissions and durations


### AzCopy – Your Command-Line Power Tool
    AzCopy is a fast CLI tool for copying data to/from Azure Storage.

What Can It Do? Upload/download blobs, files | Copy between: On-prem ↔ Azure, Azure ↔ Azure (even across regions)
    azcopy copy <source> <destination> [options]
✅ Works with: SAS tokens, Entra ID login (RBAC) (azcopy login), Access keys

| Feature                      | AzCopy Support             |
| ---------------------------- | -------------------------- |
| Copy blobs?                  | ✅ Yes                      |
| Copy file shares?            | ✅ Yes                      |
| Copy queues or tables?       | ❌ No                       |
| Identity-based auth (RBAC)?  | ✅ Yes                      |
| Works in restricted network? | ❌ Not unless port 443 open |


### Replication – Data Redundancy Models
    Azure Storage keeps multiple copies of your data — depending on the redundancy option you pick.

| Option     | Copies | Across Regions?                | Use Case                          | Notes
| ---------- | ------ | ------------------------------ | --------------------------------- | ---
| **LRS**    | 3      | ❌ Local only                   | Cost-effective, single-datacenter | 
| **ZRS**    | 3+     | ❌ Same region, different zones | High availability within region   |
| **GRS**    | 6      | ✅ Cross-region                 | Disaster recovery                 | 	Writes in primary, async copy to secondary
| **GZRS**   | 6+     | ✅ Zone + region                | Best of ZRS + GRS                 |
| **RA-GRS** | 6      | ✅ + Read-only replica          | Read access even if region down   | GRS + read access to secondary

🧠 LZ-GRGZ: Local, Zonal, Global, Global-Zonal -> (Increasing cost & durability)


❗ Exam Triggers:
 * "Comply with cross-region backup requirement" → GRS/GZRS
 * "Zone failure protection, no need for geo" → ZRS
 * "Cheapest for dev/test" → LRS
 * "App must read from secondary region" → RA-GRS
 * ZRS stores data in 3 different AZs

🧠 Region = A geographic location containing one or more datacenters.
 Examples: West Europe (Netherlands), North Europe (Ireland)


🧠 Zones = isolation within a region
    A physically separate datacenter within a single Azure region.
Each region that supports AZs has at least 3 zones, each with: Independent power, Cooling, Networking

🧠 Geo (Geography) = A broader data residency boundary, often for legal or compliance reasons.
 Example: EU vs US vs Asia Pacific, West Europe + North Europe = Europe geo
 * Data replication across regions (like GRS) stays inside a geo when possible (e.g., West Europe → North Europe)

 | **Scenario**                                                                 | **Choose**      | **Why**                                                                |
| ---------------------------------------------------------------------------- | --------------- | ---------------------------------------------------------------------- |
| “Survive **local hardware failure** (disk/server)”                           | **LRS**         | 3 copies in the **same** datacenter — protects against hardware faults |
| “Survive **datacenter/zone failure** (fire, outage in 1 zone)”               | **ZRS**         | 3 copies across **availability zones** in one region                   |
| “Survive full **region outage** (e.g., Amsterdam down, fail over to Dublin)” | **GRS**         | Primary region + **async geo copy** to paired region (e.g., Ireland)   |
| “Survive region outage + **read from secondary**”                            | **RA-GRS**      | Same as GRS, but adds **read access** to secondary                     |
| “Survive zone + region failure (best durability, no read access)”            | **GZRS**        | Combines ZRS + GRS = zone + geo redundancy                             |
| “Survive zone + region failure + **read from secondary**”                    | **RA-GZRS**     | GZRS + **read access** — not available in all regions                  |
| “Use globally (e.g., serve US + EU users)”                                   | ❌ None of these | Use **CDN, Azure Front Door, or multi-region apps**, not replication   |




## 💡 Hints and tips
* SMB uses TCP port 445 — many corporate firewalls block it. That’s why sometimes file shares don’t work in secure environments without firewall rules.
