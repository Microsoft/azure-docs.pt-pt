---
title: Solução de Gestão de Atualização em Azure
description: Este artigo descreve como utilizar a solução Azure Update Management para gerir atualizações para as suas máquinas Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: c76b14e4f08ec930159498da4a35fdad0341929e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278509"
---
# <a name="update-management-solution-in-azure"></a>Solução de Gestão de Atualização em Azure

Pode utilizar a solução Update Management na Azure Automation para gerir as atualizações do sistema operativo para as suas máquinas Windows e Linux em Azure, em ambientes no local e noutros ambientes em nuvem. Pode avaliar rapidamente o estado das atualizações disponíveis em todas as máquinas do agente e gerir o processo de instalação de atualizações necessárias para servidores.

Pode ativar a Gestão de Atualizações para máquinas virtuais (VMs) utilizando os seguintes métodos:

- A partir do seu [Azure Automation conta](automation-onboard-solutions-from-automation-account.md) uma ou mais máquinas Azure e manualmente para máquinas não Azure.

- Para um único Azure VM a partir da página de máquina virtual no portal Azure. Este cenário está disponível para [VMs Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Para [vários VMs Azure](manage-update-multi.md) selecionando-os a partir da página de **máquinas Virtuais** no portal Azure. 

> [!NOTE]
> A solução Update Management requer a ligação de um espaço de trabalho de Log Analytics à sua conta Deautomação. Para obter uma lista definitiva de regiões apoiadas, consulte [os mapeamentos do Espaço de Trabalho Azure.](./how-to/region-mappings.md) Os mapeamentos da região não afetam a capacidade de gerir VMs numa região separada da sua conta de Automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Está disponível um modelo de Gestor de [Recursos](automation-update-management-deploy-template.md) Azure que lhe permite implementar a solução de Gestão de Atualização para uma nova conta de Automação ou existente e espaço de trabalho log Analytics na sua subscrição.

## <a name="solution-overview"></a>Descrição geral da solução

As máquinas geridas pela Update Management utilizam as seguintes configurações para realizar avaliações e atualizar as implementações:

* Agente de Log Analytics para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux
* Função de Trabalho de Runbook Híbrida da Automatização
* Microsoft Update ou Windows Server Update Services (WSUS) para máquinas Windows

O diagrama que se segue ilustra como a solução avalia e aplica atualizações de segurança a todas as máquinas Windows Server e Linux conectadas num espaço de trabalho:

![Fluxo de processo de gestão de atualização](./media/automation-update-management/update-mgmt-updateworkflow.png)

A Atualização Gestão pode ser usada para máquinas de bordo nativas em múltiplas subscrições no mesmo inquilino.

Depois de um pacote ser lançado, leva 2 a 3 horas para o patch aparecer para as máquinas Linux para avaliação. Para as máquinas Windows, o patch demora 12 a 15 horas para que o patch apareça para avaliação depois de lançado.

Depois de uma máquina completar uma varredura para a conformidade da atualização, o agente reencaminha as informações a granel para os registos do Monitor Azure. Numa máquina do Windows, a varredura de conformidade é executada a cada 12 horas por padrão.

Além da programação de digitalização, a varredura para a conformidade da atualização é iniciada dentro de 15 minutos após o reinício do agente Log Analytics, antes da instalação da atualização e após a instalação da atualização.

Para uma máquina Linux, o exame de conformidade é realizado de hora a hora por padrão. Se o agente Log Analytics for reiniciado, é iniciado um exame de conformidade dentro de 15 minutos.

A solução relata como a máquina está atualizada com base na fonte com que está configurado para sincronizar. Se a máquina do Windows estiver configurada para reportar ao WSUS, dependendo da última sincronização da WSUS com o Microsoft Update, os resultados podem diferir do que o Microsoft Update mostra. Este comportamento é o mesmo para as máquinas Linux que estão configuradas para reportar a um repo local em vez de a um repo público.

> [!NOTE]
> Para comunicar adequadamente ao serviço, a Atualização management requer que alguns URLs e portas sejam ativados. Para saber mais sobre estes requisitos, consulte o [planeamento da Rede para Trabalhadores Híbridos.](automation-hybrid-runbook-worker.md#network-planning)

Pode implementar e instalar atualizações de software em máquinas que requerem as atualizações criando uma implementação programada. As atualizações classificadas como *Opcional* não estão incluídas no âmbito de implementação das máquinas Windows. Apenas as atualizações necessárias estão incluídas no âmbito de implementação.

A implementação programada define quais as máquinas-alvo que recebem as atualizações aplicáveis. Fá-lo especificando explicitamente certas máquinas ou selecionando um [grupo informático](../azure-monitor/platform/computer-groups.md) baseado em pesquisas de registo de um conjunto específico de máquinas (ou numa [consulta azure](automation-update-management-query-logs.md) que seleciona dinamicamente VMs Azure com base em critérios especificados). Estes grupos diferem da configuração do [âmbito,](../azure-monitor/insights/solution-targeting.md)que é usada apenas para determinar quais as máquinas que obtêm os pacotes de gestão que permitem a solução.

Especifica também um calendário para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Este período chama-se janela de manutenção. Um espaço de 20 minutos da janela de manutenção é reservado para reboots, assumindo que um é necessário e selecionou a opção de reinicialização apropriada. Se o remendo demorar mais do que o esperado e houver menos de 20 minutos na janela de manutenção, não ocorrerá um reboot.

As atualizações são instaladas por runbooks na Automatização do Azure. Não se pode ver estes livros, e eles não precisam de qualquer configuração. Quando uma implementação de atualização é criada, cria um calendário que inicia um livro de execução de atualização principal no momento especificado para as máquinas incluídas. O livro de execução principal inicia um livro de corridas para crianças em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implementação da atualização, as máquinas-alvo executam a implantação paralelamente. Antes da instalação, é executada uma verificação para verificar se as atualizações ainda são necessárias. Para as máquinas de clientes WSUS, se as atualizações não forem aprovadas no WSUS, a implementação da atualização falha.

Ter uma máquina registada para Gestão de Atualizações em mais de um espaço de trabalho log Analytics (também referido como multihoming) não é suportado.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela seguinte lista os sistemas operativos suportados para avaliações de atualizações. Remendar requer um Trabalhador Híbrido do Livro de Corridas. Para obter informações sobre os requisitos do Trabalhador do Livro de Execução Híbrido, consulte os guias de instalação para instalar um Trabalhador do [Livro híbrido do Windows](automation-windows-hrw-install.md) e um Trabalhador de [Runbook Híbrido Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM e SP1 Standard)| A Atualização Management apenas suporta a realização de avaliações para este sistema operativo, patching não é suportado uma vez que o [Hybrid Runbook Worker](automation-windows-hrw-install.md) não é suportado para o Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes linux exigem acesso a um repositório de atualização. A correção baseada na classificação requer `yum` para devolver dados de segurança que o CentOS não tem nas suas versões RTM. Para obter mais informações sobre patching baseado na classificação no CentOS, consulte [classificações de atualização no Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes linux exigem acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes linux exigem acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)      |Os agentes linux exigem acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de escala de máquinas virtuais Azure podem ser geridos através da Atualização. A Atualização Management trabalha nas próprias instâncias e não na imagem base. Terá de agendar as atualizações de forma incremental, para que nem todas as instâncias vm sejam atualizadas de uma só vez.
> Pode adicionar nódosos para conjuntos de escala de máquinavirtual seguindo os degraus sob [bordo de uma máquina não Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

A tabela que se segue enumera sistemas operativos não suportados:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | Não são suportados sistemas operativos de cliente (por exemplo, o Windows 7 e Windows 10).        |
|Servidor de Nano do Windows Server 2016     | Não suportado.       |
|Nódosos de serviço Azure Kubernetes | Não suportado. Utilize o processo de correção descrito em Aplicar atualizações de [segurança e kernel aos nós Linux no Serviço Azure Kubernetes (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

As seguintes informações descrevem os requisitos específicos do cliente. Para obter orientações adicionais, consulte o [planeamento da rede.](#ports)

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para comunicar com um servidor WSUS, ou necessitam de acesso ao Microsoft Update.

Pode utilizar a Gestão de Atualizações com o Gestor de Configuração. Para saber mais sobre cenários de integração, consulte Integrar o Gestor de [Configuração com gestão de atualizações.](oms-solution-updatemgmt-sccmintegration.md#configuration) É necessário o [agente Do Windows De Log Analytics.](../azure-monitor/platform/agent-windows.md) O agente é instalado automaticamente se estiver a embarcar num VM Azure.

Por predefinição, os VMs do Windows que são implementados a partir do Mercado Azure estão definidos para receber atualizações automáticas do Windows Update Service. Este comportamento não muda quando adiciona esta solução ou adiciona VMs do Windows ao seu espaço de trabalho. Se não gerir ativamente as atualizações utilizando esta solução, o comportamento predefinido (para aplicar automaticamente atualizações) aplica-se.

> [!NOTE]
> Um utilizador pode modificar a Política de Grupo para que as reinicializações da máquina possam ser realizadas apenas pelo utilizador e não pelo sistema. As máquinas geridas podem ficar presas se a Atualização não tiver direitos de reiniciar a máquina sem interação manual do utilizador.
>
> Para mais informações, consulte as definições de [Política do Grupo Configure para atualizações automáticas](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para o Linux, a máquina requer acesso a um repositório de atualização. O repositório de atualização pode ser privado ou público. TLS 1.1 ou TLS 1.2 é necessário para interagir com a Gestão de Atualizações. Um Agente de Log Analytics para o Linux que está configurado para reportar a mais de um espaço de trabalho log Analytics não é suportado com esta solução. A máquina também deve ter Python 2.x instalado.

Para obter informações sobre como instalar o agente Log Analytics para o Linux e para descarregar a versão mais recente, consulte o [agente Log Analytics para o Linux](../azure-monitor/platform/agent-linux.md). Para obter informações sobre como instalar o agente Log Analytics para windows, consulte [Connect Windows computers to Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Os VMs que foram criados a partir das imagens a pedido da Red Hat Enterprise Linux (RHEL) que estão disponíveis no Azure Marketplace estão registados para aceder à [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) que está implantada no Azure. Qualquer outra distribuição linux deve ser atualizada a partir do repositório de ficheiros online da distribuição utilizando os métodos suportados pela distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerir as implementações de atualizações, necessita de permissões específicas. Para conhecer estas permissões, consulte [o acesso baseado em Funções – Gestão de Atualizações.](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos seguintes recursos. Estes recursos são automaticamente adicionados à sua conta Deautomaquando ativa a solução. 

### <a name="hybrid-worker-groups"></a>Grupos de Função de Trabalho Híbrida

Depois de ativar esta solução, qualquer máquina Windows que esteja diretamente ligada ao seu espaço de trabalho Log Analytics é configurada automaticamente como um Trabalhador de RaquinaDo Híbrido para suportar os livros de execução que estão incluídos nesta solução.

Cada máquina Windows gerida pela solução está listada no painel de **grupos de trabalhadores híbridos** como um **grupo de trabalhadores híbridos system** para a conta Automation. As soluções utilizam o *nome anfitrião FQDN_GUID* convenção de nomeação. Não pode supor estes grupos com livros na sua conta. Se tentares, a tentativa falha. Estes grupos destinam-se a apoiar apenas esta solução de gestão.

Pode adicionar a máquina Windows a um grupo híbrido Runbook Worker na sua conta Automation para suportar os livros de execução automation se utilizar a mesma conta tanto para a solução como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada na versão 7.2.12024.0 do Trabalhador híbrido do Runbook Worker.

### <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão de Gestão de Operações do System Center estiver ligado a um espaço de [trabalho de Log Analytics,](../azure-monitor/platform/om-agents.md)os seguintes pacotes de gestão são instalados no Gestor de Operações. Estes pacotes de gestão também são instalados em máquinas Windows diretamente conectadas depois de adicionar a solução. Não é necessário configurar ou gerir estes pacotes de gestão.

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

> [!NOTE]
> Se tiver um grupo de gestão 1807 ou 2019 ligado a um espaço de trabalho de Log Analytics com agentes configurados no grupo de gestão para recolher dados de registo, precisa de anular a seguinte regra para os gerir com gestão de atualizações: Sobrepor-se ao parâmetro **IsAutoRegistrationEnabled** e definido para **True** na **regra Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Para obter mais informações sobre como os pacotes de gestão de soluções são atualizados, consulte [Connect Operations Manager para registos do Monitor Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para máquinas com o agente Operations Manger, a ser totalmente gerida pela Update Management, o agente deve ser atualizado para o agente Log Analytics para Windows ou Linux. Para aprender a atualizar o agente, consulte como atualizar um agente do Gestor de [Operações](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Em ambientes que utilizam o Gestor de Operações, deve estar a executar o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução recolhe informações sobre atualizações do sistema a partir de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução recolhe informações sobre atualizações do sistema a partir de agentes Linux e inicia a instalação de atualizações necessárias sobre distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br/><br/>Não é necessária uma ligação direta do agente do Gestor de Operações aos registos do Monitor Azure. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência da recolha

Uma varredura é realizada duas vezes por dia para cada máquina do Windows gerida. A cada 15 minutos, a API do Windows é chamada para consultar pela última vez a atualização para determinar se o estado mudou. Se o estado tiver mudado, é iniciado um exame de conformidade.

Uma varredura é realizada a cada hora para cada máquina linux gerida.

Pode levar entre 30 minutos e 6 horas para o painel de instrumentos apresentar dados atualizados a partir de máquinas geridas.

A utilização média de dados por registos do Monitor Azure para uma máquina que utilize a Atualização é de aproximadamente 25 megabytes (MB) por mês. Este valor é apenas uma aproximação e está sujeito a alterações, dependendo do seu ambiente. Recomendamos que monitorize o seu ambiente para acompanhar o seu uso exato. Para obter mais informações para analisar o uso de dados, consulte [Gerir o uso e o custo.](../azure-monitor/platform/manage-cost-storage.md)

## <a name="ports"></a>Planeamento da rede

Os seguintes endereços são necessários especificamente para gestão de atualizações. A comunicação a estes endereços ocorre sobre a porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Para as máquinas Windows, também deve permitir o tráfego em todos os pontos finais exigidos pelo Windows Update. Pode encontrar uma lista atualizada de pontos finais necessários em [Questões relacionadas com HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se tiver um [servidor local de Atualização do Windows,](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)também deve permitir o tráfego para o servidor especificado na sua chave [WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para as máquinas Red Hat Linux, consulte [os iPs para os servidores](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) de entrega de conteúdo RHUI para obter pontos finais necessários. Para outras distribuições linux, consulte a documentação do seu fornecedor.

Para obter mais informações sobre as portas necessárias para o Trabalhador do Livro Híbrido, consulte as portas de [função do Trabalhador Híbrido.](automation-hybrid-runbook-worker.md#hybrid-worker-role)

Recomendamos que utilize os endereços listados ao definir exceções. Para endereços IP, pode baixar [as gamas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente, e reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

Siga as instruções em [Connect computadores sem acesso](../azure-monitor/platform/gateway.md) à Internet para configurar máquinas que não tenham acesso à Internet.

## <a name="update-classifications"></a>Classificações de atualização

As tabelas seguintes listam as classificações de atualização em Gestão de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

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

### <a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionado com a segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou que não são atualizações de segurança.        |

Para o Linux, a Atualização de Gestão pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para remendar, a Atualização Management baseia-se nos dados de classificação disponíveis na máquina. Ao contrário de outras distribuições, o CentOS não dispõe desta informação na versão RTM. Se tiver máquinas CentOS configuradas para devolver os dados de segurança para o seguinte comando, a Atualização de Gestão pode corrigir com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente não existe um método suportado para permitir a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas é prestado um apoio de melhor esforço aos clientes que possam ter permitido isso por si próprios. 

Para classificar as atualizações na versão 6 da Red Hat Enterprise, é necessário instalar o plugin de segurança de yum. No Red Hat Enterprise Linux 7, o plugin já faz parte do próprio yum, não há necessidade de instalar nada. Para mais informações, consulte o seguinte artigo de [conhecimento](https://access.redhat.com/solutions/10021)do Chapéu Vermelho.

## <a name="integrate-with-configuration-manager"></a>Integrar no Configuration Manager

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerir Computadores, servidores e dispositivos móveis também dependem da força e maturidade do Gestor de Configuração para ajudá-los a gerir atualizações de software. O Gestor de Configuração faz parte do ciclo de gestão de atualizações de software (SUM).

Para aprender a integrar a solução de gestão com o Gestor de Configuração, consulte Integrar o Gestor de [Configuração com gestão de atualizações.](oms-solution-updatemgmt-sccmintegration.md)

### <a name="third-party-updates-on-windows"></a>Atualizações de terceiros no Windows

A Update Management conta com o repositório de atualização configurado localmente para atualizar os sistemas Windows suportados. Isto ou é WSUS ou Windows Update. Ferramentas como [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) permite-lhe importar e publicar atualizações personalizadas com a WSUS. Este cenário permite que a Atualização de Gestão atualize máquinas que utilizam o Gestor de Configuração como o seu repositório de atualização com software de terceiros. Para saber como configurar atualizações, consulte instalar [atualizações editora](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="onboard"></a>Ativar a Gestão de Atualizações

Para começar a atualizar os sistemas, é necessário ativar a solução de Gestão de Atualização. Seguem-se os métodos recomendados e suportados para embarcar na solução:

- [De uma máquina virtual](automation-onboard-solutions-from-vm.md)

- [Desde navegar em várias máquinas](automation-onboard-solutions-from-browse.md)

- [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)

- [Com um livro de execução azure Automation](automation-onboard-solutions.md)

- [Com um modelo de Gestor de Recursos Azure](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>Passos seguintes

Reveja as [FAQ](automation-faq.md) da Automação Azure para analisar questões comuns sobre esta solução.
