# Overview
* Docummentation in MS Learn: 
[https://learn.microsoft.com/en-us/cli](https://learn.microsoft.com/en-us/cli)

* **Azure CLI:** A cross-platform command-line tool to connect to Azure and execute administrative commands on Azure resources. It can be used from a terminal, a script, a browser, or a Docker container.
* **Installation and sign-in:** The Azure CLI can be installed locally on Linux, Mac, or Windows computers. To use it, you need to sign in with the *az login* command and install extensions on first use.
* **Syntax and examples:** The Azure CLI syntax follows a simple pattern of reference name - command - parameter - parameter value.
* **Telemetry and privacy:** The Azure CLI collects telemetry data by default to improve the user experience4. The data does not include any private or personal information5. You can disable data collection with the az config set core.collect_telemetry=false command6.

# Basic Commands

0. Bicep install
```bash
az bicep install && az bicep upgrade
```

1. Login
```bash
az login
```
2. Set the default tenant
```bash
az account list -o table
az account set --subscription SUBSCRIPTIONID
```
3. Set the default resource group
```bash
az group list -o table

az configure --defaults group=GROUPNAME
```
4. Deployment of the bicep file
```bash
az deployment group create \
--template-file main.bicep \
--parameters env=test
```
5. Deployment using a parameters file
```bash
az deployment group create \
--template-file main.bicep \
--parameters PARAMFILENAME.json
```
6. Making keyvault and setting up secrets 
```bash
keyVaultName='YOUR-KEY-VAULT-NAME'
read -s -p "Enter the login name: " login
read -s -p "Enter the password: " password

az keyvault create --name $keyVaultName --location westus3 --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "sqlServerAdministratorLogin" --value $login --output none
az keyvault secret set --vault-name $keyVaultName --name "sqlServerAdministratorPassword" --value $password --output none

-- to get the resource id
az keyvault show --name $keyVaultName --query id --output tsv
```
