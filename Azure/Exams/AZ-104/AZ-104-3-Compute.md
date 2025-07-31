https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator

| Idx | Focus Area                                  | Topics Covered                                                                |
| --- | ------------------------------------------- | ----------------------------------------------------------------------------- |
| 1   | **Manage Azure identities and governance**  | RBAC, dynamic groups, Entra ID roles, locks, policies, tags, subscriptions    |
| 2   | **Implement and manage storage**            | Storage tiers, blob vs file, immutability, lifecycle, AzCopy, identity access |
| 3   | **Deploy and manage Azure compute**         | VMs, VMSS, containers, App Services, backup, templates, Spot, Bastion         |
| 4   | **Implement and manage virtual networking** | NSGs, DNS, Private DNS, peering, VPN, Azure Bastion, Load Balancer            |
| 5   | **Monitor and maintain Azure resources**    | Azure Monitor, Alerts, Network Watcher, IP flow, Recovery Services Vault      |
| 6   | **Practice Exam & Gap Review**              | Take another official practice test + fix any weak spots                      |


# 1- Deploy and manage Azure compute
## 🔍 Big Picture : What AZ-104 Tests in Compute

| Skill Area                 | Examples from the exam                             |
| -------------------------- | -------------------------------------------------- |
| **Provision VMs**          | Sizes, images, regions, OS disk vs data disk       |
| **Configure VMs**          | Availability sets, zones, extensions               |
| **Automate deployments**   | ARM templates, cloud-init, Custom Script Ext.      |
| **Manage VMSS**            | Scaling rules, autoscale triggers                  |
| **Containers**             | Azure Container Instances (ACI), Azure App Service |
| **Serverless (Functions)** | Triggers, plans, scaling behavior                  |



### Availability set vs VM Scale Set

| Feature               | Scope                                       | Protects Against        | Used With                 |
| --------------------- | ------------------------------------------- | ----------------------- | ------------------------- |
| **Fault Domain**      | Single datacenter (rack)                    | Power/network failure   | Availability Set          |
| **Update Domain**     | Logical (Azure-wide)                        | Maintenance restarts    | Availability Set          |
| **Availability Set**  | Single datacenter                           | FD + UD coverage        | Individual VMs            |
| **Availability Zone** | Different **datacenters** in **one region** | Full datacenter failure | VMs, VMSS, Load Balancers |
| **Region Pair**       | Two Azure regions                           | Regional disasters, DR  | Storage, Backup, SQL      |




* Fault Domain (FD) = A rack of servers with shared power and network
* Update Domain (UD) = A logical group of VMs that get updated at the same time

* Availability Set = VMs spread across **update domains** and **fault domains** within one datacenter
* VM Scale Set = A bunch of cloned VMs that can scale, self-heal, and optionally run across zones for better availability.
 * ✅ Zone balancing ✅ Auto-scaling ✅ Uniform config for all VMs

| Feature              | **Availability Set**                              | **VM Scale Set**                           |
| -------------------- | ------------------------------------------------- | ------------------------------------------ |
| **Purpose**          | High availability (avoid single point of failure) | High availability **and auto-scaling**     |
| **VM Count**         | You create & manage individual VMs                | Azure creates & scales VMs automatically   |
| **Zones supported?** | ❌ Zone balancing not supported                    | ✅ Can auto-balance across **zones**        |
| **Scaling?**         | ❌ Manual only                                     | ✅ Auto-scale rules (CPU %, schedule, etc.) |
| **Used for?**        | Static VM clusters (e.g., domain controllers)     | Web servers, stateless workloads           |


| Concept             | Protects From                             | Applies To        |
| ------------------- | ----------------------------------------- | ----------------- |
| **Fault Domain** ✅  | Physical rack failure (e.g. power outage) | Availability Sets |
| **Update Domain** ❌ | Azure maintenance reboots only            | Availability Sets |

🧠 So:
 * If the rack loses power → Fault Domain saves you
 * If Azure restarts a VM for patching → Update Domain saves you (Azure groups your VMs into Update Domains And updates one UD at a time)

### Provision VMs

#### VM Size:
When you create a VM, one of the first things you select is the size. This determines: vCPU count, RAM, Network throughput, Max disk IOPS, GPU support (if any)

