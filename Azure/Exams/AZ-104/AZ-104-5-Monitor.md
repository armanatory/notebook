https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator

| Idx | Focus Area                                  | Topics Covered                                                                |
| --- | ------------------------------------------- | ----------------------------------------------------------------------------- |
| 1   | **Manage Azure identities and governance**  | RBAC, dynamic groups, Entra ID roles, locks, policies, tags, subscriptions    |
| 2   | **Implement and manage storage**            | Storage tiers, blob vs file, immutability, lifecycle, AzCopy, identity access |
| 3   | **Deploy and manage Azure compute**         | VMs, VMSS, containers, App Services, backup, templates, Spot, Bastion         |
| 4   | **Implement and manage virtual networking** | NSGs, DNS, Private DNS, peering, VPN, Azure Bastion, Load Balancer            |
| 5   | **Monitor and maintain Azure resources**    | Azure Monitor, Alerts, Network Watcher, IP flow, Recovery Services Vault      |
| 6   | **Practice Exam & Gap Review**              | Take another official practice test + fix any weak spots                      |


# 5- Monitor and maintain Azure resources
## 🔍 Big Picture
| Topic                         | Purpose                                                  | Think of it as...                       |
| ----------------------------- | -------------------------------------------------------- | --------------------------------------- |
| **Azure Monitor**             | Central hub for performance & health data                | The *nerve center*                      |
| **Metrics**                   | Numeric data points (CPU %, Disk IO, etc.)               | Like a fitness tracker                  |
| **Logs**                      | Detailed, queryable records (diagnostics, activity logs) | Like a flight recorder (black box)      |
| **Alerts**                    | Triggered when conditions are met                        | Like setting up a heart rate warning    |
| **Action Groups**             | What to do when an alert fires                           | "Call this number" or "Run this script" |
| **Log Analytics Workspace**   | Where log data is stored & queried                       | Like a giant searchable notebook        |
| **Azure Advisor**             | Gives cost/performance/security tips                     | Your *cloud consultant*                 |
| **VM Insights, App Insights** | Monitoring for specific services                         | Health scans for VMs/apps               |
| **Backup and Recovery**       | Protect data in case of failure                          | Insurance for your cloud stuff          |


Azure Monitor vs Log Analytics vs Azure Advisor
| Feature           | What It Is                                                                     | Think Of It As         | Key Use Cases                                             |
| ----------------- | ------------------------------------------------------------------------------ | ---------------------- | --------------------------------------------------------- |
| **Azure Monitor** | The **umbrella platform** that collects & organizes telemetry (metrics + logs) | Central dashboard      | Connect everything here (alerts, metrics, logs, insights) |
| **Log Analytics** | A **tool inside Monitor** for querying logs with KQL                           | Search engine for logs | Investigate slowdowns, failures, or deep diagnostics      |
| **Azure Advisor** | A **recommendation engine** (outside Monitor) that suggests best practices     | Cloud consultant       | Tips for cost savings, reliability, performance, security |



###  Azure Monitor
* Umbrella service for collecting, analyzing, and acting on telemetry.
* Sends data to Log Analytics, Metrics Explorer, and Alerts.

* **AMA** = Azure Monitoring Agent = Metrics + Logs + Multi-homing
 * Azure Monitor Agent (AMA) must be installed on the target VM or VMSS.
 * Azure can install it automatically during onboarding
 * You must define a Data Collection Rule (DCR).
 * DCR = instructions for what data to collect, from which resource, and where to send it.





### Log Analytics
* Workspace where log data (like VM diagnostics, app logs) is stored.
* Uses Kusto Query Language (KQL) for custom log queries.
* Backed by a Log Analytics Workspace.

### Azure Advisor: Recommendation engine.
* Gives best practices across reliability, security, performance, cost.
* Not for real-time monitoring — more like ongoing hygiene guidance.

### Azure Alerts & Actions

| Concept          | What It Is                                          | Example Use                               | Key Exam Points                                                 |
| ---------------- | --------------------------------------------------- | ----------------------------------------- | --------------------------------------------------------------- |
| **Alert Rule**   | A rule that checks for a condition (e.g. CPU > 80%) | Trigger when VM CPU spikes                | Always requires a signal source (metric or log)                 |
| **Signal**       | What the alert listens to — metric, log, activity   | Metric = CPU, Log = Error events          | **Metrics** = fast/simple; **Logs** = detailed/filterable       |
| **Action Group** | What happens when an alert fires                    | Email admin, call webhook, run automation | Can trigger multiple actions: email, Logic App, ITSM            |
| **Severity**     | Alert importance (0 = critical, 4 = info)           | Severity 1 = urgent production incident   | Often a required config                                         |
| **Alert Scope**  | What the alert is monitoring                        | A VM, a resource group, or a subscription | Choose scope carefully — affects permissions and signal sources |



* Fires when metric or log condition is met.
* Works with action groups to notify or automate responses (email, Logic App, etc.).
* Two types: Metric alerts (near real-time) vs Log alerts (based on query results, can be delayed).

Metrics vs Logs
| Feature        | **Metrics**                         | **Logs**                                      |
| -------------- | ----------------------------------- | --------------------------------------------- |
| Type           | Numeric, time-series data           | Text-based, structured data                   |
| Example        | CPU %, memory usage, IOPS           | Failed login, app crash, policy violation     |
| Collection     | Collected automatically by platform | Must be sent to a **Log Analytics Workspace** |
| Retention      | Short by default (e.g. 93 days)     | Long-term (configurable)                      |
| Query Language | Filters only                        | Uses **Kusto Query Language (KQL)**           |
| Performance    | Real-time, lightweight              | Slower, more detailed                         |
| Alerting       | Great for thresholds                | Great for pattern/match logic                 |
| Storage        | Azure Monitor Metrics DB            | Log Analytics Workspace                       |



