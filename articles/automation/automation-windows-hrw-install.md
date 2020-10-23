---
title: Implementar um trabalhador de runbook híbrido do Windows na Azure Automation
description: Este artigo diz como implementar um Trabalhador De Runbook Híbrido que pode usar para executar livros em máquinas baseadas no Windows no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: a03d14fa272f5f86af1caf0ce9537bbb186d13cc
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204524"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementar um trabalhador de runbook híbrido windows

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar runbooks diretamente na máquina que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. A Azure Automation armazena e gere os runbooks e depois entrega-os a uma ou mais máquinas designadas. Este artigo descreve como implementar um Trabalhador De Runbook Híbrido numa máquina Windows, como remover o trabalhador e como remover um grupo híbrido de runbook worker.

Depois de implementar com sucesso um trabalhador de runbook, [reveja os runbooks de runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter o seguinte.

### <a name="a-log-analytics-workspace"></a>Um espaço de trabalho Log Analytics

O papel de Trabalhador de Runbook Híbrido depende de um espaço de trabalho Azure Monitor Log Analytics para instalar e configurar o papel. Pode criá-lo através [do Azure Resource Manager,](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)através [do PowerShell,](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)ou no [portal Azure](../azure-monitor/learn/quick-create-workspace.md).

Se não tiver um espaço de trabalho do Azure Monitor Log Analytics, reveja a orientação de [design do Registo do Monitor Azure](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

Se tiver um espaço de trabalho, mas não estiver ligado à sua conta de Automação, permitir uma funcionalidade de Automação adiciona funcionalidade para a Azure Automation, incluindo suporte para o Trabalhador de Runbook Híbrido. Quando ativa uma das funcionalidades da Azure Automation no seu espaço de trabalho Log Analytics, especificamente [Gestão de Atualização](update-management/update-mgmt-overview.md) ou [Rastreio e Inventário de Alterações,](change-tracking/overview.md)os componentes do trabalhador são automaticamente empurrados para a máquina do agente.

> [!NOTE]
> Ao ativar a funcionalidade de Gestão de Atualização ou De Rastreio e Inventário de Alterações, a Azure Automation suporta apenas determinadas regiões para ligar um espaço de trabalho log Analytics e uma conta Demômes. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md) Antes de ativar qualquer recurso, reveja as informações de preços do [Azure](https://azure.microsoft.com/pricing/details/automation/) para a Azure Automation.

   Para adicionar a função de Gestão de Atualização ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Para adicionar a função de Rastreio e Inventário de Alterações ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agente do Log Analytics

A função De Trabalhador de Runbook Híbrido requer o [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) para o sistema operativo Windows suportado.

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows suportado

As seguintes versões do sistema operativo Windows são oficialmente suportadas para um Trabalhador de Runbook Híbrido Do Windows:

* Windows Server 2019
* Windows Server 2016, versão 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (incluindo multi-sessão) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um Trabalhador de Runbook Híbrido do Windows são:

* Windows PowerShell 5.1 ou posteriormente[(baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porto 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter mais requisitos de networking para o Trabalhador de Runbook Híbrido, consulte [configurar a sua rede.](automation-hybrid-runbook-worker.md#network-planning)

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Adicionar uma máquina a um grupo híbrido de trabalhadores de runbook

Pode adicionar a máquina de trabalhadores a um grupo híbrido de trabalhadores na sua conta de Automação. Note que deve apoiar os runbooks da Automação desde que utilize a mesma conta tanto para a funcionalidade Azure Automation como para a associação do grupo Híbrido Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

>[!NOTE]
>Permitir que a Azure Automation [Update Management](update-management/update-mgmt-overview.md) configura automaticamente qualquer máquina Windows que esteja ligada ao seu espaço de trabalho Log Analytics como um Trabalhador De Runbook Híbrido para suportar a gestão das suas atualizações do sistema operativo. No entanto, este trabalhador não está registado em nenhum grupo híbrido de trabalhadores de runbook já definidos na sua conta Automation.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Habilitação de máquinas para gestão com Configuração do Estado da Automação Azure

Para obter informações sobre a ativação de máquinas para gestão com a Configuração do Estado da Automação Azure, consulte [Ativar máquinas para gestão através da Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

> [!NOTE]
> Para gerir a configuração das máquinas que suportam a função de Trabalhador de Runbook Híbrido com Configuração de Estado Desejada (DSC), deve adicionar as máquinas como nós DSC.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Opções de instalação do Trabalhador runbook híbrido do Windows Hybrid

Para instalar e configurar um Trabalhador de Runbook Híbrido do Windows, pode utilizar um dos seguintes métodos.

* Para os VMs Azure, instale o agente Log Analytics para Windows utilizando a [extensão da máquina virtual para](../virtual-machines/extensions/oms-windows.md)o Windows . A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho do Log Analytics existente utilizando um modelo de Gestor de Recursos Azure ou PowerShell. Uma vez instalado o agente, o VM pode ser adicionado a um grupo híbrido de runbook worker na sua conta Automation.

* Para VMs não Azure, instale o agente Log Analytics para Windows utilizando as opções de implementação descritas nos computadores Connect Windows para o artigo [do Azure Monitor.](../azure-monitor/platform/agent-windows.md) Pode repetir este processo para que várias máquinas adicione vários trabalhadores ao seu ambiente. Uma vez instalado o agente, os VMs podem ser adicionados a um grupo híbrido de Runbook Worker na sua conta Automation.

* Utilize um script PowerShell fornecido para [automatizar](#automated-deployment) completamente o processo de configuração de uma ou mais máquinas Windows. Este é o método recomendado para máquinas no seu datacenter ou outro ambiente em nuvem.

## <a name="automated-deployment"></a>Implementação automatizada

Na máquina-alvo, execute os seguintes passos para automatizar a instalação e configuração da função do Trabalhador Híbrido do Windows utilizando o script PowerShell **New-OnPremiseHybridWorker.ps1**. O script realiza os passos seguintes:

* Instala os módulos necessários
* Assina com a sua conta Azure
* Verifica a existência de grupo de recursos especificados e conta de Automação
* Cria referências a atributos de conta de Automação
* Cria um espaço de trabalho Azure Monitor Log Analytics se não for especificado
* Ativar a solução Azure Automation no espaço de trabalho
* Descarregue e instale o agente Log Analytics para windows
* Registe a máquina como Trabalhador de Runbook Híbrido

### <a name="step-1---download-the-powershell-script"></a>Passo 1 - Descarregue o script PowerShell

Descarregue ** o **New-OnPremiseHybridWorker.ps1script da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Depois de ter descarregado o script, copie-o ou execute-o na máquina alvo. O **New-OnPremiseHybridWorker.ps1** script utiliza os parâmetros descritos abaixo durante a execução.

| Parâmetro | Estado | Descrição |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos associado à sua conta de Automação. |
| `AutomationAccountName` | Obrigatório | O nome da sua conta de Automação.
| `Credential` | Opcional | As credenciais a utilizar ao iniciar sessão no ambiente Azure. |
| `HybridGroupName` | Obrigatório | O nome de um grupo híbrido de trabalho de runbook que você especifica como um alvo para os runbooks que suportam este cenário. |
| `OMSResourceGroupName` | Opcional | O nome do grupo de recursos para o espaço de trabalho Log Analytics. Se este grupo de recursos não for especificado, o valor de `AAResourceGroupName` é utilizado. |
| `SubscriptionID` | Obrigatório | O identificador da subscrição Azure associado à sua conta Automation. |
| `TenantID` | Opcional | O identificador da organização de inquilinos associado à sua conta de Automação. |
| `WorkspaceName` | Opcional | O nome do espaço de trabalho Log Analytics. Se não tiver um espaço de trabalho log analytics, o script cria e configura um. |

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Passo 2 - Open Windows PowerShell comando shell

A partir do **Menu Iniciar** clique **em Iniciar,** digite **PowerShell,** clique com o botão direito **Windows PowerShell**e, em seguida, clique em **Executar como administrador**.

### <a name="step-3---run-the-powershell-script"></a>Passo 3 - Executar o script PowerShell

Na linha de comando PowerShell, navegue na pasta que contém o script que descarregou. Alterar os valores para os `AutomationAccountName` parâmetros, , , , , e `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Então, executar o guião.

É-lhe pedido que autentica com o Azure depois de publicar o guião. Tem de iniciar scontabilidade com uma conta que é membro da função de Administrador de Subscrição e coadministradora da subscrição.

```powershell-interactive
$NewOnPremiseHybridWorkerParameters = @{
  AutomationAccountName = <nameOfAutomationAccount>
  AAResourceGroupName   = <nameOfResourceGroup>
  OMSResourceGroupName  = <nameOfResourceGroup>
  HybridGroupName       = <nameOfHRWGroup>
  SubscriptionID        = <subscriptionId>
  WorkspaceName         = <nameOfLogAnalyticsWorkspace>
}
.\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
```

### <a name="step-4---install-nuget"></a>Passo 4 - Instalar nuGet

É-lhe pedido que concorde em instalar o NuGet e autenticar com as suas credenciais Azure. Se não tiver a versão mais recente do NuGet, pode descarregá-la a partir das [versões de distribuição disponíveis do NuGet.](https://www.nuget.org/downloads)

### <a name="step-5---verify-the-deployment"></a>Passo 5 - Verificar a implantação

Depois de terminado o guião, a página do Grupo operário Híbrido na sua conta Automation mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo na lista na página do Grupo operário híbrido e escolher o azulejo **híbrido operário.** Na página "Trabalhadores Híbridos", pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implementação manual

Para instalar e configurar um Trabalhador de Runbook Híbrido do Windows, execute os seguintes passos.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Passo 1 - Verifique se o agente está a reportar-se ao espaço de trabalho

O agente Log Analytics para Windows liga as máquinas a um espaço de trabalho Azure Monitor Log Analytics. Quando instala o agente na sua máquina e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador de Runbook Híbrido.

Quando o agente tiver ligado com sucesso ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, deverá consultar os registos de batimentos cardíacos da máquina, indicando que está ligado e a reportar ao serviço. Por defeito, cada agente encaminha um registo de batimentos cardíacos para o seu espaço de trabalho atribuído. Utilize os seguintes passos para completar a instalação e configuração do agente.

1. Ativar a função para adicionar a máquina do agente. Para a Gestão de Atualizações e VMs Azure, consulte [Ativar a Gestão de Atualização a partir de uma conta de Automação](update-management/update-mgmt-enable-automation-account.md), Ative a Gestão de [Atualizações navegando no portal Azure](update-management/update-mgmt-enable-portal.md), [Enable Update Management a partir de um runbook](update-management/update-mgmt-enable-runbook.md), ou Enable Update Management a partir de um [VM Azure](update-management/update-mgmt-enable-vm.md). Para Alter tracking e VMs Azure, consulte [Enable Azure VMs](change-tracking/enable-from-automation-account.md#enable-azure-vms), e para VMs não-Azure ver [Ativar máquinas no espaço de trabalho](change-tracking/enable-from-automation-account.md#enable-machines-in-the-workspace).

2. Para confirmar a versão do Trabalhador de Runbook Híbrido, navegue `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e note a subpasta da **versão.**

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passo 2 - Instale o ambiente do livro de correr e ligue-se à Azure Automation

Quando configura um agente para reportar a um espaço de trabalho log Analytics, a função Azure Automation empurra para baixo o `HybridRegistration` módulo PowerShell, que contém o `Add-HybridRunbookWorker` cmdlet. Utilize este cmdlet para instalar o ambiente do livro de recortes na máquina e registá-lo com a Azure Automation.

Abra uma sessão PowerShell no modo administrador e execute os seguintes comandos para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora, executar o `Add-HybridRunbookWorker` cmdlet utilizando a seguinte sintaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

Pode obter as informações necessárias para os parâmetros `Url` e a partir da página `Key` **Chaves** na sua conta Dem automação. Selecione **Teclas** na secção **de definições** de conta a partir do lado esquerdo da página.

![Gerir página Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o `Url` parâmetro, copie o valor para **URL**.

* Para o `Key` parâmetro, copie o valor **da chave de acesso primário**.

* Para o `GroupName` parâmetro, utilize o nome do grupo híbrido Runbook Worker. Se este grupo já existir na conta Automation, a máquina atual é-lhe adicionada. Se este grupo não existe, é adicionado.

* Se necessário, desa estale o `Verbose` parâmetro para receber detalhes sobre a instalação.

### <a name="step-3----install-powershell-modules"></a>Passo 3 - Instalar módulos PowerShell

Os runbooks podem utilizar qualquer uma das atividades e cmdlets definidos nos módulos instalados no seu ambiente Azure Automation. Uma vez que estes módulos não são automaticamente implantados em máquinas no local, deve instalá-los manualmente. A exceção é o módulo Azure. Este módulo é instalado por predefinição e fornece acesso a cmdlets para todos os serviços e atividades da Azure Automation.

Como o principal objetivo do Trabalhador de Runbook Híbrido é gerir os recursos locais, é provável que seja necessário instalar os módulos que suportam estes recursos, nomeadamente o `PowerShellGet` módulo. Para obter informações sobre a instalação de módulos Windows PowerShell, consulte [o Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar num local referenciado pela `PSModulePath` variável ambiental para que o trabalhador híbrido possa importar automaticamente. Para obter mais informações, consulte [instalar módulos em PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remova o Trabalhador de Runbook Híbrido de uma máquina Windows no local

1. No portal Azure, aceda à sua conta de Automação.

2. Em **Definições de Conta**, selecione **Teclas** e note os valores para **URL** e **Tecla de acesso primário**.

3. Abra uma sessão PowerShell no modo Administrador e execute o seguinte comando com o seu URL e valores de chave de acesso primário. Utilize o `Verbose` parâmetro para um registo detalhado do processo de remoção. Para remover máquinas velhas do seu grupo Operário Híbrido, utilize o `machineName` parâmetro opcional.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido de runbook worker, primeiro precisa remover o Trabalhador de Runbook Híbrido de todas as máquinas que são membros do grupo. Em seguida, utilize os seguintes passos para remover o grupo:

1. Abra a conta Automation no portal Azure.

2. Selecione **grupos de trabalhadores híbridos** em **Automação de Processos.** Selecione o grupo que pretende eliminar. A página de propriedades para este grupo aparece.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Delete**. Uma mensagem pede-lhe que confirme esta ação. Selecione **Sim** se tiver a certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este processo pode demorar vários segundos a terminar. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem, consulte [runbooks Runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md).

* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas.](troubleshoot/hybrid-runbook-worker.md#general)
