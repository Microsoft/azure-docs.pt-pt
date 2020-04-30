---
title: Configurar servidores para um estado pretendido e gerir desvios com a Automatização do Azure
description: Tutorial - Gerir configurações do servidor com configuração do Estado da Automação Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678697"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configure os servidores para um estado desejado e gerea deriva

A Configuração do Estado da Automatização Azure permite especificar configurações para os seus servidores e garantir que esses servidores estão no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - A bordo de um VM a ser gerido pela Azure Automation DSC
> - Faça upload de uma configuração para a Automação Azure
> - Compilar uma configuração numa configuração de nó
> - Atribuir uma configuração de nó a um nó gerido
> - Verifique o estado de conformidade de um nó gerido

Para este tutorial, utilizamos uma [configuração DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante que o IIS está instalado no VM.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- Um VM (não clássico) do Gestor de Recursos Azure executa o Windows Server 2008 R2 ou mais tarde. Para obter instruções sobre a criação de um VM, consulte [Criar a sua primeira máquina virtual Windows no portal Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Versão 3.6 ou mais posterior do módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com configuração de estado desejada (DSC). Para obter informações sobre o DSC, consulte a [visão geral de configuração do Estado do Windows PowerShell.](/powershell/scripting/dsc/overview/overview)

## <a name="support-for-partial-configurations"></a>Suporte para configurações parciais

A Configuração do Estado da Automação Azure suporta a utilização de [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs). Neste cenário, o DSC está configurado para gerir várias configurações de forma independente, e cada configuração é recuperada da Automação Azure. No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação. Isto significa que se estiver a utilizar duas configurações para um nó, necessitará de duas contas de Automação.

Para mais detalhes sobre como registar uma configuração parcial de um serviço de pull, consulte a documentação para [configurações parciais](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipas podem trabalhar em conjunto para gerir colaborativamente os servidores usando a configuração como código, consulte o [papel de Understanding DSC num pipeline CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na subscrição do Azure com o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração para a Automação Azure


Num editor de texto, escreva o seguinte e guarde-o localmente como **TestConfig.ps1**.

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
> Em cenários mais avançados onde é necessário importar vários módulos que forneçam Recursos `Import-DscResource` DSC, certifique-se de que cada módulo tem uma linha única na sua configuração.

Ligue para o [cmdlet Import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) para fazer o upload da configuração para a sua conta Deautomação.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração numa configuração de nó

Uma configuração DSC deve ser compilada numa configuração de nó antes de ser atribuída a um nó. Ver [configurações DSC](/powershell/scripting/dsc/configurations/configurations).

Ligue para o [Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) cmdlet para compilar `TestConfig` `TestConfig.WebServer` a configuração numa configuração de nó nomeada na sua conta Automation.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registe um VM a ser gerido pela Configuração do Estado

Pode utilizar a Configuração do Estado da Automação Azure para gerir Os VMs Azure (tanto Classic como Resource Manager), VMs no local, máquinas Linux, VMs AWS e máquinas físicas no local. Neste tópico, cobrimos como registar apenas VMs do Gestor de Recursos Azure. Para obter informações sobre o registo de outros tipos de máquinas, consulte máquinas de [embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

Ligue para o [Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) cmdlet para registar o seu VM com a Configuração do Estado de Automação Azure como um nó gerido. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Especificar definições de modo de configuração

Utilize o [cmdlet Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) para registar um VM como nó gerido e especificar propriedades de configuração. Por exemplo, pode especificar que o estado da máquina deve `ApplyOnly` ser aplicado apenas uma vez, especificando como o valor do `ConfigurationMode` imóvel. A configuração do Estado não tenta aplicar a configuração após a verificação inicial.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Também pode especificar com que frequência o DSC verifica o estado de configuração utilizando a `ConfigurationModeFrequencyMins` propriedade. Para obter mais informações sobre as definições de configuração do DSC, consulte [configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaConfig)de Configuração Local .

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerido

Agora podemos atribuir a configuração do nó compilada ao VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isto atribui a configuração `TestConfig.WebServer` do nó nomeada ao `DscVm`nó DSC registado . Por predefinição, o nó DSC é verificado para o cumprimento da configuração do nó a cada 30 minutos. Para obter informações sobre como alterar o intervalo de verificação de conformidade, consulte [Configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaConfig)de Configuração Local .

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verifique o estado de conformidade de um nó gerido

Pode obter relatórios sobre o estado de conformidade de um nó gerido utilizando o [cmdlet Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Remover os nódosos do serviço

Quando adicionar um nó à Configuração do Estado da Automação Azure, as definições no Gestor de Configuração Local estão definidas para se registarem com as configurações de serviço e de puxar e os módulos necessários para configurar a máquina.
Se optar por remover o nó do serviço, pode fazê-lo utilizando o portal Azure ou os cmdlets Az.

> [!NOTE]
> O desregistar um nó do serviço apenas define as definições do Gestor de Configuração Local para que o nó já não esteja ligado ao serviço.
> Isto não afeta a configuração que é atualmente aplicada ao nó.
> Para remover a configuração atual, utilize o [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) ou elimine o ficheiro de configuração local (esta é a única opção para os nós linux).

### <a name="azure-portal"></a>Portal do Azure

A partir da Automação Azure, clique na **configuração do Estado (DSC)** na tabela de conteúdos.
Clique em **Nódosos** para ver a lista de nódosos que estão registados no serviço.
Clique no nome do nó que deseja remover.
Na vista nó que se abre, clique **em Desregistar**.

### <a name="powershell"></a>PowerShell

Para desregistar um nó do serviço de configuração do Estado da Automatização Azure utilizando o PowerShell, siga a documentação para o cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja O Início com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md).
- Para aprender a bordo dos nós, consulte [máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [configurações de compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para referência de cmdlet PowerShell, consulte [os cmdlets](/powershell/module/azurerm.automation/#automation)de configuração do Estado da Automatização Do Azure .
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automatização do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
