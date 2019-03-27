---
title: Implementar um modelo Azure Resource Manager num runbook da automatização do Azure
description: Como implementar um modelo do Azure Resource Manager armazenado no armazenamento do Azure a partir de um runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, azure automation
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446975"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Implementar um modelo do Azure Resource Manager num runbook do PowerShell da Automatização do Azure

Pode escrever um [runbook do PowerShell da automatização do Azure](automation-first-runbook-textual-powershell.md) que implementa um recurso do Azure utilizando uma [modelo Azure Resource Management](../azure-resource-manager/resource-manager-create-first-template.md).

Ao fazer isso, pode automatizar a implantação de recursos do Azure. Pode manter modelos do Resource Manager numa localização central e seguro, como o armazenamento do Azure.

Neste artigo, vamos criar um runbook do PowerShell que utiliza um modelo do Resource Manager armazenado no [armazenamento do Azure](../storage/common/storage-introduction.md) para implementar uma nova conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [Inscreva-se numa conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automatização](automation-sec-configure-azure-runas-account.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) para armazenar o modelo do Resource Manager
* O Azure Powershell instalado numa máquina local. Ver [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Neste exemplo, vamos utilizar um modelo do Resource Manager que implementa uma nova conta de armazenamento do Azure.

Num editor de texto, copie o seguinte texto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Guarde o ficheiro localmente como `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Guardar o modelo do Resource Manager no armazenamento do Azure

Agora, vamos utilizar o PowerShell para criar uma partilha de ficheiros de armazenamento do Azure e carregar o `TemplateTest.json` ficheiro.
Para obter instruções sobre como criar um ficheiro partilharem em carregar um ficheiro no portal do Azure, consulte [introdução ao armazenamento de ficheiros do Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Inicie o PowerShell no seu computador local e execute os seguintes comandos para criar uma partilha de ficheiros e carregar o modelo do Resource Manager para que a partilha de ficheiros.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Criar o script de runbook do PowerShell

Agora, vamos criar um script do PowerShell que obtém o `TemplateTest.json` de ficheiros do armazenamento do Azure e implanta o modelo para criar uma nova conta de armazenamento do Azure.

Num editor de texto, cole o seguinte texto:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Guarde o ficheiro localmente como `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importar e publicar o runbook para a sua conta de automatização do Azure

Agora vamos utilizar o PowerShell para importar o runbook para a sua conta de automatização do Azure e, em seguida, publicar o runbook.
Para obter informações sobre como importar e publicar um runbook no portal do Azure, consulte [gerir runbooks na automatização do Azure](manage-runbooks.md).

Para importar `DeployTemplate.ps1` na sua conta de automatização, como um runbook do PowerShell, execute os seguintes comandos do PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Iniciar o runbook

Agora, vamos iniciar o runbook ao chamar o [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet.

Para obter informações sobre como iniciar um runbook no portal do Azure, consulte [a partir de um runbook na automatização do Azure](automation-starting-a-runbook.md).

Execute os seguintes comandos na consola do PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

O runbook é executado, e pode verificar o estado do mesmo ao executar `$job.Status`.

O runbook obtém o modelo do Resource Manager e utiliza-os para implementar uma nova conta de armazenamento do Azure.
Pode ver que a nova conta de armazenamento foi criada ao executar o seguinte comando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Resumo

Já está! Agora, pode utilizar modelos de automatização do Azure e o armazenamento do Azure e o Resource Manager para implementar todos os seus recursos do Azure.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre modelos do Resource Manager, consulte [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Para começar a utilizar com o armazenamento do Azure, veja [introdução ao armazenamento do Azure](../storage/common/storage-introduction.md).
* Para obter outros útil runbooks de automatização do Azure, veja [galerias de módulos e Runbooks de automatização do Azure](automation-runbook-gallery.md).
* Para localizar outros modelos úteis do Resource Manager, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)


