---
title: Configure máquinas para um estado desejado na Automação Azure
description: Este artigo diz como configurar as máquinas para um estado desejado utilizando a Configuração do Estado da Automação Azure.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 55c7522ad1dc6c7f91fae608a777dab3cd67d2ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86183175"
---
# <a name="configure-machines-to-a-desired-state"></a>Configurar computadores para um estado pretendido

A Azure Automation State Configuration permite especificar configurações para os seus servidores e garantir que esses servidores estão no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - A bordo de um VM a ser gerido pela Azure Automation DSC
> - Faça o upload de uma configuração para Azure Automation
> - Compilar uma configuração num nó
> - Atribua uma configuração de nó a um nó gerido
> - Verifique o estado de conformidade de um nó gerido

Para este tutorial, utilizamos uma [configuração DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante que o IIS está instalado no VM.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](./manage-runas-account.md).
- Um VM (não clássico) do Gestor de Recursos Azure (não clássico) que executa o Windows Server 2008 R2 ou mais tarde. Para obter instruções sobre a criação de um VM, consulte [Criar a sua primeira máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell módulo versão 3.6 ou mais tarde. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a Configuração do Estado Desejado (DSC). Para obter informações sobre o DSC, consulte [a visão geral da configuração desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Suporte para configurações parciais

A Azure Automation State Configuration suporta a utilização de [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs). Neste cenário, o DSC está configurado para gerir várias configurações de forma independente, e cada configuração é recuperada a partir da Azure Automation. No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação. Isto significa que se estiver a utilizar duas configurações para um nó, necessitará de duas contas de Automação.

Para obter detalhes sobre como registar uma configuração parcial a partir de um serviço de pull, consulte a documentação para [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipas podem trabalhar em conjunto para gerir colaborativamente servidores usando a configuração como código, consulte [o papel do Understanding DSC num Pipeline CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição Azure com o [cmdlet Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração para a Azure Automation


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
> Em cenários mais avançados onde necessita de vários módulos que fornecem Recursos DSC, certifique-se de que cada módulo tem uma linha única `Import-DscResource` na sua configuração.

Ligue para o [cmdlet Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) para carregar a configuração na sua conta Automation.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração num nó

Uma configuração DSC deve ser compilada numa configuração de nó antes de poder ser atribuída a um nó. Ver [configurações de DSC](/powershell/scripting/dsc/configurations/configurations).

Ligue para o [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) cmdlet para compilar a `TestConfig` configuração de um nó nomeado `TestConfig.WebServer` na sua conta Demôm automação.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registar um VM a ser gerido por Configuração do Estado

Pode utilizar a Configuração do Estado da Automação Azure para gerir VMs Azure (Classic e Resource Manager), VMs no local, máquinas Linux, VMs AWS e máquinas físicas no local. Neste tópico, cobrimos como registar apenas VMs Azure Resource Manager. Para obter informações sobre o registo de outros tipos de máquinas, consulte [máquinas de embarque para gestão através da Azure Automation State Configuration](automation-dsc-onboarding.md).

Ligue para o [cmdlet Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) para registar o seu VM com a Configuração do Estado da Automação Azure como um nó gerido. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Especifique as definições do modo de configuração

Utilize o [cmdlet Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) para registar um VM como nó gerido e especifique propriedades de configuração. Por exemplo, pode especificar que o estado da máquina deve ser aplicado apenas uma vez, especificando `ApplyOnly` como o valor do `ConfigurationMode` imóvel. A Configuração do Estado não tenta aplicar a configuração após a verificação inicial.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Também pode especificar com que frequência o DSC verifica o estado de configuração utilizando a `ConfigurationModeFrequencyMins` propriedade. Para obter mais informações sobre as definições de configuração do DSC, consulte [configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribua uma configuração de nó a um nó gerido

Agora podemos atribuir a configuração do nó compilado para o VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isto atribui a configuração do nó nomeado `TestConfig.WebServer` para o nó DSC registado `DscVm` . Por predefinição, o nó DSC é verificado para o cumprimento da configuração do nó a cada 30 minutos. Para obter informações sobre como alterar o intervalo de verificação de conformidade, consulte [configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verifique o estado de conformidade de um nó gerido

Pode obter relatórios sobre o estado de conformidade de um nó gerido utilizando o [cmdlet Get-AzAutomationDscNodeReport.](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Remover os nódes do serviço

Quando adiciona um nó à Configuração do Estado da Automação Azure, as definições no Gestor de Configurações Locais estão definidas para registar-se com o serviço e puxar as configurações e módulos necessários para configurar a máquina.
Se optar por remover o nó do serviço, pode fazê-lo utilizando o portal Azure ou os cmdlets Az.

> [!NOTE]
> A não registo de um nó do serviço apenas define as definições do Gestor de Configuração Local para que o nó não esteja mais ligado ao serviço.
> Isto não afeta a configuração que é atualmente aplicada ao nó.
> Para remover a configuração atual, utilize o [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) ou elimine o ficheiro de configuração local (esta é a única opção para os nosdes Linux).

### <a name="azure-portal"></a>Portal do Azure

A partir da Azure Automation, clique na **configuração do Estado (DSC)** na tabela de conteúdos.
Em seguida clique **em Nodes** para ver a lista de nós que estão registados no serviço.
Clique no nome do nó que deseja remover.
Na vista nó que se abre, clique em **Unregister**.

### <a name="powershell"></a>PowerShell

Para não registar um nó do serviço de configuração do Estado da Automação Azure utilizando o PowerShell, siga a documentação para o cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para aprender a ativar os nós, consulte [Ativar a Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
