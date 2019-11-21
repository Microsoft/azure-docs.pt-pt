---
title: Configurar servidores para um estado pretendido e gerir desvios com a Automatização do Azure
description: Tutorial - Manage server configurations with Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 72e5018dc1212e57dc190c05cc54158d37ca7fe1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231505"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configure servers to a desired state and manage drift

Azure Automation State Configuration allows you to specify configurations for your servers and ensure that those servers are in the specified state over time.

> [!div class="checklist"]
> - Onboard a VM to be managed by Azure Automation DSC
> - Upload a configuration to Azure Automation
> - Compile a configuration into a node configuration
> - Assign a node configuration to a managed node
> - Check the compliance status of a managed node

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- An Azure Resource Manager VM (not Classic) running Windows Server 2008 R2 or later. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell module version 3.6 or later. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
- Familiarity with Desired State Configuration (DSC). For information about DSC, see [Windows PowerShell Desired State Configuration Overview](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Create and upload a configuration to Azure Automation

For this tutorial, we will use a simple DSC configuration that ensures that IIS is installed on the VM.

Para obter informações sobre configurações de DSC, veja [DSC configurations (Configurações de DSC)](/powershell/scripting/dsc/configurations/configurations).

Num editor de texto, escreva o seguinte e guarde-o localmente como `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> In more advanced scenarios where you require multiple modules to be imported that provide DSC Resources, make sure each module has a unique `Import-DscResource` line in your configuration.

Call the `Import-AzureRmAutomationDscConfiguration` cmdlet to upload the configuration into your Automation account:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compile a configuration into a node configuration

A DSC configuration must be compiled into a node configuration before it can be assigned to a node.

For information about compiling configurations, see [DSC configurations](/powershell/scripting/dsc/configurations/configurations).

Call the `Start-AzureRmAutomationDscCompilationJob` cmdlet to compile the `TestConfig` configuration into a node configuration:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

This creates a node configuration named `TestConfig.WebServer` in your Automation account.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Register a VM to be managed by State Configuration

You can use Azure Automation State Configuration to manage Azure VMs (both Classic and Resource Manager), on-premises VMs, Linux machines, AWS VMs, and on-premises physical machines. In this topic, we cover how to register only Azure Resource Manager VMs. For information about registering other types of machines, see [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

Call the `Register-AzureRmAutomationDscNode` cmdlet to register your VM with Azure Automation State Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

This registers the specified VM as a managed node in State Configuration.

### <a name="specify-configuration-mode-settings"></a>Specify configuration mode settings

When you register a VM as a managed node, you can also specify properties of the configuration. For example, you can specify that the state of the machine is to be applied only once (DSC does not attempt to apply the configuration after the initial check) by specifying `ApplyOnly` as the value of the **ConfigurationMode** property:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

You can also specify how often DSC checks the configuration state by using the **ConfigurationModeFrequencyMins** property:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

For more information about setting configuration properties for a managed node, see [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

For more information about DSC configuration settings, see [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Assign a node configuration to a managed node

Now we can assign the compiled node configuration to the VM we want to configure.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

This assigns the node configuration named `TestConfig.WebServer` to the registered DSC node named `DscVm`.
By default, the DSC node is checked for compliance with the node configuration every 30 minutes.
For information about how to change the compliance check interval, see [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Working with Partial Configurations

Azure Automation State Configuration supports usage of [partial configurations](/powershell/scripting/dsc/pull-server/partialconfigs).
In this scenario, DSC is configured to manage multiple configurations independently, and each configuration is retrieved from Azure Automation.
However, only one configuration can be assigned to a node per automation account.
This means if you are using two configurations for a node you will require two automation accounts.

For details about how to register a partial configuration from pull service, see the documentation for [partial configurations](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

For more information about how teams can work together to collaboratively manage servers using configuration as code see [Understanding DSC's role in a CI/CD Pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Check the compliance status of a managed node

You can get reports on the compliance status of a managed node by calling the `Get-AzureRmAutomationDscNodeReport` cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Removing nodes from service

When you add a node to Azure Automation State Configuration, the settings in Local Configuration Manager are set to register with the service and pull configurations and required modules to configure the machine.
If you choose to remove the node from the service, you can do so using either the Azure portal or the Az cmdlets.

> [!NOTE]
> Unregistering a node from the service only sets the Local Configuration Manager settings so the node is no longer connecting to the service.
> This does not effect the configuration that is currently applied to the node.
> To remove the current configuration, use the [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) or delete the local configuration file (this is the only option for Linux nodes).

### <a name="azure-portal"></a>Portal do Azure

From Azure Automation, click on **State configuration (DSC)** in the table of contents.
Next click **Nodes** to view the list of nodes that are registered with the service.
Click on the name of the node you wish to remove.
In the Node view that opens, click **Unregister**.

### <a name="powershell"></a>PowerShell

To unregister a node from Azure Automation State Configuration service using PowerShell, follow the documentation for the cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passos seguintes

- To get started, see [Getting started with Azure Automation State Configuration](automation-dsc-getting-started.md)
- To learn how to onboard nodes, see [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md)
- To learn about compiling DSC configurations so that you can assign them to target nodes, see [Compiling configurations in Azure Automation State Configuration](automation-dsc-compile.md)
- For PowerShell cmdlet reference, see [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation)
- For pricing information, see [Azure Automation State Configuration pricing](https://azure.microsoft.com/pricing/details/automation/)
- To see an example of using Azure Automation State Configuration in a continuous deployment pipeline, see [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md)
