---
title: Visão geral da Gestão de Atualização de Automação Azure
description: Visão geral da funcionalidade Update Management que gere atualizações para as suas máquinas Windows e Linux
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: d3a3a19673ecb6edb82f0512f318298865c8ed24
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681283"
---
# <a name="update-management-overview"></a>Descrição geral da Gestão de Atualizações

Pode utilizar a Update Management no Azure Automation para gerir as atualizações do sistema operativo para as suas máquinas Windows e Linux em Azure, em ambientes no local e noutros ambientes em nuvem. Pode avaliar rapidamente o estado das atualizações disponíveis em todas as máquinas do agente e gerir o processo de instalação de atualizações necessárias para servidores.

Pode ativar a Gestão de Atualizações para máquinas virtuais (VMs) utilizando os seguintes métodos:

* A partir da sua [conta Azure Automation](automation-onboard-solutions-from-automation-account.md) para uma ou mais máquinas Azure.
* Manualmente para máquinas não-Azure.
* Para um único Azure VM da página virtual da máquina no portal Azure. Este cenário está disponível para [VMs Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)
* Para [vários VMs Azure](manage-update-multi.md) selecionando-os a partir da página de máquinas Virtuais no portal Azure.

> [!NOTE]
> A Atualização de Gestão requer a ligação de um espaço de trabalho do Log Analytics à sua conta Deautomação. Para obter uma lista definitiva de regiões apoiadas, consulte [os mapeamentos do Espaço de Trabalho Azure.](how-to/region-mappings.md) Os mapeamentos da região não afetam a capacidade de gerir VMs numa região separada da sua conta de Automação.

Um modelo de Gestor de [Recursos Azure](automation-update-management-deploy-template.md) está disponível para ajudá-lo a implementar a Gestão de Atualizações para uma nova conta de Automação ou existente e espaço de trabalho log Analytics na sua subscrição.

> [!NOTE]
> Não é possível utilizar uma máquina configurada com Gestão de Atualização para executar scripts personalizados da Azure Automation. Esta máquina só pode executar o script de atualização assinado pela Microsoft. 

## <a name="update-management-overview"></a>Descrição geral da Gestão de Atualizações

As máquinas geridas pela Update Management utilizam as seguintes configurações para realizar avaliações e atualizar as implementações:

* Agente de Log Analytics para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux
* Função de Trabalho de Runbook Híbrida da Automatização
* Microsoft Update ou Windows Server Update Services (WSUS) para máquinas Windows

O diagrama que se segue ilustra como a Atualização de Gestão avalia e aplica atualizações de segurança a todas as máquinas windows server e Linux conectadas num espaço de trabalho:

![Fluxo de trabalho de Gestão de Atualização](./media/automation-update-management/update-mgmt-updateworkflow.png)

A Gestão de Atualizações pode ser utilizada para integrar máquinas de forma nativa em múltiplas subscrições no mesmo inquilino.

Depois de um pacote ser lançado, leva 2 a 3 horas para o patch aparecer para as máquinas Linux para avaliação. Para as máquinas Windows, o patch demora 12 a 15 horas para que o patch apareça para avaliação depois de lançado.

Depois de uma máquina completar uma varredura para a conformidade da atualização, o agente reencaminha as informações a granel para os registos do Monitor Azure. Numa máquina do Windows, a varredura de conformidade é executada a cada 12 horas por padrão.

Além da programação de digitalização, a varredura para a conformidade da atualização é iniciada dentro de 15 minutos após o reinício do agente Log Analytics, antes da instalação da atualização e após a instalação da atualização.

Para uma máquina Linux, o exame de conformidade é realizado de hora a hora por padrão. Se o agente Log Analytics for reiniciado, é iniciado um exame de conformidade dentro de 15 minutos.

Update Management informa como a máquina está atualizada até à data com base na fonte com que está configurado para sincronizar. Se a máquina do Windows estiver configurada para reportar ao WSUS, dependendo da última sincronização da WSUS com o Microsoft Update, os resultados podem diferir do que o Microsoft Update mostra. Este comportamento é o mesmo para as máquinas Linux que estão configuradas para reportar a um repo local em vez de a um repo público.

