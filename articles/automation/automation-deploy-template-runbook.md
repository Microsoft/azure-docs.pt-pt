---
title: Implementar um modelo do Azure Resource Manager num runbook do PowerShell da Automatização do Azure
description: Este artigo descreve como implementar um modelo de Gestor de Recursos Azure armazenado no Azure Storage a partir de um livro de execução PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: powershell, runbook, json, azure automação
ms.openlocfilehash: d9b443d1840840d3d6202140da235589c73453cc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051401"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Implemente um modelo de gestor de recursos Azure num livro de execução PowerShell

Pode escrever um [manual Azure Automation PowerShell](./learn/automation-tutorial-runbook-textual-powershell.md) que implementa um recurso Azure utilizando um [modelo de Gestão de Recursos Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Os modelos permitem-lhe utilizar a Azure Automation para automatizar a implementação dos seus recursos Azure. Pode manter os seus modelos de Gestor de Recursos numa localização central e segura, como o Azure Storage.

Neste artigo, criamos um runbook PowerShell que utiliza um modelo de Gestor de Recursos armazenado no [Azure Storage](../storage/common/storage-introduction.md) para implementar uma nova conta de Armazenamento Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automatização](./automation-security-overview.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* [Conta de Armazenamento Azure](../storage/common/storage-account-create.md) na qual armazenar o modelo de Gestor de Recursos.
* Azure PowerShell instalado numa máquina local. Consulte [a Instalação do Módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações sobre como obter a Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Neste exemplo, usamos um modelo de Gestor de Recursos que implementa uma nova conta de Armazenamento Azure.

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

Guarde o ficheiro localmente como **TemplateTest.jsligado**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Guarde o modelo do Gestor de Recursos no Armazenamento Azure

Agora usamos o PowerShell para criar uma partilha de ficheiros Azure Storage e carregar a **TemplateTest.jsno** ficheiro. Para obter instruções sobre como criar uma partilha de ficheiros e carregar um ficheiro no portal Azure, consulte Começar com o [armazenamento do Ficheiro Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Lançar PowerShell na sua máquina local e executar os seguintes comandos para criar uma partilha de ficheiros e carregar o modelo de Gestor de Recursos para essa partilha de ficheiros.

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

## <a name="create-the-powershell-runbook-script"></a>Crie o script do livro de execução PowerShell

Agora criamos um script PowerShell que obtém a **TemplateTest.jsem** ficheiro a partir do Azure Storage e implementamos o modelo para criar uma nova conta de Armazenamento Azure.

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

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importe e publique o livro de bordo na sua conta de Automação Azure

Agora usamos o PowerShell para importar o livro de bordo na sua conta de Automação Azure e, em seguida, publicar o livro de bordo. Para obter informações sobre como importar e publicar um livro de bordo no portal Azure, consulte [Gerir os runbooks na Azure Automation](manage-runbooks.md).

Para importar **DeployTemplate.ps1** na sua conta Demôm automação como um livro de regras PowerShell, execute os seguintes comandos PowerShell:

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

Agora começamos o runbook chamando o [cmdlet Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Para obter informações sobre como iniciar um livro de bordo no portal Azure, consulte [Iniciar um livro de recortes na Azure Automation](./start-runbooks.md).

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

Após o runbook, pode verificar o seu estado recuperando o valor da propriedade do objeto de `$job.Status` trabalho.

O runbook obtém o modelo de Gestor de Recursos e usa-o para implementar uma nova conta de Armazenamento Azure. Pode ver que a nova conta de armazenamento foi criada executando o seguinte comando:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os modelos do Gestor de Recursos, consulte [a visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Para começar com o Azure Storage, consulte [Introdução ao Armazenamento Azure](../storage/common/storage-introduction.md).
* Para encontrar outros livros úteis da Azure Automation, consulte [use livros e módulos em Azure Automation](automation-runbook-gallery.md).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation#automation).
