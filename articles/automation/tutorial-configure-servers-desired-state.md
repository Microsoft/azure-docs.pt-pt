---
title: Configurar servidores para um estado pretendido e gerir desvios com a Automatização do Azure
description: Tutorial – gerenciar configurações de servidor com a configuração de estado de automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 09ba4bc9e5ac496a7d1d65ff145d56818e53116e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243344"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado desejado e gerenciar descompasso

A configuração de estado da automação do Azure permite que você especifique configurações para seus servidores e verifique se esses servidores estão no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - Carregar uma VM a ser gerenciada pelo Azure DSC de Automação
> - Carregar uma configuração para a automação do Azure
> - Compilar uma configuração em uma configuração de nó
> - Atribuir uma configuração de nó a um nó gerenciado
> - Verificar o status de conformidade de um nó gerenciado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, irá precisar de:

- Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM Azure Resource Manager (não clássica) que executa o Windows Server 2008 R2 ou posterior. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell módulo versão 3,6 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a DSC (configuração de estado desejado). Para obter informações sobre DSC, consulte [visão geral da configuração de estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overviews)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração para a automação do Azure

Para este tutorial, usaremos uma configuração DSC simples que garante que o IIS esteja instalado na VM.

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
> Em cenários mais avançados em que você precisa que vários módulos sejam importados para fornecer recursos de DSC, certifique-se de que cada módulo tenha uma linha `Import-DscResource` exclusiva em sua configuração.

Chame o cmdlet `Import-AzureRmAutomationDscConfiguration` para carregar a configuração em sua conta de automação:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração em uma configuração de nó

Uma configuração DSC deve ser compilada em uma configuração de nó antes que possa ser atribuída a um nó.

Para obter informações sobre como compilar configurações, consulte [configurações de DSC](/powershell/scripting/dsc/configurations/configurations).

Chame o cmdlet `Start-AzureRmAutomationDscCompilationJob` para compilar a configuração de `TestConfig` em uma configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Isso cria uma configuração de nó chamada `TestConfig.WebServer` em sua conta de automação.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrar uma VM a ser gerenciada pela configuração de estado

Você pode usar a configuração de estado da automação do Azure para gerenciar VMs do Azure (clássicas e Resource Manager), VMs locais, máquinas Linux, VMs AWS e máquinas físicas locais. Neste tópico, abordaremos como registrar somente Azure Resource Manager VMs. Para obter informações sobre como registrar outros tipos de computadores, consulte [máquinas de integração para o gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).

Chame o cmdlet `Register-AzureRmAutomationDscNode` para registrar sua VM com a configuração de estado da automação do Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Isso registra a VM especificada como um nó gerenciado na configuração de estado.

### <a name="specify-configuration-mode-settings"></a>Especificar configurações do modo de configuração

Ao registrar uma VM como um nó gerenciado, você também pode especificar as propriedades da configuração. Por exemplo, você pode especificar que o estado do computador deve ser aplicado apenas uma vez (o DSC não tenta aplicar a configuração após a verificação inicial) especificando `ApplyOnly` como o valor da propriedade **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Você também pode especificar a frequência com que a DSC verifica o estado de configuração usando a propriedade **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Para obter mais informações sobre como definir propriedades de configuração para um nó gerenciado, consulte [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Para obter mais informações sobre definições de configuração DSC, consulte [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora, podemos atribuir a configuração de nó compilado à VM que desejamos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isso atribui a configuração de nó denominada `TestConfig.WebServer` ao nó DSC registrado denominado `DscVm`.
Por padrão, o nó DSC é verificado quanto à conformidade com a configuração de nó a cada 30 minutos.
Para obter informações sobre como alterar o intervalo de verificação de conformidade, consulte [Configurando o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Trabalhando com configurações parciais

A configuração de estado da automação do Azure dá suporte ao uso de [configurações parciais](/powershell/dsc/pull-server/partialconfigs).
Nesse cenário, a DSC é configurada para gerenciar várias configurações de forma independente e cada configuração é recuperada da automação do Azure.
No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação.
Isso significa que se você estiver usando duas configurações para um nó, precisará de duas contas de automação.

Para obter detalhes sobre como registrar uma configuração parcial do serviço de pull, consulte a documentação para [configurações parciais](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipes podem trabalhar em conjunto para gerenciar servidores de forma colaborativa usando a configuração como código, consulte [noções básicas sobre a função do DSC em um pipeline de CI/CD](/powershell/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o status de conformidade de um nó gerenciado

Você pode obter relatórios sobre o status de conformidade de um nó gerenciado chamando o cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Removendo nós do serviço

Quando você adiciona um nó à configuração de estado da automação do Azure, as configurações no local Configuration Manager são definidas para registrar com o serviço e as configurações de pull e os módulos necessários para configurar o computador.
Se você optar por remover o nó do serviço, poderá fazer isso usando os cmdlets portal do Azure ou AZ.

> [!NOTE]
> O cancelamento do registro de um nó do serviço define apenas as configurações de Configuration Manager locais para que o nó não se conecte mais ao serviço.
> Isso não afeta a configuração atualmente aplicada ao nó.
> Para remover a configuração atual, use o [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) ou exclua o arquivo de configuração local (essa é a única opção para nós do Linux).

### <a name="azure-portal"></a>Portal do Azure

Na automação do Azure, clique em **configuração de estado (DSC)** no sumário.
Em seguida, clique em **nós** para exibir a lista de nós que estão registrados com o serviço.
Clique no nome do nó que você deseja remover.
Na exibição de nó que é aberta, clique em **Cancelar registro**.

### <a name="powershell"></a>PowerShell

Para cancelar o registro de um nó do serviço de configuração de estado da automação do Azure usando o PowerShell, siga a documentação do cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber como integrar nós, confira [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