> [!NOTE]
> Para comunicar adequadamente ao serviço, a Atualização management requer que alguns URLs e portas sejam ativados. Para saber mais sobre estes requisitos, consulte a [configuração da Rede](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning).

Pode implementar e instalar atualizações de software em máquinas que requerem as atualizações criando uma implementação programada. As atualizações classificadas como opcionais não estão incluídas no âmbito de implementação das máquinas Windows. Apenas as atualizações necessárias estão incluídas no âmbito de implementação.

A implementação programada define quais as máquinas-alvo que recebem as atualizações aplicáveis. Fá-lo especificando explicitamente certas máquinas ou selecionando um [grupo informático](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups) baseado em pesquisas de registo de um conjunto específico de máquinas (ou numa [consulta azure](automation-update-management-query-logs.md) que seleciona dinamicamente VMs Azure com base em critérios especificados). Estes grupos diferem da configuração do [âmbito,](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)que é usada para controlar o alvo das máquinas que recebem a configuração para ativar a Gestão de Atualizações. Isto impede-os de realizar e reportar a conformidade da atualização, e instalar atualizações necessárias aprovadas.

Ao definir uma implementação, também especifica um calendário para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Este período chama-se janela de manutenção. Um espaço de 20 minutos da janela de manutenção é reservado para reboots, assumindo que um é necessário e selecionou a opção de reinicialização apropriada. Se o remendo demorar mais do que o esperado e houver menos de 20 minutos na janela de manutenção, não ocorrerá um reboot.

As atualizações são instaladas por runbooks na Automatização do Azure. Não se pode ver estes livros, e eles não precisam de qualquer configuração. Quando uma implementação de atualização é criada, cria um calendário que inicia um livro de execução de atualização principal no momento especificado para as máquinas incluídas. O livro de execução principal inicia um livro de corridas para crianças em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implementação da atualização, as máquinas-alvo executam a implantação paralelamente. Antes da instalação, é executada uma verificação para verificar se as atualizações ainda são necessárias. Para as máquinas de clientes WSUS, se as atualizações não forem aprovadas no WSUS, a implementação da atualização falha.
Ter uma máquina registada para Gestão de Atualizações em mais de um espaço de trabalho log Analytics (também referido como multihoming) não é suportado.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes suportados

A tabela seguinte lista os sistemas operativos suportados para avaliações de atualizações. Remendar requer um Trabalhador Híbrido do Livro de Corridas. Para obter informações sobre os requisitos do Trabalhador do Livro de Execução Híbrido, consulte A implantação de um Trabalhador do [Livro de Corridas Híbrido do Windows](automation-windows-hrw-install.md) e de um Trabalhador de [Runbook Híbrido Linux](automation-linux-hrw-install.md).

