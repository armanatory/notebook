https://learn.microsoft.com/en-us/credentials/certifications/azure-administrator

| Idx | Focus Area                                  | Topics Covered                                                                |
| --- | ------------------------------------------- | ----------------------------------------------------------------------------- |
| 1   | **Manage Azure identities and governance**  | RBAC, dynamic groups, Entra ID roles, locks, policies, tags, subscriptions    |
| 2   | **Implement and manage storage**            | Storage tiers, blob vs file, immutability, lifecycle, AzCopy, identity access |
| 3   | **Deploy and manage Azure compute**         | VMs, VMSS, containers, App Services, backup, templates, Spot, Bastion         |
| 4   | **Implement and manage virtual networking** | NSGs, DNS, Private DNS, peering, VPN, Azure Bastion, Load Balancer            |
| 5   | **Monitor and maintain Azure resources**    | Azure Monitor, Alerts, Network Watcher, IP flow, Recovery Services Vault      |
| 6   | **Practice Exam & Gap Review**              | Take another official practice test + fix any weak spots                      |


# 4 - Implement and Manage Virtual Networking
## 🔍 Big Picture :  
| Area                 | Know How To...                              |
| -------------------- | ------------------------------------------- |
| Create/Manage VNets  | Subnets, address spaces, route tables       |
| Control traffic      | NSGs, service tags, priorities              |
| Secure access        | Private Endpoints, firewalls, custom routes |
| Connect environments | VPN Gateway, ExpressRoute, VNet peering     |
| Balance traffic      | LB vs App Gateway vs Front Door             |


### 4-1. Virtual Networks (VNets)

🧠 Think: “Everything lives inside a VNet” A VNet is your own private network inside Azure — like a virtual version of your office LAN.

Key parts of a VNet:
| Concept           | What It Means                                                       | Example                      |
| ----------------- | ------------------------------------------------------------------- | ---------------------------- |
| **Address space** | IP range for the whole VNet (in CIDR format)                        | `10.0.0.0/16`                |
| **Subnets**       | Slices of the VNet for organizing resources                         | `10.0.1.0/24`, `10.0.2.0/24` |
| **Region**        | A VNet belongs to a single Azure region                             | West Europe                  |
| **Isolation**     | VNets don’t talk to each other unless you link them (e.g., peering) | Default = isolated           |

🎯 Why Use VNets?
 * Organize by tiers (frontend, backend, DB)
 * Control traffic between subnets
 * Apply NSGs, route tables, and other policies
 * Required for:  VM networking, Private Endpoints, App service with VNet integration

🔐 Subnet Gotchas: non-overlapping IP ranges,  can’t assign two subnets to the same IP block, VM NICs connect to subnets
🧠 Subnet = room in the building (VNet)

📦 VNet Peering (Preview): peer VNets for private, fast communication, works within or across regions.

### 4-2. IP Addressing & Routing
  Every device in a VNet — VM, load balancer, private endpoint — needs an IP address. Routing decides how traffic flows between them.

Core Concepts
| Term           | What it means                                                     | Example                                          |
| -------------- | ----------------------------------------------------------------- | ------------------------------------------------ |
| **CIDR block** | The IP range for your VNet or subnet                              | `10.0.0.0/16` for VNet, `10.0.1.0/24` for subnet |
| **Private IP** | Used for internal communication                                   | `10.x.x.x`, `192.168.x.x`                        |
| **Public IP**  | Needed for external access                                        | Azure assigns these optionally                   |
| **NIC**        | Every VM has a Network Interface Card, which gets an IP           | Think: “VM’s network plug”                       |
| **Dynamic IP** | Default — IP is assigned at boot, may change (unless you reserve) | Flexible but not permanent                       |
| **Static IP**  | Fixed — won’t change across VM reboots                            | Needed for DNS, whitelisting, etc.               |
CIDR: A CIDR (Classless Inter-Domain Routing) block is a way to represent a range of IP addresses using a compact notation.
[Network Prefix] / [Subnet Mask Length]
So in 10.0.0.0/16, 10.0.0.0 is the starting IP of the range /16 means the first 16 bits are fixed, and the rest can vary.
 So it includes: 10.0.0.1, 10.0.0.2, ..., all the way to 10.0.255.254

 | CIDR  | IP Range Size | Example IPs                 |Usable IPs|
