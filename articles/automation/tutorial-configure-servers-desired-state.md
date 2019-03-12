---
title: Configurar servidores para um estado pretendido e gerir desvios com a Automatização do Azure
description: Tutorial - Gerir configurações de servidor com a configuração de estado de automatização do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 582533d23757de748b9cc7d40e45acc00240d384
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570322"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado pretendido e gerir desvios

Configuração de estado da automatização do Azure permite-lhe especificar configurações para os seus servidores e certifique-se de que esses servidores estão no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - Carregar uma VM para serem geridos pelo Azure Automation DSC
> - Carregar uma configuração para a automatização do Azure
> - Compilar uma configuração numa configuração de nó
> - Atribuir uma configuração de nó a um nó gerenciado
> - Verificar o estado de conformidade de um nó gerido

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM do Azure Resource Manager (não clássica) com o Windows Server 2008 R2 ou posterior. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- O Azure PowerShell versão 3.6 ou posterior do módulo. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a Desired State Configuration (DSC). Para obter informações sobre o DSC, consulte [Windows PowerShell Desired State Configuration descrição geral](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração para automatização do Azure

Para este tutorial, utilizamos uma simple configuração de DSC que assegura que o IIS é instalado na VM.

Para obter informações sobre configurações de DSC, veja [DSC configurations (Configurações de DSC)](/powershell/dsc/configurations).

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

Chamar o `Import-AzureRmAutomationDscConfiguration` cmdlet para carregar a configuração para a sua conta de automatização:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração numa configuração de nó

Uma configuração de DSC deve ser compilada numa configuração de nó, antes que possa ser atribuído a um nó.

Para obter informações sobre como compilar configurações, consulte [configurações de DSC](/powershell/dsc/configurations).

Chamar o `Start-AzureRmAutomationDscCompilationJob` cmdlet para compilar o `TestConfig` configuração numa configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Esta ação cria uma configuração de nó com o nome `TestConfig.WebServer` na conta de automatização.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registe-se de uma VM para ser gerido pela configuração de estado

Pode utilizar a configuração de estado de automatização do Azure para gerir VMs do Azure (clássicas e do Resource Manager), VMs no local, máquinas Linux, AWS VMs e máquinas físicas no local. Neste tópico, vamos abordar como registar-se apenas as VMs do Azure Resource Manager. Para obter informações sobre como registar a outros tipos de máquinas, consulte [integrar computadores para gestão de configuração de estado de automatização do Azure](automation-dsc-onboarding.md).

Chamar o `Register-AzureRmAutomationDscNode` cmdlet para registar a sua VM com a configuração de estado de automatização do Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

A VM especificada é registada como um nó gerenciado na configuração de estado.

### <a name="specify-configuration-mode-settings"></a>Especificar definições do modo de configuração

Quando registra uma VM como um nó gerenciado, também pode especificar propriedades da configuração. Por exemplo, pode especificar que o estado da máquina está a ser aplicada apenas uma vez (DSC não tenta aplicar a configuração após a verificação inicial), especificando `ApplyOnly` como o valor do **ConfigurationMode** propriedade :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Também pode especificar a frequência com que o DSC verifica o estado de configuração ao utilizar o **ConfigurationModeFrequencyMins** propriedade:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Para obter mais informações sobre como definir propriedades de configuração para um nó gerenciado, consulte [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Para obter mais informações sobre definições de configuração de DSC, veja [configurar o Gestor de configuração Local](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora podemos atribuir a configuração do nó compilados para a VM que pretende configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Esta ação atribui a configuração de nó com o nome `TestConfig.WebServer` para o nó de DSC registado com o nome `DscVm`.
Por predefinição, o nó de DSC é analisado relativamente à conformidade com a configuração do nó a cada 30 minutos.
Para obter informações sobre como alterar o intervalo de verificação de conformidade, consulte [configurar o Gestor de configuração Local](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o estado de conformidade de um nó gerido

Pode obter relatórios sobre o estado de conformidade de um nó gerido ao chamar o `Get-AzureRmAutomationDscNodeReport` cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para saber como a carregar nós, consulte [integrar computadores para gestão de configuração de estado de automatização do Azure](automation-dsc-onboarding.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