* **Latency**: Metric alerts = 1-min latency; Log alerts = ~5+ min delay
* Only Metric Alerts have built-in resolve behavior.
* This is not the case for Log Alerts unless manually handled.

Per Action Group, not per alert:
| Channel        | Limit                                              |
| -------------- | -------------------------------------------------- |
| **Email**      | \~100 emails/hour per action group                 |
| **SMS**        | **1 SMS per 5 minutes**, max **6 per hour**        |
| **Voice Call** | **1 voice call per 5 minutes**, max **2 per hour** |

🧩 **Diagnostic Settings**: Let you export telemetry to destinations like: Log Analytics (for querying), Storage Account (for archiving), Event Hub (for streaming); and you can set them on VMs, AppServices, KVs, AzureSQLs, etc. 
 * Without Diagnostic Settings, many logs won't be collected.


### Backup Vaults & Recovery Services Vaults

| Feature                       | **Backup Vault**                                       | **Recovery Services Vault**                         |
| ----------------------------- | ------------------------------------------------------ | --------------------------------------------------- |
| **Use Case**                  | Modern workloads, Azure Files, blobs, SQL in Azure VMs | Older workloads, classic VM backup, DPM, MARS agent |
| **Best for**                  | Newer resources and Azure-native workloads             | Hybrid/on-prem backups, legacy systems              |
| **Backup Center Support**     | ✅ Full integration                                     | ✅ Supported                                         |
| **Resource Type**             | First-class ARM resource                               | First-class ARM resource                            |
| **VM Support**                | Yes                                                    | Yes                                                 |
| **Azure Files & Blob Backup** | ✅ Native support                                       | ❌ Not directly supported                            |
| **Availability Zones**        | ✅ ZRS support                                          | ❌ Typically LRS only                                |
| **Encryption**                | Platform-managed and customer-managed keys supported   | Same                                                |
| **Management Experience**     | Recommended for **new deployments**                    | Still used but being phased out slowly              |
| **Agent Compatibility**       | Azure Backup agent (MARS) not supported                | ✅ Supported                                         |


“B is for Blob & modern.”
Use Backup Vault for modern services like Azure Files, Blobs, SQL in VMs.
Use Recovery Services Vault when working with on-prem, legacy agents, or older tooling.


* **Recovery Services Vault**: For VM backups, Azure Files, SQL.
* **Backup Vault** (newer): For newer workloads, especially Azure Files, blobs.
* **Restore Point**: Used with restore point collections for VM restore — faster and localized.
* **Azure Site Recovery**: DR (DR = Disaster Recovery )tool for full replication — not just backup.
 * Site Recovery = replication, Backup = restore  
  

### Update Management
| Feature                     | Description                                                                        |
| --------------------------- | ---------------------------------------------------------------------------------- |
| **Update Management**       | Feature in **Azure Automation** that tracks patch compliance and schedules updates |
| **Log Analytics Workspace** | Required to store update data                                                      |
| **Automation Account**      | Executes update jobs                                                               |
| **Target Systems**          | Azure VMs or hybrid servers (Windows/Linux)                                        |
| **Schedules**               | You define when updates are applied                                                |

🔧 How It Works – Step by Step: Enable Update Management -> Link VM to Automation + Log Analytics -> Define a schedule

* Update Management is not enabled by default, and Log Analytics + Automation Account must be connected.
 

### Azure Site Recovery (ASR) - “ASR = Always Stand Ready”
Azure Site Recovery (ASR) is a disaster recovery solution. It keeps your VMs, physical servers, or workloads replicated and ready to fail over to another region (or to Azure from on-prem), in case your primary site goes down. **🔄 It’s business continuity, not backup.**

🧠 Key Differences: ASR vs Azure Backup
| Feature           | **Azure Site Recovery (ASR)**  | **Azure Backup**             |
| ----------------- | ------------------------------ | ---------------------------- |
| **Goal**          | Keep VM running during outages | Restore files/VMs after loss |
| **Recovery Time** | Minutes                        | Hours                        |
| **Replication**   | Continuous                     | Scheduled (e.g., daily)      |
| **Use Case**      | Disaster recovery              | Backup/restore               |
| **Failover?**     | ✅ Yes                          | ❌ No                         |
| **Costs**         | Ongoing replication + storage  | Pay for vault and storage    |

1. You configure replication on a VM.
2. ASR replicates the disk continuously to a target region.
3. During disaster, trigger a failover (manually or automated).
4. VM spins up in target region → users stay online.
5. After fix, trigger failback.


🔁 Backup vs ASR?
| If you need to restore something that was deleted/lost → use Backup
| If you need to keep running during region outages → use ASR


### Azure Service Health

| Feature             | Purpose                                                                          |
| ------------------- | -------------------------------------------------------------------------------- |
| **Service Health**  | See *current* issues (e.g., outages) affecting **Azure services in your region** |
| **Resource Health** | See if a **specific resource** (VM, storage, etc.) is **healthy or degraded**    |
| **Health Alerts**   | Get notified when services or regions are impacted                               |

    🧠 Think:
      * Service Health = Azure-wide
      * Resource Health = Your stuff



### 💡Hints

