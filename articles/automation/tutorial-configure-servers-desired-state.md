---
title: Configurar servidores para um estado pretendido e gerir desvios com a Automatização do Azure
description: Tutorial - Gerir configurações do servidor com configuração do Estado da Automação Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416596"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configure os servidores para um estado desejado e gerea deriva

A Configuração do Estado da Automatização Azure permite especificar configurações para os seus servidores e garantir que esses servidores estão no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - A bordo de um VM a ser gerido pela Azure Automation DSC
> - Faça upload de uma configuração para a Automação Azure
> - Compilar uma configuração numa configuração de nó
> - Atribuir uma configuração de nó a um nó gerido
> - Verifique o estado de conformidade de um nó gerido

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- Um VM (não clássico) do Gestor de Recursos Azure que executa o Windows Server 2008 R2 ou mais tarde. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Versão 3.6 ou mais posterior do módulo Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com configuração de estado desejada (DSC). Para obter informações sobre o DSC, consulte a [visão geral de configuração do Estado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração para a Automação Azure

Para este tutorial, utilizaremos uma configuração DSC simples que garante que o IIS está instalado no VM.

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
> Em cenários mais avançados onde é necessário importar vários módulos que forneçam Recursos `Import-DscResource` DSC, certifique-se de que cada módulo tem uma linha única na sua configuração.

Ligue `Import-AzureRmAutomationDscConfiguration` para o cmdlet para fazer o upload da configuração para a sua conta Desmótica:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração numa configuração de nó

Uma configuração DSC deve ser compilada numa configuração de nó antes de ser atribuída a um nó.

Para obter informações sobre a compilação de configurações, consulte [as configurações do DSC](/powershell/scripting/dsc/configurations/configurations).

Ligue `Start-AzureRmAutomationDscCompilationJob` para o cmdlet `TestConfig` para compilar a configuração numa configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Isto cria uma configuração de nó nomeada `TestConfig.WebServer` na sua conta Deautomação.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registe um VM a ser gerido pela Configuração do Estado

Pode utilizar a Configuração do Estado da Automação Azure para gerir Os VMs Azure (tanto Classic como Resource Manager), VMs no local, máquinas Linux, VMs AWS e máquinas físicas no local. Neste tópico, cobrimos como registar apenas VMs do Gestor de Recursos Azure. Para obter informações sobre o registo de outros tipos de máquinas, consulte máquinas de [embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

Ligue `Register-AzureRmAutomationDscNode` para o cmdlet para registar o seu VM com a Configuração do Estado da Automação Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Isto regista o VM especificado como um nó gerido na Configuração do Estado.

### <a name="specify-configuration-mode-settings"></a>Especificar definições de modo de configuração

Ao registar um VM como nó gerido, também pode especificar propriedades da configuração. Por exemplo, pode especificar que o estado da máquina deve ser aplicado apenas uma vez (o DSC não tenta aplicar a configuração após a verificação inicial) especificando `ApplyOnly` como o valor da propriedade Modo de **Configuração:**

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Também pode especificar com que frequência o DSC verifica o estado de configuração utilizando a propriedade **ConfigurationModeFrequencyMins:**

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Para obter mais informações sobre a definição de propriedades de configuração para um nó gerido, consulte [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Para obter mais informações sobre as definições de configuração do DSC, consulte [configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaConfig)de Configuração Local .

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerido

Agora podemos atribuir a configuração do nó compilada ao VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isto atribui a configuração `TestConfig.WebServer` do nó nomeada para `DscVm`o nó DSC registado chamado .
Por predefinição, o nó DSC é verificado para o cumprimento da configuração do nó a cada 30 minutos.
Para obter informações sobre como alterar o intervalo de verificação de conformidade, consulte [Configurar o Gestor](/powershell/scripting/dsc/managing-nodes/metaConfig)de Configuração Local .

## <a name="working-with-partial-configurations"></a>Trabalhar com Configurações Parciais

A Configuração do Estado da Automação Azure suporta o uso de [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs).
Neste cenário, o DSC está configurado para gerir várias configurações de forma independente, e cada configuração é recuperada da Automação Azure.
No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação.
Isto significa que se estiver a usar duas configurações para um nó, necessitará de duas contas de automação.

Para mais detalhes sobre como registar uma configuração parcial do serviço pull, consulte a documentação para [configurações parciais](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipas podem trabalhar em conjunto para gerir colaborativamente os servidores usando a configuração como código ver [Understanding DSC papel em um pipeline CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verifique o estado de conformidade de um nó gerido

Você pode obter relatórios sobre o estado de `Get-AzureRmAutomationDscNodeReport` conformidade de um nó gerido, chamando o cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Remoção de nódosos de serviço

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

- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender a bordo de nós, consulte [máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/azurerm.automation/#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
