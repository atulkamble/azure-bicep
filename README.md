# Azure-Bicep

Azure Bicep is a Domain-Specific Language (DSL) for deploying Azure resources declaratively. It is a more concise, human-readable syntax compared to Azure Resource Manager (ARM) JSON templates. Bicep supports modularity, enabling you to reuse code and organize your infrastructure as code effectively.

Here’s an overview of Bicep modules and sample code to get started:

```
az bicep version
az bicep install 
az bicep version
```

---

## **What Are Bicep Modules?**
Bicep modules are reusable pieces of code that encapsulate resource definitions. Modules promote reusability and maintainability by allowing you to define resources in a separate file and use them in multiple deployments.

- **Root Module**: The main entry point of a Bicep file (e.g., `main.bicep`).
- **Child Modules**: Additional `.bicep` files containing reusable resource definitions.

---

## **How to Use Modules**
### 1. **Create a Module**
Define a module in its `.bicep` file. For example, create a `storageAccount.bicep` file for deploying a storage account.

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

### 2. **Reference the Module in the Root File**
Use the `module` keyword to call the module in the root `main.bicep` file.

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

### 3. **Deploy the Bicep File**
Deploy using the Azure CLI:

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file main.bicep
```

---

## **Benefits of Using Modules**
- **Code Reusability**: Define resources once and use them across multiple projects.
- **Modularization**: Break down complex templates into manageable files.
- **Simplified Updates**: Update the module file, and changes reflect in all deployments.

---

## **Example: Virtual Network Module**
### vnet.bicep
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

### main.bicep
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

## **Tips for Working with Modules**
1. **Organize by Resource**: Use separate files for different resources, such as `vnet.bicep`, `storageAccount.bicep`, etc.
2. **Use Parameters and Outputs**: Enable flexibility by exposing key properties via parameters and outputs.
3. **Use `targetScope`**: Specify whether the module is for a subscription, resource group, or management group deployment.
4. **Validate Templates**: Use `az bicep build` to compile and validate your Bicep files.

Need help with specific examples or advanced scenarios? Let me know!