> [!NOTE]
> A avaliação da atualização das máquinas Linux só é suportada em certas regiões, tal como listado na conta de Automação e na tabela de [mapeamento do](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)espaço de trabalho Log Analytics. 

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM e SP1 Standard)| A Atualização Management apenas suporta avaliações para este sistema operativo. O patching não é suportado, uma vez que o [Hybrid Runbook Worker](automation-windows-hrw-install.md) não é suportado para o Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes linux exigem acesso a um repositório de atualização. A correção baseada na classificação requer a devolução de dados de `yum` segurança que o CentOS não tem nas suas versões RTM. Para obter mais informações sobre patching baseado na classificação no CentOS, consulte [classificações de atualização no Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes linux exigem acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes linux exigem acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)      |Os agentes linux exigem acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de escala de máquinas virtuais Azure podem ser geridos através da Atualização. A Atualização Management trabalha nas próprias instâncias e não na imagem base. Terá de agendar as atualizações de forma incremental, para que nem todas as instâncias vm sejam atualizadas de uma só vez.
> Pode adicionar nódosos para conjuntos de escala de máquinavirtual seguindo os degraus sob [bordo de uma máquina não Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de clientes não suportados

A tabela que se segue enumera sistemas operativos não suportados:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | Os sistemas operativos dos clientes (como o Windows 7 e windows 10) não são suportados.        |
|Windows Server 2016 Nano Server     | Não suportado.       |
|Nódosos de serviço Azure Kubernetes | Não suportado. Utilize o processo de correção descrito em Aplicar atualizações de [segurança e kernel aos nós Linux no Serviço Azure Kubernetes (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

As seguintes informações descrevem os requisitos específicos do cliente do sistema operativo. Para obter orientações adicionais, consulte o [planeamento da rede.](#ports)

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para comunicar com um servidor WSUS, ou necessitam de acesso ao Microsoft Update. Para obter informações sobre como instalar o agente Log Analytics para windows, consulte [Connect Windows computers to Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Pode utilizar a Atualização de Gestão com o Microsoft Endpoint Configuration Manager. Para saber mais sobre cenários de integração, consulte Integrar o Gestor de [Configuração com gestão de atualizações.](updatemgmt-mecmintegration.md#configuration) O [agente Log Analytics para Windows](../azure-monitor/platform/agent-windows.md) é necessário para servidores Windows geridos por sites no ambiente do Seu Gestor de Configuração. 

Por predefinição, os VMs do Windows que são implementados a partir do Mercado Azure estão definidos para receber atualizações automáticas do Windows Update Service. Este comportamento não muda quando adiciona VMs do Windows ao seu espaço de trabalho. Se não gerir ativamente as atualizações utilizando a Atualização, aplica-se o comportamento predefinido (para aplicar automaticamente atualizações).

> [!NOTE]
> Pode modificar a Política de Grupo para que as reinicializações da máquina possam ser realizadas apenas pelo utilizador e não pelo sistema. As máquinas geridas podem ficar presas se a Atualização não tiver direitos de reiniciar a máquina sem interação manual do utilizador. Para mais informações, consulte as definições de [Política do Grupo Configure para atualizações automáticas](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para o Linux, a máquina requer acesso a um repositório de atualização, privado ou público. TLS 1.1 ou TLS 1.2 é necessário para interagir com a Gestão de Atualizações. A Atualização de Gestão não suporta um agente de Log Analytics para o Linux que esteja configurado para reportar a mais de um espaço de trabalho de Log Analytics. A máquina também deve ter Python 2.x instalado.

> [!NOTE]
> A avaliação da atualização das máquinas Linux só é suportada em certas regiões. Consulte a conta de Automação e a tabela de [mapeamento do](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)espaço de trabalho Log Analytics . 

Para obter informações sobre como instalar o agente Log Analytics para o Linux e para descarregar a versão mais recente, consulte o [agente Log Analytics para o Linux](../azure-monitor/platform/agent-linux.md). 

Os VMs criados a partir das imagens a pedido da Red Hat Enterprise Linux (RHEL) que estão disponíveis no Azure Marketplace estão registados para aceder à Infraestrutura de Atualização do [Chapéu Vermelho (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) que está implantada no Azure. Qualquer outra distribuição linux deve ser atualizada a partir do repositório de ficheiros online da distribuição utilizando métodos suportados pela distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerir as implementações de atualizações, necessita de permissões específicas. Para conhecer estas permissões, consulte [o acesso baseado em Funções – Gestão de Atualizações.](automation-role-based-access-control.md#update-management-permissions)

## <a name="update-management-components"></a>Atualizar componentes de Gestão

A Update Management utiliza os recursos descritos nesta secção. Estes recursos são automaticamente adicionados à sua conta Deautomatização quando ativa a Gestão de Atualizações.

### <a name="hybrid-runbook-worker-groups"></a>Grupos de trabalhadores de runbook híbrido

Depois de ativar a Atualização, qualquer máquina Windows que esteja diretamente ligada ao seu espaço de trabalho Log Analytics é configurada automaticamente como um Trabalhador de Resta Híbrida para suportar os livros de execução que suportam a Gestão de Atualizações.

Cada máquina Windows gerida pela Update Management está listada no painel de grupos de trabalhadores híbridos como um grupo de trabalhadores híbridos system para a conta Automation. Os grupos usam a `Hostname FQDN_GUID` convenção de nomeação. Não pode supor estes grupos com livros na sua conta. Se tentares, a tentativa falha. Estes grupos destinam-se a apoiar apenas a Atualização de Gestão.

Pode adicionar a máquina Windows a um grupo híbrido runbook Worker na sua conta Automation para suportar os livros de execução automation se utilizar a mesma conta para gestão de atualizações e a adesão ao grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada na versão 7.2.12024.0 do Trabalhador híbrido do Runbook Worker.

### <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão de Gestor de Operações estiver ligado a um espaço de [trabalho de Log Analytics,](../azure-monitor/platform/om-agents.md)os seguintes pacotes de gestão são instalados no Gestor de Operações. Estes pacotes de gestão também estão instalados para Gestão de Atualizações em máquinas Windows diretamente ligadas. Não precisa de configurar nem de gerir estes pacotes de gestão.

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

> [!NOTE]
> Se tiver um grupo de gestão 1807 ou 2019 ligado a um espaço de trabalho do Log Analytics com agentes configurados no grupo de gestão para recolher dados de registo, precisa de anular o parâmetro `IsAutoRegistrationEnabled` e defini-lo para True na regra **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Para obter mais informações sobre atualizações para pacotes de gestão, consulte [connect Operations Manager para registos do Monitor Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para que a Gestão de Atualizações gere totalmente as máquinas com o agente Log Analytics, tem de atualizar o agente Log Analytics para windows ou o agente Log Analytics para o Linux. Para aprender a atualizar o agente, consulte como atualizar um agente do Gestor de [Operações](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Em ambientes que utilizam o Gestor de Operações, deve estar a executar o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-sources"></a>Fontes apoiadas

O quadro seguinte descreve as fontes conectadas que a Atualização de Gestão suporta:

| Origem ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A Update Management recolhe informações sobre atualizações do sistema a partir de agentes do Windows e inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |A Update Management recolhe informações sobre atualizações do sistema a partir de agentes Linux e inicia a instalação de atualizações necessárias sobre distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A Update Management recolhe informações sobre atualizações do sistema de agentes de um grupo de gestão conectado.<br/><br/>Não é necessária uma ligação direta do agente do Gestor de Operações aos registos do Monitor Azure. Os dados são encaminhados do grupo de gestão para o espaço de trabalho log Analytics. |

### <a name="collection-frequency"></a>Frequência da recolha

Atualização A Gestão gere as máquinas para obter dados utilizando as seguintes regras. Pode levar entre 30 minutos e 6 horas para o painel de instrumentos apresentar dados atualizados a partir de máquinas geridas.

* Cada máquina Windows - Update Management faz uma varredura duas vezes por dia para cada máquina. A cada 15 minutos, consulta a API do Windows pela última vez na atualização para determinar se o estado mudou. Se o estado tiver mudado, a Atualização Management inicia uma verificação de conformidade.

* Cada máquina Linux - Atualização management faz uma varredura a cada hora.

A utilização média de dados por registos do Monitor Azure para uma máquina que utilize a Atualização é de aproximadamente 25 MB por mês. Este valor é apenas uma aproximação e está sujeito a alterações, dependendo do seu ambiente. Recomendamos que monitorize o seu ambiente para acompanhar o seu uso exato. Para obter mais informações para analisar o uso de dados, consulte [Gerir o uso e o custo.](../azure-monitor/platform/manage-cost-storage.md)

## <a name="network-planning"></a><a name="ports"></a>Planeamento da rede

Os seguintes endereços são necessários especificamente para gestão de atualizações. A comunicação a estes endereços ocorre sobre a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Para as máquinas Windows, também deve permitir o tráfego em todos os pontos finais exigidos pelo Windows Update. Pode encontrar uma lista atualizada de pontos finais necessários em [Questões relacionadas com HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se tiver um [servidor local de Atualização do Windows,](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)também deve permitir o tráfego para o servidor especificado na sua chave [WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para as máquinas Red Hat Linux, consulte [os iPs para os servidores](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) de entrega de conteúdo RHUI para obter pontos finais necessários. Para outras distribuições linux, consulte a documentação do seu fornecedor.

Para obter mais informações sobre as portas necessárias para o Trabalhador do Livro de Corridas Híbrido, consulte [os endereços de Gestão de Atualização para o Trabalhador do Livro de Corridas Híbridos](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Recomendamos que utilize os endereços listados ao definir exceções. Para endereços IP, pode baixar [as gamas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente, e reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

Siga as instruções em [Connect computadores sem acesso](../azure-monitor/platform/gateway.md) à Internet para configurar máquinas que não tenham acesso à Internet.

## <a name="update-classifications"></a>Classificações de atualizações

O quadro seguinte define as classificações que a Atualização de Gestão suporta para atualizações do Windows. 

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico e não relacionado com a segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto e relacionado com a segurança.        |
|Update rollups     | Um conjunto cumulativo de hotfixes que são embalados em conjunto para uma fácil implantação.        |
|Pacotes de funcionalidades     | Novas funcionalidades do produto que são distribuídas fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixos que são aplicados a uma aplicação.        |
|Atualizações de definições     | Uma atualização de ficheiros de vírus ou outradefinição.        |
|Ferramentas     | Um utilitário ou funcionalidade que ajuda a completar uma ou mais tarefas.        |
|Atualizações     | Uma atualização de uma aplicação ou ficheiro que está atualmente instalado.        |

A tabela seguinte define as classificações suportadas para atualizações linux.

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionado com a segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou que não são atualizações de segurança.        |

Para o Linux, a Atualização de Gestão pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para remendar, a Atualização Management baseia-se nos dados de classificação disponíveis na máquina. Ao contrário de outras distribuições, o CentOS não dispõe desta informação na versão RTM. Se tiver máquinas CentOS configuradas para devolver os dados de segurança para o seguinte comando, a Atualização de Gestão pode corrigir com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente não existe um método suportado para permitir a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas é prestado um apoio de melhor esforço aos clientes que possam ter permitido esta funcionalidade por conta própria. 

Para classificar as atualizações na versão 6 da Red Hat Enterprise, é necessário instalar o plugin de segurança de yum. No Red Hat Enterprise Linux 7, o plugin já faz parte do yum em si e não há necessidade de instalar nada. Para mais informações, consulte o seguinte artigo de [conhecimento](https://access.redhat.com/solutions/10021)do Chapéu Vermelho.

## <a name="integrate-update-management-with-configuration-manager"></a>Integrar gestão de atualizações com gestor de configuração

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerir Computadores, servidores e dispositivos móveis também dependem da força e maturidade do Gestor de Configuração para ajudar a gerir as atualizações de software. Para aprender a integrar a Gestão de Atualizações com o Gestor de Configuração, consulte [O Gestor de Configuração Integrado com Gestão de Atualizações.](updatemgmt-mecmintegration.md)

## <a name="third-party-updates-on-windows"></a>Atualizações de terceiros no Windows

A Atualização A Gestão conta com o repositório de atualização configurado localmente para atualizar os sistemas Windows suportados, seja wSUS ou Windows Update. Ferramentas como [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) permitem-lhe importar e publicar atualizações personalizadas com a WSUS. Este cenário permite que a Atualização de Gestão atualize máquinas que utilizam o Gestor de Configuração como o seu repositório de atualização com software de terceiros. Para saber como configurar atualizações, consulte instalar [atualizações editora](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

Um modelo de Gestor de [Recursos](automation-update-management-deploy-template.md) Azure está disponível para ajudá-lo a implementar a Gestão de Atualizações para uma nova conta de Automação ou existente e o espaço de trabalho do Azure Monitor Log Analytics na sua subscrição. Não configura o âmbito das máquinas que devem ser geridas, isto é executado como um passo separado após a utilização do modelo.

Aqui estão as formas de ativar a Gestão de Atualizações e selecionar máquinas para ser geridas:

* [De uma máquina virtual.](automation-onboard-solutions-from-vm.md)
* [Desde a navegação em várias máquinas.](automation-onboard-solutions-from-browse.md)
* [A partir de uma conta de Automação Azure.](automation-onboard-solutions.md)

## <a name="next-steps"></a>Passos seguintes

Reveja as [FAQ](automation-faq.md) de Automação Azure para analisar questões comuns sobre gestão de atualizações.
