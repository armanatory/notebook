https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator

| Idx | Focus Area                                  | Topics Covered                                                                |
| --- | ------------------------------------------- | ----------------------------------------------------------------------------- |
| 1   | **Manage Azure identities and governance**  | RBAC, dynamic groups, Entra ID roles, locks, policies, tags, subscriptions    |
| 2   | **Implement and manage storage**            | Storage tiers, blob vs file, immutability, lifecycle, AzCopy, identity access |
| 3   | **Deploy and manage Azure compute**         | VMs, VMSS, containers, App Services, backup, templates, Spot, Bastion         |
| 4   | **Implement and manage virtual networking** | NSGs, DNS, Private DNS, peering, VPN, Azure Bastion, Load Balancer            |
| 5   | **Monitor and maintain Azure resources**    | Azure Monitor, Alerts, Network Watcher, IP flow, Recovery Services Vault      |
| 6   | **Practice Exam & Gap Review**              | Take another official practice test + fix any weak spots                      |


# 1- Manage Azure identities and governance
## 🔍 Big Picture
Think of Azure identities and governance as the “**who, what, and why**” of your cloud environment:

* **Who** gets access? ➝ Microsoft Entra ID (users, groups, roles)
* **What** can they do? ➝ RBAC (roles like Reader, Contributor, custom roles)
* **Why** are things done this way? ➝ Governance tools (locks, tags, policies, Blueprints)

🧠 Analogy Time:
Imagine you're running a hotel:

* Entra ID = guest list + staff directory
* RBAC = what rooms they can open
* Tags = color-coded keycards for billing
* Locks = “Do Not Disturb” signs (you can’t delete or change this resource!)
* Azure Policy = hotel rules (e.g., no smoking, all rooms must have a fire alarm)

## 🎯 Deeper Dive

### Azure Locks —> "Do Not Disturb" for Resources
Locks are extra protection on top of RBAC —> kind of like "no matter who you are or what your role says, you still can’t delete this."
| Lock Type      | What it Does                                  | Example Use Case                                | Allowed | Denied
| -------------- | --------------------------------------------- | ----------------------------------------------- |-------- | ------
| `CanNotDelete` | Users can read and modify, **but not delete** | Protect a storage account from deletion         | Read, Write |Delete
| `ReadOnly`     | Users can **only view**, not change or delete | Lock critical settings like VNet configurations | Read | Write, Delete

* Azure Policy can prevent certain actions like creating resources without tags, or enforcing location, but doesn’t block deletes by default.
* The lock overrides RBAC when it comes to denying changes.
  
### Azure Policy
While RBAC controls who can do things, and locks control whether you can change or delete, Azure Policy controls what can exist and how it's configured.

🎯 Core Idea: Enforce consistency across resources.
Think of it like this:

    Policy = guardrails
    "Every VM must be in West Europe",
    "All resources must have a tag Environment",
    "No public IPs allowed"

The effect is what Azure does when the policy condition is met.

| Effect   | What It Does                            | When to Use                                       |
| -------- | --------------------------------------- | ------------------------------------------------- |
| `Deny`   | Blocks the request entirely             | You want to **stop** something from being created |
| `Audit`  | Logs the violation but allows it        | You want to **monitor** without enforcement       |
| `Append` | Adds extra settings/tags to the request | You want to **enrich** requests                   |
| `Modify` | Alters the request before it's allowed  | You want to **fix config automatically**          |


### Tags
Tags are for organization and cost control, allowing groupping and such in reports.

* Tags are not inherited automatically from RGs or subs
* You can apply tags at any level: resource, RG, subscription


### Administrative Units – Delegating Management
This is often confused with groups, but it's a different tool in Entra ID.
Administrative Units (AUs) are like mini-Entra tenants. You use them to delegate user/group management to specific admins — without giving them control over the whole directory.

### Azure Blueprints – "Starter Kits for Governance"
**Blueprints are like pre-packed kits for building a compliant environment.**

They let you bundle together: ARM templates (resources), RBAC role assignments, Policy assignments, and Resource groups and deploy them as one unit, across multiple subscriptions.

* Blueprint definitions live in a management group or subscription.
* When you apply it = Blueprint assignment
* You can lock resources in a Blueprint assignment (e.g., deny deletion).
* Blueprints are best for repeatable governance, especially in multi-sub environments.

### Resource Groups and Subscription Scopes
🔑 RBAC and policy scope matters:
| Scope Level      | Example                        |
| ---------------- | ------------------------------ |
| Management Group | "All production subscriptions" |
| Subscription     | "Contoso Dev Subscription"     |
| Resource Group   | "RG-Finance"                   |
| Resource         | "VM-WebApp01"                  |

Apply broad policies at the subscription level, and specific RBAC at the resource group/resource level for least privilege. Azure Policy (not RBAC) is often best applied at broader scopes, like the subscription or management group, to ensure consistent enforcement.

### PIM – Privileged Identity Management
PIM is all about temporary, just-in-time elevation of rights.  You don't want someone to have Global Admin 24/7, just for one emergency task.

So with PIM:

* You assign eligible roles (not active all the time)
* The user activates the role when needed
* You can require MFA, approval, and track audit logs

### 💡Hints

