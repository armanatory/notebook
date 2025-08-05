# PowerShell Cmdlets in AZ-104

## Common commands : Big Picture Summary

    know what the cmdlet does, not memorize its syntax.

### 🧱 **1. Resource Creation**

* **New-AzResourceGroup** → create a resource group
* **New-AzStorageAccount** → create storage account
* **New-AzVM** → deploy a virtual machine
* **New-AzDisk / New-AzDiskConfig** → prepare data disk
* **Add-AzVMDataDisk** → attach a data disk to VM
* **New-AzResourceGroupDeployment** → deploy **ARM template** to a **resource group**

> ✅ Mnemonic: **"New-Az = I’m creating something."**

---

### 🔄 **2. Deployment & Templates**

* **New-AzResourceGroupDeployment** → deploy ARM template to a **resource group**
* **az deployment group create** → same as above, but in **CLI**
* **Export-AzResourceGroup** → generate ARM template from **live environment**
* **Save-AzResourceGroupDeploymentTemplate** → save **template from deployment history**
* **Get-AzResourceGroupDeployment / DeploymentOperation** → inspect old deployments

> ✅ Mnemonic:
> **“Export = current state”**
> **“Save = what was deployed”**

---

### ⚙️ **3. Scale Set Management**

* **Update-AzVmss** → update properties (OS/data disks/image ref)
* **Start-AzVmssRollingOSUpgrade** → upgrade OS across instances
* **Update-AzVmssInstance** → refresh VMSS instance from model

> ⚠️ Common trap:
> Updating config? → `Update-AzVmss`
> Rolling update? → `Start-AzVmssRollingOSUpgrade`

---

### ☁️ **4. Management Groups & Subscriptions**

* **New-AzManagementGroupSubscription** → move sub into MG
* **Remove-AzManagementGroupSubscription** → remove sub from MG

> ✅ Think: “I'm managing a tree of groups & subs.”

---

### 📦 **5. App Services & Slot Swaps**

* **Start-AzWebAppSlotSwap** → with preview (`-SwapWithPreviewAction`)
* **CompleteSlotSwap**, **ApplySlotConfig**, **ResetSlotSwap** → finalize or rollback swap

> 🧠 Mnemonic:
> **“Swap = 2 steps: Start (preview) → Complete/Apply/Reset”**

---

### 🔐 **6. Storage Access**

* **Get-AzStorageAccountKey** → fetch storage keys (e.g., give to devs)
* **Set-AzStorageAccountNetworkRuleSet** → restrict access by network rules
* **Set-AzVirtualNetworkSubnetConfig** → add service endpoint for Microsoft.Storage

---

## How to Remember in Exams:

### 🧠 Use This Thought Model:

1. **Am I deploying something?** → `New-*`
2. **Am I exporting or saving a template?**

   * Current config? → `Export`
   * Past deployment? → `Save`
3. **Am I configuring something already created?** → `Set-*` or `Update-*`
4. **CLI or PowerShell?** `az` = CLI, `Az` = PowerShell



---

## ⚠️ Common PowerShell Pitfalls in AZ-104

### 1. **Choosing the Wrong Cmdlet Type**

* **Trap**: Picking a cmdlet that deploys a resource instead of configuring it.
* **Example**: Choosing `New-AzResource` instead of `New-AzResourceGroupDeployment` to deploy a template.

> 🔑 Think: *“Am I creating a new resource or deploying from a template?”*

---

### 2. **Confusing `Export-` with `Save-`**

* `Export-AzResourceGroup`: exports current **live** state as ARM template.
* `Save-AzResourceGroupDeploymentTemplate`: saves template from a **previous deployment**.

> ⚠️ Pitfall: If the question says "manually updated since deployment", `Export-` is correct — not `Save-`.

---

### 3. **Selecting a VM cmdlet when the question is about a Scale Set**

* **Trap**: Using `New-AzVM` or `Set-AzVM` when working with **VMSS**.
* **Correct**: Use `Update-AzVmss`, `Start-AzVmssRollingOSUpgrade`, or `Update-AzVmssInstance`.

> 🧠 Mnemonic: VMSS ≠ VM

---

### 4. **Missing the Azure CLI vs PowerShell Difference**

* **az deployment group create** → Azure CLI
* **New-AzResourceGroupDeployment** → PowerShell

> ⚠️ Trap: The cmdlet will be wrong *even if the logic is right*, if it's the wrong toolset.

---

### 5. **Assuming `New-AzResource` is for templates**

* `New-AzResource` is used for generic REST-level resource creation (low-level).
* **Not** used for ARM template deployments.

> Use `New-AzResourceGroupDeployment` instead.

---

### 6. **Confusing Preview & Completion for Slot Swaps**

* **Slot swap** is a 2-step process:

  1. Start with preview: `Start-AzWebAppSlotSwap -SwapWithPreviewAction ApplySlotConfig`
  2. Then: `CompleteSlotSwap` or `ResetSlotSwap`

> 🔄 If you choose just one step, you're wrong.

---

### 7. **Wrong cmdlet for disk operations**

* `New-AzDisk` vs `Add-AzVMDataDisk` vs `Update-AzDisk`

  * Creating the disk? → `New-AzDisk`
  * Attaching to VM? → `Add-AzVMDataDisk`
  * Modifying properties? → `Update-AzDisk`

---

### 8. **Management Group Confusion**

* Moving subs:

  * Use `New-AzManagementGroupSubscription`
  * Not `Update-*`, which updates metadata

---

## ✅ Quick Mental Checklist for Each Cmdlet Question

Ask:

1. **Am I creating, configuring, or deploying?**
2. **Is this PowerShell or CLI?**
3. **Does the cmdlet match the resource type (VM ≠ VMSS)?**
4. **Is the question about current state or past deployment?**
5. **Am I missing a second step (like Complete/Attach)?**

---

Want to try applying this checklist to one of the questions you posted earlier? I can walk you through one with this mindset.

