---
title: Implementar um Trabalhador de Resta Híbrida windows na Automação Azure
description: Este artigo diz como implementar um Trabalhador de Runbook Híbrido que pode usar para executar livros em computadores baseados no Windows no seu datacenter local ou ambiente na nuvem.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 3a27cee7a94ee6f33c399d10f90e47ec574e7380
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744183"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implementar um trabalhador de resta híbrida do Windows

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar livros de execução diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. A Azure Automation armazena e gere os livros de execução e depois entrega-os a um ou mais computadores designados. Este artigo descreve como implantar um Trabalhador de Livro Híbrido numa máquina windows.

Depois de implementar com sucesso um trabalhador do livro de corridas, reveja os livros de [execução run num Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros de execução para automatizar processos no seu datacenter no local ou em outro ambiente de nuvem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Instalação e configuração do trabalhador do livro de corridas híbrido do Windows Hybrid

Para instalar e configurar um Trabalhador do Livro híbrido do Windows, pode utilizar um dos seguintes métodos.

* Para VMs Azure, instale o agente Log Analytics para Windows utilizando a [extensão](../virtual-machines/extensions/oms-windows.md)da máquina virtual para windows . A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho existente no Log Analytics utilizando um modelo de Gestor de Recursos Azure ou PowerShell. Uma vez instalado o agente, o VM pode ser adicionado a um grupo híbrido runbook worker na sua conta Automation. Consulte os passos 3 e 4 na secção [de implantação manual.](#manual-deployment)

* Utilize um livro de execução automation para automatizar completamente o processo de configuração de um computador Windows. Este é o método recomendado para máquinas no seu datacenter ou outro ambiente de nuvem.

* Siga um procedimento passo a passo para instalar manualmente e configurar a função de Trabalhador do Livro de Execução Híbrido no seu VM não Azure.

> [!NOTE]
> Para gerir a configuração dos servidores que suportam a função de Trabalhador do Livro de Execução Híbrido com a Configuração de Estado Desejado (DSC), deve adicionar os servidores como nós DSC.

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

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Habilitar servidores para gestão com configuração do Estado da Automação Azure

Para obter informações sobre a ativação de servidores para gestão com a Configuração do Estado da Automação Azure, consulte [máquinas Ativas para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

Ativar a [Azure](automation-update-management.md) Automation Update Management configura automaticamente qualquer computador Windows que esteja ligado ao seu espaço de trabalho Log Analytics como um Trabalhador de Resta Híbrida para suportar atualizações do livro de execução. No entanto, este trabalhador não está registado em nenhum grupo híbrido de trabalho de runbook já definido na sua conta De automação.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Adição do computador a um grupo híbrido de trabalhadores de runbook

Pode adicionar o computador de trabalho a um grupo híbrido de trabalho de runbook na sua conta de Automação. Note que deve apoiar os livros de execução automation desde que esteja a usar a mesma conta tanto para a funcionalidade De Automação Azure como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

## <a name="automated-deployment"></a>Implementação automatizada

Na máquina-alvo, execute os seguintes passos para automatizar a instalação e configuração da função Do Trabalhador Híbrido do Windows.

### <a name="step-1---download-the-powershell-script"></a>Passo 1 - Descarregue o script PowerShell

Descarregue o script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). O download deve ser diretamente do computador que executa a função Hybrid Runbook Worker ou de outro computador no seu ambiente. Quando tiver descarregado o guião, copie-o para o seu trabalhador. O script **New-OnPremiseHybridWorker.ps1** utiliza os parâmetros descritos abaixo durante a execução.

| Parâmetro | Estado | Descrição |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos que está associado à sua conta de Automação. |
| `AutomationAccountName` | Obrigatório | O nome da sua conta de Automação.
| `Credential` | Opcional | As credenciais a utilizar ao iniciar sessão no ambiente Azure. |
| `HybridGroupName` | Obrigatório | O nome de um grupo híbrido runbook worker que especifica como alvo para os livros de execução que suportam este cenário. |
| `OMSResourceGroupName` | Opcional | O nome do grupo de recursos para o espaço de trabalho log Analytics. Se este grupo de recursos não for especificado, o valor é `AAResourceGroupName` utilizado. |
| `SubscriptionID` | Obrigatório | O identificador da subscrição Azure associado à sua conta Automation. |
| `TenantID` | Opcional | O identificador da organização de inquilinos associado à sua conta de Automação. |
| `WorkspaceName` | Opcional | O nome do espaço de trabalho Log Analytics. Se não tiver um espaço de trabalho no Log Analytics, o script cria e configura um. |

> [!NOTE]
> Ao ativar funcionalidades, a Azure Automation apenas suporta certas regiões para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Passo 2 - Concha de linha de comando Open Windows PowerShell

Abra o **Windows PowerShell** a partir do ecrã **Iniciar** no modo Administrador.

### <a name="step-3---run-the-powershell-script"></a>Passo 3 - Executar o script PowerShell

Na concha da linha de comando PowerShell, navegue para a pasta que contém o script que descarregou. Mude os valores para os `AutomationAccountName` parâmetros, , , e `AAResourceGroupName` `OMSResourceGroupName` `HybridGroupName` `SubscriptionID` `WorkspaceName` . Então executa o guião.

É-te pedido que autentiques com o Azure depois de executares o guião. Deve inscrever-se com uma conta que seja membro do papel de Administradorde Subscrição e coadministrador da subscrição.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Passo 4 - Instale nuget

É-lhe pedido que concorde em instalar o NuGet e autenticar com as suas credenciais Azure. Se não tiver a versão NuGet mais recente, pode obtê-la a partir de [versões disponíveis](https://www.nuget.org/downloads)de distribuição NuGet .

### <a name="step-5---verify-the-deployment"></a>Passo 5 - Verificar a implantação

Após a terminação do guião, a página dos Grupos de Trabalhadores Híbridos mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo da lista na página dos Grupos de Trabalhadores Híbridos e escolher o azulejo **Dos Trabalhadores Híbridos.** Na página dos Trabalhadores Híbridos, pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implementação manual

Na máquina-alvo, execute os dois primeiros passos uma vez para o seu ambiente de Automação. Em seguida, execute os passos restantes para cada computador trabalhador.

### <a name="step-1---create-a-log-analytics-workspace"></a>Passo 1 - Criar um espaço de trabalho de Log Analytics

Se ainda não tiver um espaço de trabalho de Log Analytics, reveja a orientação de design de [log do Monitor Azure](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Passo 2 - Adicione uma funcionalidade de Automação Azure ao espaço de trabalho log Analytics

Uma funcionalidade de Automação adiciona funcionalidade para a Automação Azure, incluindo suporte para o Trabalhador do Livro Híbrido. Quando ativa uma funcionalidade de Automação Azure no seu espaço de trabalho Log Analytics, os componentes do trabalhador são automaticamente empurrados para o computador do agente.

Para adicionar a funcionalidade De automação Azure, por exemplo, Gestão de Atualizações, ao seu espaço de trabalho, executar o seguinte cmdlet PowerShell:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Passo 3 - Instale o agente Log Analytics para Windows

O agente Log Analytics para windows liga os computadores a um espaço de trabalho do Monitor Azure Log Analytics. Quando instala o agente no computador e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador do Livro de Execução Híbrido.

Para instalar o agente no computador, siga as instruções do [Connect Windows para os registos do Monitor Azure](../log-analytics/log-analytics-windows-agent.md). Pode repetir este processo para que vários computadores adicionem vários trabalhadores ao seu ambiente.

Quando o agente tiver ligações bem-sucedidas ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa, deve ver registos de batimentos cardíacos para o computador, indicando que está ligado e reportando ao serviço. Por padrão, cada agente reencaminha um recorde de batimentos cardíacos para o seu espaço de trabalho atribuído. Utilize os seguintes passos para completar a instalação e a instalação do agente.

1. Ative a função para adicionar a máquina do agente. Ver [Ativar máquinas no espaço de trabalho](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Verifique se o agente descarregou corretamente a funcionalidade De sufira De Automação Azure. 
3. Para confirmar a versão do Hybrid Runbook Worker, navegue para **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** e note a subpasta da **versão.**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passo 4 - Instale o ambiente do livro de corridas e ligue-se à Automação Azure

Quando configura um agente para reportar a um espaço de trabalho de Log Analytics, a funcionalidade De automação Azure empurra para baixo o `HybridRegistration` módulo PowerShell, que contém o `Add-HybridRunbookWorker` cmdlet. Utilize este cmdlet para instalar o ambiente do livro de recortes no computador e registá-lo com a Automação Azure.

Abra uma sessão PowerShell no modo Administrador e execute os seguintes comandos para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Agora corra o `Add-HybridRunbookWorker` cmdlet utilizando a seguinte sintaxe.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Pode obter as informações necessárias para este cmdlet a partir da página 'Gerir Chaves' no portal Azure. Abra esta página selecionando **Teclas** na página Definições na sua conta Automação.

![Gerir a página de Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Para o `GroupName` parâmetro, utilize o nome do grupo Híbrido Runbook Worker. Se este grupo já existir na conta Automation, o computador atual é adicionado ao mesmo. Se este grupo não existir, é adicionado.
* Para o `EndPoint` parâmetro, utilize a entrada **URL** na página 'Gerir teclas'.
* Para o `Token` parâmetro, utilize a entrada **CHAVE DE ACESSO PRIMÁRIO** na página 'Gerir chaves'.
* Se necessário, defina o `Verbose` parâmetro para receber detalhes sobre a instalação.

### <a name="step-5----install-powershell-modules"></a>Passo 5 - Instalar módulos PowerShell

Os livros de execução podem utilizar qualquer uma das atividades e cmdlets definidos nos módulos instalados no seu ambiente de Automação Azure. Uma vez que estes módulos não são automaticamente implantados em computadores no local, deve instalá-los manualmente. A exceção é o módulo Azure. Este módulo é instalado por padrão e proporciona acesso a cmdlets para todos os serviços e atividades do Azure Automation.

Como o principal objetivo do Trabalhador do Livro Híbrido é gerir os recursos locais, é provável que seja necessário instalar os módulos que suportam estes recursos, nomeadamente o `PowerShellGet` módulo. Para obter informações sobre a instalação de módulos Windows PowerShell, consulte [o Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar num local referenciado pela `PSModulePath` variável ambiental, de modo a que o trabalhador híbrido possa importá-los automaticamente. Para mais informações, consulte [Instalar Módulos no PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remova o Trabalhador do Livro Híbrido de um computador Windows no local

1. No portal Azure, vá à sua conta de Automação.
2. Em **definições de conta,** selecione **Teclas** e note os valores para **URL** e **Chave de Acesso Primário**.

3. Abra uma sessão PowerShell no modo Administrador e execute o seguinte comando com o seu URL e valores-chave de acesso primário. Utilize o `Verbose` parâmetro para um registo detalhado do processo de remoção. Para remover máquinas velhas do seu grupo Hybrid Worker, utilize o `machineName` parâmetro opcional.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido runbook worker, você primeiro precisa remover o Trabalhador do Livro Híbrido de todos os computadores que é membro do grupo. Em seguida, utilize os seguintes passos para remover o grupo:

1. Abra a conta Automation no portal Azure.
2. Selecione **grupos de trabalhadores híbridos** no âmbito **da Automatização de Processos.** Selecione o grupo que pretende eliminar. A página de propriedades para aquele grupo aparece.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Eliminar**. Uma mensagem pede-lhe para confirmar esta ação. Selecione **Sim** se tiver a certeza de que pretende continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este processo pode levar alguns segundos para terminar. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="next-steps"></a>Passos seguintes

* [Executar runbooks numa Função de Trabalho de Runbook Híbrida](automation-hrw-run-runbooks.md)
* [Problemas problemas híbridos do trabalhador do livro de corridas](troubleshoot/hybrid-runbook-worker.md#windows)