| ----- | ------------- | --------------------------- | ---- |
| `/24` | 256 IPs       | `10.0.1.0 – 10.0.1.255`     | 254 |
| `/16` | 65,536 IPs    | `10.0.0.0 – 10.0.255.255`   ||
| `/8`  | 16 million+   | `10.0.0.0 – 10.255.255.255` ||


📡 Routing in Azure: 
 * Azure creates default system routes:
 * Traffic within a VNet = automatically routed
 * Traffic to internet = routed via Azure's internet gateway


Traffic to other VNets or on-prem = needs setup


### 4-3. NSGs & Security Rules
  Control inbound and outbound traffic at the subnet or NIC level. NSGs are like firewalls that live inside Azure.

| Scope      | Impact                                 |
| ---------- | -------------------------------------- |
| **Subnet** | Applies to **all resources** in subnet |
| **NIC**    | Applies to a **specific VM**           |


Each NSG has security rules: Priority (**lower = evaluated first**),  Direction: Inbound / Outbound, Source & Destination (can be IPs, subnets, or tags), Port (e.g., TCP 80), Protocol (TCP/UDP/Any), Action: Allow or Deny

* in case of a conflict between NSG of subnet and NSG of NIC, the NIC has the final say.

🏷️ Service Tags: Let you refer to groups without typing IPs.
| Service Tag                | What It Refers To                | Example Use                         |
| -------------------------- | -------------------------------- | ----------------------------------- |
| **VirtualNetwork**         | All traffic within the same VNet | Allow VM-to-VM communication        |
| **Internet**               | Traffic from outside Azure       | Allow/deny public access            |
| **AzureLoadBalancer**      | Probes from Azure’s LB           | Ensure health checks are allowed    |
| **AzureCloud**             | IPs used by Azure services       | When Azure needs to talk to your VM |
| **Storage**                | Azure Blob/File/Queue/etc.       | Allow secure storage access         |
| **Sql**                    | Azure SQL Database endpoints     | Allow DB connection to app VM       |
| **AppGateway**             | Azure Application Gateway        | When AGW needs to access backend    |
| **ApiManagement**          | Azure API Management             | For securing APIs behind NSGs       |
| **AzureTrafficManager**    | Global load balancing endpoints  | For failover routing                |
| **AzureFrontDoor.Backend** | Front Door to internal VMs       | Edge traffic to your app            |
🧠 Mnemonic: “VISA CAPS AA” V = VirtualNetwork I = Internet S = Storage  A = AzureCloud  C = AzureLoadBalancer  A = AppGateway  P = ApiManagement  S = Sql  A = AzureTrafficManager A = AzureFrontDoor




### 4-4. Connect Across Networks

| Concept               | What It Does                                          | Use Case                        | 💡 Memory Hook                 | ⏱️ Latency  | 💸 Cost                         |
| --------------------- | ----------------------------------------------------- | ------------------------------- | ------------------------------ | ----------- | ------------------------------- |
| **VNet Peering**      | Connects two Azure VNets (same or diff. regions)      | Intra-Azure communication       | 🫱🫲 “Peering = Private Pipe”  | ✅ Very low  | ✅ Low                           |
| **VPN Gateway**       | Encrypted tunnel via public internet                  | Azure ↔ On-premises             | 🔒 “VPN = Virtual Private Net” | 🚫 Higher   | ⚠️ Moderate setup + egress      |
| **Site-to-Site VPN**  | Persistent VPN between Azure & on-prem network        | Long-term hybrid infrastructure | 🏢➡️☁️ “Site = Stable Link”    | 🚫 Moderate | ⚠️ Moderate                     |
| **Point-to-Site VPN** | VPN from single client to Azure                       | Developer remote access         | 💻➡️☁️ “Point = Personal”      | 🚫 Higher   | ✅ Low per-user                  |
| **VNet-to-VNet VPN**  | Secure VPN between two Azure VNets                    | When peering not suitable       | 🛣️ “Two VNets, one tunnel”    | 🚫 Higher   | ⚠️ Gateway costs                |
| **ExpressRoute**      | Private, dedicated fiber to Azure (not over Internet) | Critical or regulated workloads | 🚄 “Fast Track to Azure”       | ✅ Ultra-low | ❌ Expensive setup & monthly fee |

💡 Quick Exam Triggers:
 * “High security, low latency” → ExpressRoute
 * “Encrypted over internet” → VPN Gateway
 * “Simple Azure-to-Azure link” → VNet Peering
 * “For a developer’s laptop” → Point-to-Site VPN



