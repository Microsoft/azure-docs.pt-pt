---
title: Função de Trabalho de Runbook Híbrida do Windows da Automatização do Azure
description: Este artigo fornece informações sobre a instalação de um Trabalhador de Runbook Híbrido de Automação Azure que pode usar para executar livros de execução em computadores baseados no Windows no seu datacenter local ou ambiente na nuvem.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 9f3e06f66996be4a2b43b64e6100c62a2fa41381
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649964"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementar um trabalhador de resta híbrida do Windows

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar livros de execução diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. Os livros de execução são armazenados e geridos na Azure Automation e depois entregues a um ou mais computadores designados. Este artigo descreve como implantar o Trabalhador do Livro Híbrido numa máquina Windows.

Depois de implementar com sucesso um trabalhador do livro de corridas, reveja os livros de [execução run num Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros de execução para automatizar processos no seu datacenter no local ou em outro ambiente de nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do trabalhador do livro de corridas híbrido do Windows Hybrid

Para instalar e configurar um Trabalhador do Livro híbrido do Windows, pode utilizar um dos seguintes métodos.

* Para Os VMs Azure, instale o agente Log Analytics para windows utilizando a [extensão](../virtual-machines/extensions/oms-windows.md)virtual da máquina para windows . A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho existente no Log Analytics utilizando um modelo de Gestor de Recursos Azure ou com PowerShell. Uma vez instalado o agente, o VM pode ser adicionado a um grupo híbrido runbook worker na sua conta Automação seguindo o **passo 4** sob a secção de [implantação manual](#manual-deployment) abaixo.

* Utilize um livro de execução automation para automatizar completamente o processo de configuração de um computador Windows. Este é o método recomendado para máquinas no seu datacenter ou outro ambiente de nuvem.

* Siga um procedimento passo a passo para instalar manualmente e configurar a função de Trabalhador do Livro de Execução Híbrido no seu VM não Azure.

> [!NOTE]
> Para gerir a configuração dos seus servidores que suportam a função de Trabalhador do Livro de Execução Híbrido com a Configuração do Estado Desejado (DSC), precisa adicioná-los como nós DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisitos mínimos para o Trabalhador do Livro híbrido do Windows

Os requisitos mínimos para um Trabalhador do Livro Híbrido windows são:

* Windows Server 2012 ou posterior
* Windows PowerShell 5.1 ou posterior[(baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="network-configuration"></a>Configuração da rede

Para obter mais requisitos de networking para o Trabalhador do Livro de Corridas Híbrido, consulte [Configurar a sua rede](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Embarque no servidor para gestão com Automation DSC

Para obter informações sobre servidores de embarque para gestão com dSC, consulte [máquinas de embarque para gestão pela Azure Automation DSC](automation-dsc-onboarding.md).

Se ativar a [solução De Gestão de Atualizações,](../operations-management-suite/oms-solution-update-management.md)qualquer computador Windows ligado ao seu espaço de trabalho Log Analytics é automaticamente configurado como um Trabalhador de Resta Híbrida para suportar os livros incluídos nesta solução. No entanto, não está registado em nenhum grupo de Trabalhadores Híbridos já definido na sua conta De automação.

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Adicionar o computador a um grupo híbrido de trabalhadores de runbook

O computador pode ser adicionado a um grupo híbrido Runbook Worker na sua conta Automation para apoiar os livros de automação desde que esteja a usar a mesma conta tanto para a solução como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

## <a name="automated-deployment"></a>Implantação automatizada

Na máquina-alvo, execute os seguintes passos para automatizar a instalação e configuração da função Do Trabalhador Híbrido do Windows.

### <a name="1-download-the-powershell-script"></a>1. Descarregue o script PowerShell

Descarregue o script New-OnPremiseHybridWorker.ps1 da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) diretamente do computador que executa a função Hybrid Runbook Worker ou de outro computador no seu ambiente. Copie o guião para o trabalhador. O script New-OnPremiseHybridWorker.ps1 requer os seguintes parâmetros durante a execução:

* *Nome aAResourceGroup (obrigatório):* O nome do grupo de recursos que está associado à sua conta de Automação.
* *OMSResourceGroupName* (opcional): O nome do grupo de recursos para o espaço de trabalho log Analytics. Se este grupo de recursos não for especificado, o *AAResourceGroupName* é utilizado.
* *Subscrição ID* (obrigatório): O ID de subscrição azure em que a sua Conta de Automação está.
* *TenantID* (opcional): O identificador da organização de inquilinos associado à sua Conta de Automação.
* *Nome do espaço de trabalho* (opcional): O nome do espaço de trabalho Log Analytics. Se não tiver um espaço de trabalho no Log Analytics, o script cria e configura um.
* Nome de *Conta de Automação* (obrigatório): O nome da sua Conta de Automação.
* *HybridGroupName* (obrigatório): O nome de um grupo híbrido runbook worker que especifica como um alvo para os livros de execução que suportam este cenário.
* *Credencial* (opcional): As credenciais a utilizar ao iniciar sessão no ambiente Azure.
  
> [!NOTE]
> Ao ativar soluções, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O Mapeamento da Região para A Conta de Automação e espaço de trabalho log Analytics](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Abrir a linha de comando PowerShell do Windows

Abra o **Windows PowerShell** a partir do ecrã **Iniciar** no modo Administrador.

### <a name="3-run-the-powershell-script"></a>3. Executar o script PowerShell

A partir da concha da linha de comando PowerShell, navegue para a pasta que contém o script que descarregou. Altere os valores para os parâmetros *-AutomaçãoAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*, e *-WorkspaceName*. Então executa o guião.

É-te pedido que autentiques com o Azure depois de executares o guião. Deve inscrever-se com uma conta que seja membro do papel de Administradorde Subscrição e coadministrador da subscrição.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. Instalar nuget

É-lhe pedido que concorde em instalar o NuGet e autenticar com as suas credenciais Azure. Se não tiver a versão NuGet mais recente, pode obtê-la a partir de [versões disponíveis](https://www.nuget.org/downloads)de distribuição NuGet .

### <a name="5-verify-the-deployment"></a>5. Verificar a implantação

Após a terminação do guião, a página dos **Grupos de Trabalhadores Híbridos** mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página grupo seletiva de grupos de **trabalhadores híbridos** e selecionar o azulejo **Híbrido Workers.** Na página dos **Trabalhadores Híbridos,** vê-se cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

Na máquina-alvo, execute os dois primeiros passos uma vez para o seu ambiente de Automação. Em seguida, execute os passos restantes para cada computador trabalhador.

### <a name="1-create-a-log-analytics-workspace"></a>1. Criar um espaço de trabalho de Log Analytics

Se ainda não tiver um espaço de trabalho log Analytics, reveja primeiro a orientação de design de [log do Monitor Azure](../azure-monitor/platform/design-logs-deployment.md) antes de criar um espaço de trabalho.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Adicione a solução de Automação ao espaço de trabalho log Analytics

A solução Automation adiciona funcionalidade para a Automação Azure, incluindo suporte para O Trabalhador de Runbook Híbrido. Quando adiciona a solução ao seu espaço de trabalho Log Analytics, empurra automaticamente os componentes do trabalhador para o computador do agente que irá instalar no próximo passo.

Para adicionar a solução **Automation** ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Instale o agente Log Analytics para Windows

O agente Log Analytics para windows liga os computadores a um espaço de trabalho do Monitor Azure Log Analytics. Quando instala o agente no computador e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador do Livro de Corridas Híbrido.

Para instalar o agente no computador, siga as instruções do [Connect Windows para os registos do Monitor Azure](../log-analytics/log-analytics-windows-agent.md). Pode repetir este processo para que vários computadores adicionem vários trabalhadores ao seu ambiente.

Quando o agente tiver ligações com sucesso ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, deve ver registos de batimentos cardíacos para o computador, indicando que está ligado e reportando ao serviço. Por padrão, cada agente reencaminha um recorde de batimentos cardíacos para o seu espaço de trabalho atribuído. Pode verificar se o agente descarregou corretamente a solução Automation quando tem uma pasta chamada AzureAutomationFiles em C:\Program Files\Microsoft Monitoring Agent\Agent. Para confirmar a versão do Hybrid Runbook Worker, navegue para C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ e note a subpasta da *versão* \\.

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instale o ambiente do livro de execução e ligue-se à Automação Azure

Quando configura um agente para reportar a um espaço de trabalho de Log Analytics, a solução Automation empurra para baixo o módulo HybridRegistration PowerShell, que contém o cmdlet **Add-HybridRunbookWorker.** Utilize este cmdlet para instalar o ambiente do livro de recortes no computador e registá-lo com a Automação Azure.

Abra uma sessão PowerShell no modo Administrador e execute os seguintes comandos para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Em seguida, executar o cmdlet **Add-HybridRunbookWorker** utilizando a seguinte sintaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Pode obter as informações necessárias para este cmdlet a partir da página "Gerir chaves" no portal Azure. Abra esta página selecionando a opção **Chaves** a partir da página **Definições** na sua conta Automação.

![Página "Gerir chaves"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o parâmetro *GroupName,* utilize o nome do grupo Hybrid Runbook Worker. Se este grupo já existir na conta Automation, o computador atual é adicionado ao mesmo. Se este grupo não existir, é adicionado.
* Para o parâmetro *EndPoint,* utilize a entrada **URL** na página 'Gerir teclas'.
* Para o parâmetro *Token,* utilize a entrada **CHAVE DE ACESSO PRIMÁRIO** na página 'Gerir chaves'.

Para receber informações detalhadas sobre a instalação, utilize o interruptor *-Verbose* com **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. Instalar módulos PowerShell

Os livros de execução podem utilizar qualquer uma das atividades e cmdlets definidos nos módulos instalados no seu ambiente de Automação Azure. Estes módulos não são automaticamente implantados em computadores no local, pelo que deve instalá-los manualmente. A exceção é o módulo Azure. Este módulo é instalado por padrão e proporciona acesso a cmdlets para todos os serviços e atividades do Azure Automation.

Como o principal objetivo da funcionalidade Hybrid Runbook Worker é gerir os recursos locais, é provável que seja necessário instalar os módulos que suportam estes recursos, em particular o módulo PowerShellGet. Para obter informações sobre a instalação de módulos Windows PowerShell, consulte [o Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar num local referenciado pela variável ambiente PSModulePath para que o trabalhador híbrido possa importá-los automaticamente. Para mais informações, consulte [Instalar Módulos no PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros de execução para automatizar processos no seu centro de dados no local ou em outro ambiente em nuvem, consulte [run run run book em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover os trabalhadores híbridos do livro de corridas, consulte [Remove Azure Automation Hybrid Runbook Workers](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para aprender a resolver problemas com os seus Trabalhadores híbridos, consulte os trabalhadores do [livro híbrido Windows Desmonantes](troubleshoot/hybrid-runbook-worker.md#windows)
* Para obter passos adicionais sobre como resolver problemas com a Atualização de Gestão, consulte A Gestão de [Atualizações: resolução de problemas](troubleshoot/update-management.md).