| Family        | Optimized For       | 💸 Cost          | Mnemonic to Remember 🧠                                | Example Sizes     |
| ------------- | ------------------- | ---------------- | ------------------------------------------------------ | ----------------- |
| **B-series**  | Burstable workloads | 💸 Very cheap    | **B** = **Budget**, **Burstable**, **Basic**           | B2s, B4ms         |
| **D-series**  | General-purpose     | 💵 Medium        | **D** = **Default**, your go-to for general use        | D2s v5, D4as v5   |
| **E-series**  | Memory-optimized    | 💵💵 High        | **E** = **Extended RAM** (E for **Elastic**)           |  E4s v3, E8as v5   |
| **F-series**  | Compute-optimized   | 💵💵 High        | **F** = **Fast CPU**, **Focused** compute tasks        | F4s v2, F8s v2    |
| **N-series**  | GPU-intensive       | 💰💰💰 Very high | **N** = **NVIDIA**, **Neural nets**, **Neon graphics** | NC, ND, NV-series |
| **Ls-series** | Storage throughput  | 💵💵 Med–High    | **L** = **Large disks**, **Log-heavy workloads**       |L8s v3            |


#### Disk Types

| Disk Type        | Purpose                 | Attached to                                      |
| ---------------- | ----------------------- | ------------------------------------------------ |
| **OS Disk**      | Boot + system files     | Every VM                                         |
| **Data Disk(s)** | App + user data storage | Optional                                         |
| **Temp Disk**    | Short-term swap space   | Included by default on many VMs (e.g., D-series) |


Managed Disk SKUs

| Disk Tier        | Hardware      | Use Case                      | 💸 Cost | Supports ZRS?   | 🧠 Mnemonic                                                |
| ---------------- | ------------- | ----------------------------- | ---------| ------------------ | ---------------------------------------------------------- |
| **Standard HDD** | Spinning disk | Cheapest, low IOPS            | 💸 Lowest | ❌ No | **H** = **"Horribly slow but Helpful for cheap dev/test"** |
| **Standard SSD** | Basic SSD     | Moderate performance, general | 💸💸      | ✅ Some regions| **S** = **"Simple SSD for Stable workloads"**              |
| **Premium SSD**  | Fast SSD      | High IOPS, prod databases     | 💸💸💸    | ✅ Some regions| **P** = **"Power SSD for Production & Performance"**       |
| **Ultra Disk**   | High-perf SSD | Custom-tuned IOPS and throughput, massive DBs     | 💰💰💰💰  | ❌ No (zone pinned)| **U** = **"Ultimate speed — Unleashed power"**             |

 
(IOPS: Input Output Operations Per Second) 

🧠 Think:
 * HDD = slow but cheap
 * Standard SSD = better, still economical
 * Premium SSD = IOPS-focused
 * Ultra = top-tier for DBs with high perf needs

#### VM Images and provisioning options
 What is a VM Image? An image is the base OS and optional pre-installed software used to create a VM.

🧱 Common Image Types
| Type                     | Description                                                 | Mnemonic 🧠                      |
| ------------------------ | ----------------------------------------------------------- | -------------------------------- |
| **Platform image**       | Built-in by Azure (Windows, Linux, SQL Server, etc.)        | “Out-of-the-box” default         |
| **Custom image**         | Your own prepared VM image (e.g., with apps/config)         | “Cloned golden template”         |
| **Shared Image Gallery** | Image stored in a central repo, shared across subscriptions | “Company-wide shared library”    |
| **Marketplace image**    | 3rd-party image (e.g., firewall, SAP, WordPress, etc.)      | “Purchased or specialized image” |


📦 VM Provisioning Options
| Option                     | Description                                     | When to Use 🧠                 |
| -------------------------- | ----------------------------------------------- | ------------------------------ |
| **Azure portal**           | Click-and-create, great for testing             | Manual setup or quick test     |
| **ARM template**           | JSON script, repeatable deployments             | IaC (Infra as Code)            |
| **Azure CLI / PowerShell** | Scripted, parameterized provisioning            | Automation + CI/CD pipelines   |
| **Image versioning (SIG)** | Pin version, test new ones, roll back if needed | Safe rollout of OS/app changes |


#### VM extensions & automation

**What are VM Extensions?** Little agents/scripts that run on a VM after it’s provisioned to customize or configure it.

🧱 Common VM Extensions
| Extension Name                 | What It Does                                          | Mnemonic 🧠                 |
| ------------------------------ | ----------------------------------------------------- | --------------------------- |
| **Custom Script**              | Runs any script (e.g., `.ps1`, `.sh`)                 | “Run once, do anything”     |
| **Desired State Config (DSC)** | Enforce config state (PowerShell-based)               | “Keep it always how I want it” |
| **Azure Monitor Agent**        | Collect logs/metrics (newer than Log Analytics agent) | “Monitor me”                |
| **Antimalware**                | Installs Microsoft Antimalware on Windows VMs         | “Defend me”                 |
| **Domain Join**                | Automatically joins a VM to AD domain                 | “Log me in to the domain”   |

###  Azure Automation Account 
    It’s a container for automation tools you can use to manage Azure or on-prem resources.


