---
title: Implementar um modelo do Azure Resource Manager num runbook do PowerShell da Automatização do Azure
description: Este artigo diz como implementar um modelo de Gestor de Recursos Azure armazenado no Armazenamento Azure a partir de um livro de execução PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, automação azul
ms.openlocfilehash: 921d878c585b811700b1c112524e314f0af53c24
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837081"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Implemente um modelo de Gestor de Recursos Azure num livro de execução powerShell

Pode escrever um livro de [execução Azure Automation PowerShell](automation-first-runbook-textual-powershell.md) que implementa um recurso Azure utilizando um modelo de Gestão de [Recursos Azure](../azure-resource-manager/resource-manager-create-first-template.md). A utilização do modelo permite-lhe utilizar a Azure Automation e o Azure Storage para automatizar a implantação dos seus recursos Azure. Pode manter os seus modelos de Gestor de Recursos num local central e seguro, como o Armazenamento Azure.

Neste artigo, criamos um livro powerShell que utiliza um modelo de Gestor de Recursos armazenado no [Armazenamento Azure](../storage/common/storage-introduction.md) para implementar uma nova conta de Armazenamento Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automatização](automation-sec-configure-azure-runas-account.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* [Conta de Armazenamento Azure](../storage/common/storage-create-storage-account.md) na qual armazenar o modelo de Gestor de Recursos
* Azure PowerShell instalado numa máquina local. Consulte [a instalação do Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Para este exemplo, usamos um modelo de Gestor de Recursos que implementa uma nova conta de Armazenamento Azure.

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

Guarde o ficheiro localmente como **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Salve o modelo do Gestor de Recursos no Armazenamento Azure

Agora usamos powerShell para criar uma partilha de ficheiros De armazenamento Azure e carregar o ficheiro **TemplateTest.json.**
Para obter instruções sobre como criar uma partilha de ficheiros e fazer upload de um ficheiro no portal Azure, consulte [Iniciar-se com o armazenamento de Ficheiros Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Lance powerShell na sua máquina local e execute os seguintes comandos para criar uma partilha de ficheiros e fazer upload do modelo de Gestor de Recursos para essa partilha de ficheiros.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Crie o script powerShell runbook

Agora criamos um script PowerShell que obtém o ficheiro **TemplateTest.json** do Armazenamento Azure e implementa o modelo para criar uma nova conta de Armazenamento Azure.

Num editor de texto, cola o seguinte texto:

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
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Guarde o ficheiro localmente como **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importe e publique o livro de corridas na sua conta Azure Automation

Agora usamos a PowerShell para importar o livro de corridas para a sua conta Azure Automation e, em seguida, publicar o livro de execução. Para obter informações sobre como importar e publicar um livro de execução no portal Azure, consulte [Gerir livros de execução em Automação Azure.](manage-runbooks.md)

Para importar **deploytemplate.ps1** para a sua conta de Automação como um livro de execução PowerShell, execute os seguintes comandos PowerShell:

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
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Iniciar o runbook

Agora começamos o livro de corridas chamando o [cmdlet start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) Para obter informações sobre como iniciar um livro de corridas no portal Azure, consulte [Iniciar um livro de corridas em Automação Azure](automation-starting-a-runbook.md).

Executar os seguintes comandos na consola PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

O livro de corridas corre e pode verificar o seu estado executando `$job.Status` .

O livro de execução obtém o modelo de Gestor de Recursos e usa-o para implementar uma nova conta de Armazenamento Azure.
Pode ver que a nova conta de armazenamento foi criada executando o seguinte comando:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os modelos do Gestor de Recursos, consulte a [visão geral do Gestor de Recursos do Azure](../azure-resource-manager/management/overview.md).
* Para começar com o Armazenamento Azure, consulte [Introdução ao Armazenamento Azure.](../storage/common/storage-introduction.md)
* Para encontrar outros livros de execução úteis da Azure Automation, consulte [Livros de execução e módulos usados em Automação Azure.](automation-runbook-gallery.md)
* Para encontrar outros modelos úteis do Gestor de Recursos, consulte [os modelos De Arranque Rápido do Azure](https://azure.microsoft.com/resources/templates/).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).