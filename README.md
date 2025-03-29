---

# 🌩️ Azure Bicep – Getting Started with Modules and Deployment

Azure Bicep is a **Domain-Specific Language (DSL)** for deploying Azure resources declaratively. It is **simpler, cleaner, and more readable** than traditional ARM JSON templates. Bicep also supports **modularity**, enabling you to reuse code and organize your infrastructure as code effectively.

---
Ref:
1. [What is Bicep?](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview?tabs=bicep)
2. [Bicep for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.visualstudiobicep)
3. [Install Bicep tools](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/install)
4. [vscode-remote-try-bicep](https://github.com/Azure/vscode-remote-try-bicep)
5. [Create Bicep files with Visual Studio Code](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code?tabs=azure-cli)
6. [Bicep Playground](https://azure.github.io/bicep/)
7. [Learn modules for Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/learn-bicep)
8. [Decompile a JSON Azure Resource Manager template to Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/decompile?tabs=azure-cli)
9. [Define resources with Bicep, ARM templates, and Terraform AzAPI provider](https://learn.microsoft.com/en-us/azure/templates/)
10. [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts)


## 🛠️ Prerequisites

Install and verify Bicep CLI with Azure CLI:

```bash
az login
az bicep version
az bicep install 
az bicep upgrade
az deployment create
bicep build
```
```bash
cd Downloads
git clone https://github.com/atulkamble/azure-bicep.git
cd azure-bicep

// Add Extension bicep in VSCode

az account show
az account set --subscription "cc57cd42-dede-4674-b810-a0fbde41504a"

az group list --output table
az group create --name myRG --location eastus
az deployment group create --resource-group myRG --template-file main01.bicep


az deployment group create --resource-group myRG --template-file main01.bicep

az bicep build --file main.bicep
az bicep decompile --file template.json
az deployment group show --resource-group <your-resource-group>

az group delete --name myRG --yes --no-wait

az deployment group list --resource-group myRG --output table

az deployment group delete --name main01 --resource-group myRG

az resource delete --name mystorageaccount --resource-group myRG --resource-type "Microsoft.Storage/storageAccounts"

rm main.bicep
Remove-Item main.bicep

```
---

## 🔧 What Are Bicep Modules?

Bicep **modules** are reusable pieces of code that encapsulate Azure resource definitions. You can:

- Break large deployments into smaller files.
- Reuse modules across multiple projects.
- Simplify complex deployments.

### Types:
- **Root Module**: Main entry point (e.g., `main.bicep`)
- **Child Modules**: Reusable resource definitions (e.g., `storageAccount.bicep`, `vnet.bicep`)

---

## 📦 How to Use Modules – Step-by-Step

### ✅ Step 1: Create a Module (e.g., `storageAccount.bicep`)

```bicep
@description('Name of the storage account')
param storageAccountName string

@description('Location of the storage account')
param location string = resourceGroup().location

resource storageAccount 'Microsoft.Storage/storageAccounts@2022-09-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}

output storageAccountId string = storageAccount.id
```

---

### ✅ Step 2: Reference the Module in `main.bicep`

```bicep
@description('Storage account name')
param storageAccountName string = 'myuniquestorageaccount'

module storageModule './storageAccount.bicep' = {
  name: 'storageDeployment'
  params: {
    storageAccountName: storageAccountName
    location: resourceGroup().location
  }
}

output storageAccountId string = storageModule.outputs.storageAccountId
```

---

## 🧩 Additional Example – Virtual Network Module

### `vnet.bicep`

```bicep
@description('Name of the virtual network')
param vnetName string

@description('Address space of the virtual network')
param addressSpace array

resource vnet 'Microsoft.Network/virtualNetworks@2022-09-01' = {
  name: vnetName
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: addressSpace
    }
  }
}

output vnetId string = vnet.id
```

### Use in `main.bicep`

```bicep
param vnetName string = 'myVNet'
param addressSpace array = [
  '10.0.0.0/16'
]

module vnetModule './vnet.bicep' = {
  name: 'vnetDeployment'
  params: {
    vnetName: vnetName
    addressSpace: addressSpace
  }
}

output vnetId string = vnetModule.outputs.vnetId
```

---

## 🚀 How to Run a Bicep File

You’ll use Azure CLI to deploy your Bicep files.

### ✅ Step-by-Step Deployment

#### 🧱 1. Create a Resource Group
```bash
az group create --name MyResourceGroup --location eastus
```

#### 🚀 2. Deploy the Template
```bash
az deployment group create \
  --resource-group MyResourceGroup \
  --template-file main.bicep
```

---

### 🧪 Optional: Compile and Preview ARM from Bicep

```bash
az bicep build --file main.bicep
```

---

## 🗂️ Real-World Folder Structure for Bicep Projects

```
/iac
│
├── main.bicep
├── modules/
│   ├── vnet.bicep
│   ├── vm.bicep
│   └── storageAccount.bicep
```

### Sample Module Usage:
```bicep
module vnet './modules/vnet.bicep' = {
  name: 'vnetDeploy'
  params: {
    vnetName: 'myVnet'
    addressSpace: ['10.0.0.0/16']
  }
}
```

---

## ✅ Benefits of Using Bicep Modules

- **Code Reusability**: Use the same logic across multiple deployments.
- **Maintainability**: Update modules in one place.
- **Simplified Syntax**: Less code, more clarity.
- **Consistency**: Enforce standards and structure.

---

## 💡 Pro Tips

- Use `@description` to make your parameters self-documenting.
- Use `targetScope` if you’re deploying at subscription or tenant level.
- Validate your modules with:
```bash
az bicep build --file <module>.bicep
```

---

Would you like this organized into a GitHub repo or want a sample project (with VNet + VM + Storage) zipped and ready to run?