Features Inside an Automation Account:
| Feature                         | What It Does                                          | Mnemonic 🧠          |
| ------------------------------- | ----------------------------------------------------- | -------------------- |
| **Runbooks**                    | PowerShell or Python scripts (scheduled or triggered) | “Robot workers”      |
| **Update Management**           | Patches VMs, tracks compliance                        | “Patch police”       |
| **Inventory / Change Tracking** | Monitors software & file changes                      | “Watchdog for drift” |
| **Hybrid Worker**               | Run automation on-prem or non-Azure VMs               | “Remote robot hands” |

🧼 Update Management in Action: You need to automatically patch VMs on a schedule and ensure they’re compliant.
 * Scans VMs for missing updates (Windows/Linux)
 * Applies patches on schedule (e.g., 2 AM Sundays)
 * Tracks compliance in a dashboard

Example: You want to apply security updates monthly across all VMs and check which ones are non-compliant. -->  Automation Account + Update Management

To use Update Management:

 1. Create an Automation Account
 2. Enable Update Management
 3. Link your Log Analytics Workspace
 4. Add VMs to the patching schedule

 ###  VM Backup in Azure
Azure uses Recovery Services Vaults to automatically back up:
 * Entire VM (OS + data disks)
 * On a schedule (daily, weekly, etc.)
 * With retention (e.g., keep backups for 30 days or 1 year)

 You can:
  * Restore full VM
  * Or just individual files

| Concept                     | Description                                 | Mnemonic 🧠             |
| --------------------------- | ------------------------------------------- | ----------------------- |
| **Recovery Services Vault** | Backup storage container                    | “The backup box”        |
| **Backup Policy**           | Defines schedule + retention rules          | “The plan”              |
| **Snapshot**                | Quick VM state capture (temp, not a backup) | “Moment-in-time mirror” |
| **Geo-Redundant Storage**   | Backups stored in multiple regions          | “Disaster proof”        |



## 💡 Hints and tips
* SMB uses TCP port 445 — many corporate firewalls block it. That’s why sometimes file shares don’t work in secure environments without firewall rules.


* You don’t need to memorize syntax. What Microsoft wants is that you can: ✅ Recognize the purpose of a cmdlet
 * ❓ “Which PowerShell command creates a new virtual machine in Azure?” 
   And you’ll see options like: A. New-AzVM B. Set-AzVMExtension  C. Get-AzVM D. Remove-AzVM
    You just need to know:  New- → creates  Set- → configures   Get- → reads    Remove- → deletes

| Cmdlet              | Purpose                         | Tip to Remember            |
| ------------------- | ------------------------------- | -------------------------- |
| `New-AzVM`          | Create a new VM                 | "New" = Provision          |
| `Get-AzVM`          | View details of an existing VM  | "Get info"                 |
| `Remove-AzVM`       | Delete a VM                     | "Remove = Goodbye"         |
| `Set-AzVMExtension` | Add or configure a VM extension | "Set" = Stick on something |


| Cmdlet                     | Purpose                                | Tip to Remember                 |
| -------------------------- | -------------------------------------- | ------------------------------- |
| `New-AzStorageAccount`     | Create a new storage account           | Common trap: Not for containers |
| `New-AzStorageContainer`   | Create a blob container inside account | "Container = blob folder"       |
| `Set-AzStorageBlobContent` | Upload a file to blob storage          | Upload content (file)           |
| `Get-AzStorageBlob`        | Retrieve blob info                     | "Get = Read what's there"       |

| Cmdlet                            | Purpose                       | Tip to Remember            |
| --------------------------------- | ----------------------------- | -------------------------- |
| `New-AzVirtualNetwork`            | Create a new VNet             | Core networking            |
| `New-AzNetworkInterface`          | Create a NIC (needed for VMs) | Attaches VM to network     |
| `New-AzPublicIpAddress`           | Provision a public IP         | Needed for internet access |
| `New-AzNetworkSecurityGroup`      | Creates an NSG                | Use with subnets/NICs      |
| `New-AzNetworkSecurityRuleConfig` | Add rule to NSG               | Watch for priority/order   |

| Cmdlet                          | Purpose                              | Tip to Remember           |
| ------------------------------- | ------------------------------------ | ------------------------- |
| `New-AzResourceGroup`           | Create a new resource group          | Often first step          |
| `New-AzResourceGroupDeployment` | Deploy resources using ARM templates | Core for IaC              |
| `Remove-AzResourceGroup`        | Delete all resources in a group      | Destructive — be careful! |

| Cmdlet                 | Purpose                                   | Tip to Remember                     |
| ---------------------- | ----------------------------------------- | ----------------------------------- |
| `New-AzRoleAssignment` | Assign a role to a user/service principal | Tricky wording in questions         |
| `Get-AzADUser`         | View Entra ID user                        | Often combined with role assignment |