🔄 VNet Peering vs. VPN Gateway
| Feature            | VNet Peering             | VPN Gateway                  |
| ------------------ | ------------------------ | ---------------------------- |
| Latency            | Low (Microsoft backbone) | Higher (encrypted)           |
| Setup Time         | Seconds                  | Minutes                      |
| Cross-region?      | ✅ Yes                    | ✅ Yes                        |
| Costs              | Cheap per GB             | More costly (gateway + data) |
| Traffic encryption | ❌ No (private)           | ✅ Yes (IPSec tunnel)         |


### 4-5. Public Connectivity

| Feature                 | Purpose                                                                       | Common Use                                          |
| ----------------------- | ----------------------------------------------------------------------------- | --------------------------------------------------- |
| **Public IP Address**   | Assigns a public-facing IP to a resource                                      | Access VM, LB, etc. from internet                   |
| **Azure Load Balancer** | Distributes traffic across multiple VMs (Layer 4 - TCP/UDP)                   | Scale & high availability                           |
| **NAT Gateway**         | Allows private subnets to access the internet **outbound**, using a public IP | Internet access without assigning public IPs to VMs |

* A VM with a public IP can be accessed directly (but it's less secure).
* Use a Load Balancer to spread traffic across many VMs (with or without public IPs).
* Use a NAT Gateway when you want outbound internet access without exposing the VM.
* NAT Gateway is purpose-built for outbound-only, without assigning public IPs to every VM.
* a VM with a directly assigned public IP uses that for outbound, bypassing NAT Gateway.
* Load Balancer = good for multi-VM, port-based, layer 4 traffic
* health probe = 

### 4-6. Private Endpoints & DNS
This section is about securely accessing Azure services without using the public internet

| Concept               | What It Means                                            | Use Case                                |
| --------------------- | -------------------------------------------------------- | --------------------------------------- |
| **Private Endpoint**  | Private IP in your VNet mapped to a public Azure service | Access Blob, SQL, etc. *privately*      |
| **Private Link**      | The underlying tech that enables Private Endpoints       | The magic under the hood                |
| **Private DNS Zone**  | DNS zone like `privatelink.database.windows.net`         | Resolve private endpoints automatically |
| **Split-horizon DNS** | Same name resolves differently inside vs. outside VNet   | Secure, internal-only resolution        |

💡 How It Works
 1. You create a Private Endpoint for, say, a Blob Storage account.
 2. Azure gives that storage account a private IP inside your VNet.
 3. Azure sets up a private DNS zone (optional but recommended).
 4. When your VMs access myaccount.blob.core.windows.net, DNS resolves it to the private IP.
 * ☁️🌐 ➡️ 🔒 = Secure, internal routing only — no public IP traffic.

🧠 Key Exam Clues
 * “Restrict public access” → Use Private Endpoint
 * “Allow access only from VNet” → Use Private Link
 * “Name resolution is broken” → DNS zone is likely missing
 * “Resolve to private IP” → Use Private DNS Zone
 * “Different resolution inside/outside” → That’s split-horizon DNS

| Type      | What It Does                              | Example                                     | 💡 When to Use                |
| --------- | ----------------------------------------- | ------------------------------------------- | ----------------------------- |
| **A**     | Maps a name to an **IPv4 address**        | `app1.contoso.com → 52.160.1.5`             | You know the exact IP 🧠      |
| **AAAA**  | Maps a name to an **IPv6 address**        | `host.contoso.com → ::1`                    | IPv6 support                  |
| **CNAME** | Maps a name to another **domain name**    | `www.contoso.com → app.azurewebsites.net`   | You want to alias a name 🧠   |
| **MX**    | Mail exchange record (for email routing)  | `contoso.com → mailserver1`                 | Mail delivery                 |
| **TXT**   | Stores **text** (SPF, verification, etc.) | `v=spf1 include:spf.protection.outlook.com` | Identity checks, auth         |
| **NS**    | Delegates DNS zone to name servers        | `contoso.com → ns1-azure-dns.com`           | DNS zone authority            |
| **PTR**   | Reverse DNS — IP → hostname               | `52.160.1.5 → app1.contoso.com`             | Reverse lookups (less common) |

* A Record = Address  🔢 You have the IP, and you want to point a name to it.  Use A when Azure gives you a static public IP (like with Load Balancer).
* CNAME = Canonical Name 🔗 You want to alias a name to another domain — no IP involved.  Use CNAME when Azure gives you a domain, not an IP (like with App Services or Front Door).

| If You See This in the Question...                   | Use This Type                            |
| ---------------------------------------------------- | ---------------------------------------- |
| “Static IP was assigned”                             | ✅ A Record                               |
| “You must use a custom domain with Azure Front Door” | ✅ CNAME                                  |
| “Set up SPF for email security”                      | ✅ TXT                                    |
| “Users can’t resolve the name to IP”                 | A or CNAME — depends on what’s behind it |
| “Point domain to Azure App Service”                  | ✅ CNAME                                  |
| “Use domain with static IP from Load Balancer”       | ✅ A Record                               |


### 4-7. Network Security Tools

| Tool                          | What It Secures                    | Layer | Typical Use                                      |
| ----------------------------- | ---------------------------------- | ----- | ------------------------------------------------ |
| **NSG**                       | Network traffic at subnet/NIC      | L3/4  | Allow/block inbound/outbound traffic (TCP/UDP)   |
| **Azure Firewall**            | Network edge with full logging     | L3–7  | Centralized rules, stateful inspection, FQDNs    |
| **Application Gateway (WAF)** | HTTP/S traffic (Layer 7)           | L7    | Protects apps from OWASP threats                 |
| **Azure Bastion**             | RDP/SSH access to VMs              | N/A   | Secure VM access via browser, no public IPs      |
| **DDoS Protection**           | Defends against volumetric attacks | N/A   | Auto-enabled Basic, Standard = alerts/mitigation |


🧠 Core Differences & Exam Clues
| Scenario / Clue                         | Tool to Use        |
| --------------------------------------- | ------------------ |
| “Control traffic between subnets”       | NSG                |
| “Block access to malicious domains”     | Azure Firewall     |
| “Restrict RDP access without public IP” | Azure Bastion      |
| “Protect web apps from SQL injection”   | WAF on App Gateway |
| “Get alert on high-traffic DDoS event”  | DDoS Standard      |


🔁 NSG vs Firewall vs WAF (Web App Firewall) – TL;DR
| Feature         | NSG     | Firewall          | WAF on App Gateway    |
| --------------- | ------- | ----------------- | --------------------- |
| Layer           | 3–4     | 3–7               | 7 (HTTP/S only)       |
| Logs            | Minimal | Rich, Centralized | WAF logs (diagnostic) |
| Domain names    | ❌       | ✅                 | ✅ (for HTTP URLs)     |
| Central control | ❌       | ✅                 | ✅ (App only)          |

🛡️ Azure DDoS Protection – Defense Against Floods
 * Basic (Always On, Free) -> scope: 	Platform-wide (Azure infra + all PIPs), Automatic mitigation for large attacks BUT ❌ No logs, alerts, or cost protection
 * Standard (Paid, Enterprise Use) -> Scope: 	Your resources with public IPs, Logs, alerts, Rapid Response team access



## 💡 Hints and tips

* A /24 block = 256 IPs total, 2 are reserved by Azure: 1 for the network address and 1 for the broadcast address --> 256 - 2 = **254 usable IPs**

You were close — this is a classic trap!

🧠 Port Quick Reference
| Protocol  | Port | Use                           |
| --------- | ---- | ----------------------------- |
| **SSH**   | 22   | Secure Shell (Linux VMs, CLI) |
| **HTTP**  | 80   | Unencrypted web traffic       |
| **HTTPS** | 443  | Secure web traffic            |
| **RDP**   | 3389 | Remote Desktop (Windows)      |
| **FTP**   | 21   | Old school file transfers     |



| Cmdlet                            | Purpose                       | Tip to Remember            |
| --------------------------------- | ----------------------------- | -------------------------- |
| `New-AzVirtualNetwork`            | Create a new VNet             | Core networking            |
| `New-AzNetworkInterface`          | Create a NIC (needed for VMs) | Attaches VM to network     |
| `New-AzPublicIpAddress`           | Provision a public IP         | Needed for internet access |
| `New-AzNetworkSecurityGroup`      | Creates an NSG                | Use with subnets/NICs      |
| `New-AzNetworkSecurityRuleConfig` | Add rule to NSG               | Watch for priority/order   |
 