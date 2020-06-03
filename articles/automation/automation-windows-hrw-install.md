---
title: Implementar um trabalhador de runbook híbrido do Windows na Azure Automation
description: Este artigo diz como implementar um Trabalhador De Runbook Híbrido que pode usar para executar livros em computadores baseados no Windows no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 8a21f6a58e8dc657d3b60aac33661504363072e2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309922"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementar um trabalhador de runbook híbrido windows

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar runbooks diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. A Azure Automation armazena e gere os runbooks e depois entrega-os a um ou mais computadores designados. Este artigo descreve como implantar um Trabalhador De Runbook Híbrido numa máquina Windows.

Depois de implementar com sucesso um trabalhador de runbook, [reveja os runbooks de runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do Trabalhador do Runbook Híbrido do Windows Hybrid

Para instalar e configurar um Trabalhador de Runbook Híbrido do Windows, pode utilizar um dos seguintes métodos.

* Para os VMs Azure, instale o agente Log Analytics para Windows utilizando a [extensão da máquina virtual para](../virtual-machines/extensions/oms-windows.md)o Windows . A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho do Log Analytics existente utilizando um modelo de Gestor de Recursos Azure ou PowerShell. Uma vez instalado o agente, o VM pode ser adicionado a um grupo híbrido de runbook worker na sua conta Automation. Consulte os passos 3 e 4 na secção [de implantação manual.](#manual-deployment)

* Utilize um livro de funcionamento automation para automatizar completamente o processo de configuração de um computador Windows. Este é o método recomendado para máquinas no seu datacenter ou outro ambiente em nuvem.

* Siga um procedimento passo a passo para instalar e configurar manualmente o papel de Trabalhador do Runbook Híbrido no seu VM não-Azure.

> [!NOTE]
> Para gerir a configuração de servidores que suportam a função de Trabalhador de Runbook Híbrido com Configuração de Estado Desejada (DSC), tem de adicionar os servidores como nós DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisitos mínimos para o Trabalhador de Runbook Híbrido do Windows

Os requisitos mínimos para um Trabalhador de Runbook Híbrido do Windows são:

* Windows Server 2012 ou posterior
* Windows PowerShell 5.1 ou posteriormente[(baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porto 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para obter mais requisitos de networking para o Trabalhador de Runbook Híbrido, consulte [configurar a sua rede.](automation-hybrid-runbook-worker.md#network-planning)

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Permitir a gestão de servidores com Configuração do Estado da Automação Azure

Para obter informações sobre como permitir a gestão de servidores com configuração do Estado da Automação Azure, consulte [Ativar as máquinas para gestão através da Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

Ativar a Azure Automation [Update Management](automation-update-management.md) configura automaticamente qualquer computador Windows que esteja ligado ao seu espaço de trabalho Log Analytics como um Trabalhador de Runbook Híbrido para suportar atualizações de runbook. No entanto, este trabalhador não está registado em nenhum grupo híbrido de trabalhadores de runbook já definidos na sua conta Automation.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Adição do computador a um grupo híbrido de trabalhadores de runbook

Pode adicionar o computador do trabalhador a um grupo híbrido de trabalhadores na sua conta automation. Note que deve apoiar os runbooks da Automação desde que utilize a mesma conta tanto para a funcionalidade Azure Automation como para a associação do grupo Híbrido Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

## <a name="automated-deployment"></a>Implementação automatizada

Na máquina-alvo, execute os seguintes passos para automatizar a instalação e configuração da função de Trabalhador Híbrido do Windows.

### <a name="step-1---download-the-powershell-script"></a>Passo 1 - Descarregue o script PowerShell

Descarregue o script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). O download deve ser diretamente do computador que executa a função De Trabalhador do Runbook Híbrido ou de outro computador no seu ambiente. Quando tiver descarregado o guião, copie-o para o seu trabalhador. O script **New-OnPremiseHybridWorker.ps1** utiliza os parâmetros descritos abaixo durante a execução.

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

> [!NOTE]
> Ao ativar funcionalidades, a Azure Automation suporta apenas certas regiões para ligar um espaço de trabalho Log Analytics e uma conta Automation. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Passo 2 - Open Windows PowerShell comando shell

Abrir **o Windows PowerShell** a partir do ecrã **Iniciar** no modo administrador.

### <a name="step-3---run-the-powershell-script"></a>Passo 3 - Executar o script PowerShell

Na linha de comando PowerShell, navegue na pasta que contém o script que descarregou. Alterar os valores para os `AutomationAccountName` parâmetros, , , , , e `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Então, executar o guião.

É-lhe pedido que autentica com o Azure depois de publicar o guião. Tem de iniciar scontabilidade com uma conta que é membro da função de Administrador de Subscrição e coadministradora da subscrição.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Passo 4 - Instalar nuGet

É-lhe pedido que concorde em instalar o NuGet e autenticar com as suas credenciais Azure. Se não tiver a versão mais recente do NuGet, pode obtê-la a partir das [versões de distribuição NuGet disponíveis.](https://www.nuget.org/downloads)

### <a name="step-5---verify-the-deployment"></a>Passo 5 - Verificar a implantação

Depois de terminado o guião, a página do Grupo operário híbrido mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo na lista na página do Grupo operário híbrido e escolher o azulejo **híbrido operário.** Na página "Trabalhadores Híbridos", pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implementação manual

Na máquina-alvo, execute os dois primeiros passos uma vez para o ambiente de Automação. Em seguida, execute os passos restantes para cada computador do trabalhador.

### <a name="step-1---create-a-log-analytics-workspace"></a>Passo 1 - Criar um espaço de trabalho Log Analytics

Se ainda não tiver um espaço de trabalho log analytics, reveja a orientação de [design do Azure Monitor Log](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Passo 2 - Adicione uma função de Automatização Azure ao espaço de trabalho Log Analytics

Uma funcionalidade de Automação adiciona funcionalidade para a Azure Automation, incluindo suporte para o Trabalhador De Runbook Híbrido. Quando ativa uma função de Automatização Azure no seu espaço de trabalho Log Analytics, os componentes do trabalhador são automaticamente empurrados para o computador do agente.

Para adicionar a função Azure Automation, por exemplo, Gestão de Atualização, ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Passo 3 - Instale o agente Log Analytics para windows

O agente Log Analytics para o Windows liga computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando instala o agente no seu computador e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador de Runbook Híbrido.

Para instalar o agente no computador, siga as instruções dos [computadores Connect Windows para os registos do Monitor Azure](../log-analytics/log-analytics-windows-agent.md). Pode repetir este processo para vários computadores adicionarem vários trabalhadores ao seu ambiente.

Quando o agente tiver ligado com sucesso ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, deverá consultar os registos de batimentos cardíacos para o computador, indicando que está ligado e reportando ao serviço. Por defeito, cada agente encaminha um registo de batimentos cardíacos para o seu espaço de trabalho atribuído. Utilize os seguintes passos para completar a instalação e configuração do agente.

1. Ativar a função para adicionar a máquina do agente. Ver [Ativar as máquinas no espaço de trabalho.](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)
2. Verifique se o agente descarregou corretamente a função Azure Automation. 
3. Para confirmar a versão do Trabalhador de Runbook Híbrido, navegue por **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** e note a subpagação da **versão.**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passo 4 - Instale o ambiente do livro de correr e ligue-se à Azure Automation

Quando configura um agente para reportar a um espaço de trabalho log Analytics, a função Azure Automation empurra para baixo o `HybridRegistration` módulo PowerShell, que contém o `Add-HybridRunbookWorker` cmdlet. Utilize este cmdlet para instalar o ambiente do runbook no computador e registá-lo com a Azure Automation.

Abra uma sessão PowerShell no modo administrador e execute os seguintes comandos para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora, executar o `Add-HybridRunbookWorker` cmdlet utilizando a seguinte sintaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Pode obter as informações necessárias para este cmdlet a partir da página 'Chaves's gerir no portal Azure. Abra esta página selecionando **Chaves** na página Definições na sua conta Demôm automação.

![Gerir página Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o `GroupName` parâmetro, utilize o nome do grupo híbrido Runbook Worker. Se este grupo já existir na conta Automation, o computador atual é adicionado ao mesmo. Se este grupo não existe, é adicionado.
* Para o `EndPoint` parâmetro, utilize a entrada **URL** na página 'Gerir Chaves'.
* Para o `Token` parâmetro, utilize a entrada **da chave de acesso primário** na página 'Chaves's 'Gestão'.
* Se necessário, desa estale o `Verbose` parâmetro para receber detalhes sobre a instalação.

### <a name="step-5----install-powershell-modules"></a>Passo 5 - Instalar módulos PowerShell

Os runbooks podem utilizar qualquer uma das atividades e cmdlets definidos nos módulos instalados no seu ambiente Azure Automation. Uma vez que estes módulos não são automaticamente implantados em computadores no local, deve instalá-los manualmente. A exceção é o módulo Azure. Este módulo é instalado por predefinição e fornece acesso a cmdlets para todos os serviços e atividades da Azure Automation.

Como o principal objetivo do Trabalhador de Runbook Híbrido é gerir os recursos locais, é provável que seja necessário instalar os módulos que suportam estes recursos, nomeadamente o `PowerShellGet` módulo. Para obter informações sobre a instalação de módulos Windows PowerShell, consulte [o Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar num local referenciado pela `PSModulePath` variável ambiental para que o trabalhador híbrido possa importar automaticamente. Para obter mais informações, consulte [instalar módulos em PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remova o Trabalhador de Runbook Híbrido de um computador Windows no local

1. No portal Azure, aceda à sua conta de Automação.
2. Em **Definições de Conta**, selecione **Teclas** e note os valores para **URL** e **Tecla de acesso primário**.

3. Abra uma sessão PowerShell no modo Administrador e execute o seguinte comando com o seu URL e valores de chave de acesso primário. Utilize o `Verbose` parâmetro para um registo detalhado do processo de remoção. Para remover máquinas velhas do seu grupo Operário Híbrido, utilize o `machineName` parâmetro opcional.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido de trabalhador runbook, primeiro precisa remover o Trabalhador de Runbook Híbrido de todos os computadores que é membro do grupo. Em seguida, utilize os seguintes passos para remover o grupo:

1. Abra a conta Automation no portal Azure.
2. Selecione **grupos de trabalhadores híbridos** em **Automação de Processos.** Selecione o grupo que pretende eliminar. A página de propriedades para este grupo aparece.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Delete**. Uma mensagem pede-lhe que confirme esta ação. Selecione **Sim** se tiver a certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este processo pode demorar vários segundos a terminar. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem, consulte [runbooks Runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md).
* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas.](troubleshoot/hybrid-runbook-worker.md#general)
