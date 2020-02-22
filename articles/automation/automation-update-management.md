---
title: Solução de Gestão de Atualização em Azure
description: Este artigo descreve como utilizar a solução Azure Update Management para gerir atualizações para os seus computadores Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: e7a86f3a709566bcf18241ce3c329c0355be2743
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539557"
---
# <a name="update-management-solution-in-azure"></a>Solução de Gestão de Atualização em Azure

Pode utilizar a solução Update Management no Azure Automation para gerir as atualizações do sistema operativo para os seus computadores Windows e Linux em Azure, em ambientes no local e em outros fornecedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Pode ativar a Gestão de Atualizações para máquinas virtuais (VMs) diretamente da sua conta De automação Azure. Para saber como, consulte ['Gerir actualizações' para várias máquinas virtuais](manage-update-multi.md). Também pode ativar a Atualização de Gestão para um VM a partir da página de máquinavirtual no portal Azure. Este cenário está disponível para [VMs Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

> [!NOTE]
> A solução Update Management requer a ligação de um espaço de trabalho de Log Analytics à sua conta Deautomação. Para obter uma lista definitiva de regiões apoiadas, consulte [os mapeamentos do Espaço de Trabalho Azure.](./how-to/region-mappings.md) Os mapeamentos da região não afetam a capacidade de gerir VMs numa região separada da sua conta de Automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Descrição geral da solução

Os computadores geridos pela Atualização management utilizam as seguintes configurações para realizar a avaliação e atualizar as implementações:

* Agente de Monitorização da Microsoft (MMA) para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux
* Função de Trabalho de Runbook Híbrida da Automatização
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama que se segue ilustra como a solução avalia e aplica atualizações de segurança a todos os computadores Windows Server e Linux conectados num espaço de trabalho:

![Fluxo de processo de gestão de atualização](./media/automation-update-management/update-mgmt-updateworkflow.png)

A Atualização Gestão pode ser usada para máquinas de bordo nativas em múltiplas subscrições no mesmo inquilino.

Depois de um pacote ser lançado, leva 2 a 3 horas para o patch aparecer para as máquinas Linux para avaliação. Para as máquinas Windows, o patch demora 12 a 15 horas para que o patch apareça para avaliação depois de lançado.

Depois de um computador completar uma varredura para a conformidade da atualização, o agente reencaminha as informações a granel para os registos do Monitor Azure. Num computador Windows, a varredura de conformidade é executada a cada 12 horas por padrão.

Além do calendário de digitalização, a varredura para a conformidade da atualização é iniciada dentro de 15 minutos após o reinício do MMA, antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a varredura de conformidade é realizada de hora a hora por padrão. Se o agente MMA for reiniciado, é iniciado um exame de conformidade dentro de 15 minutos.

A solução relata como o computador está atualizado com base na fonte com que está configurado para sincronizar. Se o computador Windows estiver configurado para reportar ao WSUS, dependendo da última sincronização da WSUS com o Microsoft Update, os resultados podem diferir do que o Microsoft Update mostra. Este comportamento é o mesmo para os computadores Linux que estão configurados para reportar a um repo local em vez de a um repo público.

> [!NOTE]
> Para comunicar adequadamente ao serviço, a Atualização management requer que alguns URLs e portas sejam ativados. Para saber mais sobre estes requisitos, consulte o [planeamento da Rede para Trabalhadores Híbridos.](automation-hybrid-runbook-worker.md#network-planning)

Pode criar uma implementação agendada para implementar e instalar atualizações de software em computadores que precisam das atualizações. As atualizações classificadas como *Opcional* não estão incluídas no âmbito de implementação dos computadores Windows. Apenas as atualizações necessárias estão incluídas no âmbito de implementação.

A implementação programada define quais os computadores-alvo que recebem as atualizações aplicáveis. Fá-lo especificando explicitamente certos computadores ou selecionando um [grupo informático](../azure-monitor/platform/computer-groups.md) baseado em pesquisas de registo de um conjunto específico de computadores (ou numa [consulta Azure](automation-update-management-query-logs.md) que seleciona dinamicamente VMs Azure com base em critérios especificados). Estes grupos diferem da configuração do [âmbito,](../azure-monitor/insights/solution-targeting.md)que é usada apenas para determinar quais as máquinas que obtêm os pacotes de gestão que permitem a solução.

Especifica também um calendário para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Este período chama-se janela de manutenção. Um espaço de 20 minutos da janela de manutenção é reservado para reboots, assumindo que um é necessário e selecionou a opção de reinicialização apropriada. Se o remendo demorar mais do que o esperado e houver menos de 20 minutos na janela de manutenção, não ocorrerá um reboot.

As atualizações são instaladas por runbooks na Automatização do Azure. Não se pode ver estes livros, e eles não precisam de qualquer configuração. Quando uma implementação de atualização é criada, cria uma programação que inicia um livro de execução de atualização principal no momento especificado para os computadores incluídos. O livro de execução principal inicia um livro de corridas para crianças em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implementação da atualização, os computadores-alvo executam a implementação paralelamente. Antes da instalação, é executada uma verificação para verificar se as atualizações ainda são necessárias. Para os computadores clientes da WSUS, se as atualizações não forem aprovadas no WSUS, a implementação da atualização falha.

Ter uma máquina registada para Gestão de Atualizações em mais de um espaço de trabalho log Analytics (multihoming) não é suportado.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela seguinte lista os sistemas operativos suportados para avaliações de atualizações. Remendar requer um Trabalhador Híbrido do Livro de Corridas. Para obter informações sobre os requisitos do Trabalhador do Livro de Execução Híbrido, consulte os guias de instalação para instalar um Trabalhador do [Livro híbrido do Windows](automation-windows-hrw-install.md) e um Trabalhador de [Runbook Híbrido Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM e SP1 Standard)| A Atualização Management apenas suporta a realização de avaliações para este sistema operativo, patching não é suportado uma vez que o [Hybrid Runbook Worker](automation-windows-hrw-install.md) não é suportado para o Windows Server 2008 R2. |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A correção baseada na classificação requer `yum` para devolver dados de segurança que o CentOS não tem nas suas versões RTM. Para obter mais informações sobre patching baseado na classificação no CentOS, consulte [classificações de atualização no Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

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

Os agentes do Windows devem ser configurados para comunicar com um servidor WSUS, ou devem ter acesso ao Microsoft Update.

Você pode usar Gerenciamento de Atualizações com Configuration Manager. Para saber mais sobre cenários de integração, consulte Integrar o Gestor de [Configuração com gestão de atualizações.](oms-solution-updatemgmt-sccmintegration.md#configuration) O [agente windows](../azure-monitor/platform/agent-windows.md) é necessário. O agente é instalado automaticamente se estiver a embarcar num VM Azure.

Por predefinição, os VMs do Windows que são implementados a partir do Mercado Azure estão definidos para receber atualizações automáticas do Windows Update Service. Este comportamento não muda quando adiciona esta solução ou adiciona VMs do Windows ao seu espaço de trabalho. Se não gerir ativamente as atualizações utilizando esta solução, o comportamento predefinido (para aplicar automaticamente atualizações) aplica-se.

> [!NOTE]
> Um utilizador pode modificar a Política de Grupo para que as reinicializações da máquina possam ser realizadas apenas pelo utilizador e não pelo sistema. As máquinas geridas podem ficar presas se a Atualização não tiver direitos de reiniciar a máquina sem interação manual do utilizador.
>
> Para mais informações, consulte as definições de [Política do Grupo Configure para atualizações automáticas](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para o Linux, a máquina deve ter acesso a um repositório de atualização. O repositório de atualização pode ser privado ou público. TLS 1.1 ou TLS 1.2 é necessário para interagir com a Gestão de Atualizações. Um Agente de Log Analytics para o Linux que está configurado para reportar a mais de um espaço de trabalho log Analytics não é suportado com esta solução. A máquina também deve ter Python 2.x instalado.

Para obter informações sobre como instalar o Agente de Análise de Registo sintetizada para o Linux e para descarregar a versão mais recente, consulte o [Agente Delog Analytics para o Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o Agente De saúde em Diário da Madeira para windows, consulte [connect Windows computers to Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Os VMs que foram criados a partir das imagens a pedido da Red Hat Enterprise Linux (RHEL) que estão disponíveis no Azure Marketplace estão registados para aceder à [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) que está implantada no Azure. Qualquer outra distribuição linux deve ser atualizada a partir do repositório de ficheiros online da distribuição utilizando os métodos suportados pela distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerir as implementações de atualizações, necessita de permissões específicas. Para conhecer estas permissões, consulte [o acesso baseado em Funções – Gestão de Atualizações.](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos seguintes recursos. Os recursos são adicionados à sua conta de Automação. Ou são agentes ligados diretamente ou num grupo de gestão ligado ao Diretor de Operações.

### <a name="hybrid-worker-groups"></a>Grupos de Função de Trabalho Híbrida

Depois de ativar esta solução, qualquer computador Windows que esteja diretamente ligado ao seu espaço de trabalho Log Analytics é automaticamente configurado como um Trabalhador de RaquinaDo Híbrido para suportar os livros de execução que estão incluídos nesta solução.

Cada computador Windows gerido pela solução está listado no painel de **grupos de trabalhadores híbridos** como um **grupo de trabalhadores híbridos system** para a conta Automation. As soluções utilizam o *nome anfitrião FQDN_GUID* convenção de nomeação. Não pode supor estes grupos com livros na sua conta. Se tentares, a tentativa falha. Estes grupos destinam-se a apoiar apenas a solução de gestão.

Pode adicionar os computadores Windows a um grupo Híbrido Runbook Worker na sua conta Automation para suportar os livros de execução automation se utilizar a mesma conta tanto para a solução como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada na versão 7.2.12024.0 do Trabalhador híbrido do Runbook Worker.

### <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão de Gestão de Operações do System Center estiver ligado a um espaço de trabalho de Log Analytics, os seguintes pacotes de gestão são instalados no Gestor de Operações. Estes pacotes de gestão também são instalados em computadores Windows diretamente ligados depois de adicionar a solução. Não é necessário configurar ou gerir estes pacotes de gestão.

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

> [!NOTE]
> Assuma que tem um gestor de operações 1807 ou 2019 com agentes configurados ao nível do Grupo de Gestão para os associar a um espaço de trabalho. A suposição atual para que apareçam é sobrepor-se à **regra IsAutoRegistrationEnabled** to **True** na **regra Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init.**

Para obter mais informações sobre como os pacotes de gestão de soluções são atualizados, consulte [Connect Operations Manager para registos do Monitor Azure](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para sistemas com o Agente Manger de Operações: Para que um agente seja totalmente gerido pela Atualização, o agente deve ser atualizado para o MMA. Para aprender a atualizar o agente, consulte como atualizar um agente do Gestor de [Operações](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Em ambientes que utilizam o Gestor de Operações, deve estar a executar o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução:

| Origem ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução recolhe informações sobre atualizações do sistema a partir de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução recolhe informações sobre atualizações do sistema a partir de agentes Linux e inicia a instalação de atualizações necessárias sobre distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br/><br/>Não é necessária uma ligação direta do agente do Gestor de Operações aos registos do Monitor Azure. Os dados são reencaminhados do grupo de gestão para a área de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência da recolha

Uma varredura é realizada duas vezes por dia para cada computador windows gerido. A cada 15 minutos, a API do Windows é chamada para consultar pela última vez a atualização para determinar se o estado mudou. Se o estado tiver mudado, é iniciado um exame de conformidade.

Uma varredura é realizada a cada hora para cada computador Linux gerido.

Pode levar entre 30 minutos e 6 horas para o painel de instrumentos apresentar dados atualizados a partir de computadores geridos.

A utilização média de dados por registos do Monitor Azure para uma máquina que utilize a Atualização é de aproximadamente 25 megabytes (MB) por mês. Este valor é apenas uma aproximação e está sujeito a alterações, dependendo do seu ambiente. Recomendamos que monitorize o seu ambiente para acompanhar o seu uso exato.

## <a name="ports"></a>Planeamento da rede

Os seguintes endereços são necessários especificamente para gestão de atualizações. A comunicação a estes endereços ocorre sobre a porta 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Para as máquinas Windows, também deve permitir o tráfego em todos os pontos finais exigidos pelo Windows Update. Pode encontrar uma lista atualizada de pontos finais necessários em [Questões relacionadas com HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se tiver um [servidor local de Atualização do Windows,](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)também deve permitir o tráfego para o servidor especificado na sua chave [WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para as máquinas Red Hat Linux, consulte [os iPs para os servidores](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) de entrega de conteúdo RHUI para obter pontos finais necessários. Para outras distribuições linux, consulte a documentação do seu fornecedor.

Para obter mais informações sobre as portas que o Trabalhador do Livro Híbrido necessita, consulte portas de [função de Trabalhador Híbrido](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Recomendamos que utilize os endereços listados ao definir exceções. Para endereços IP, pode baixar [as gamas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente, e reflete as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

Siga as instruções em [Connect computadores sem acesso](../azure-monitor/platform/gateway.md) à Internet para configurar máquinas que não tenham acesso à Internet.

## <a name="view-update-assessments"></a>Ver avaliações de atualizações

Na sua conta De automatização, selecione Gestão de **Atualização** para ver o estado das suas máquinas.

Esta visão fornece informações sobre as suas máquinas, atualizações em falta, implementações de atualizações e implementações de atualizações programadas. Na coluna **COMPLIANCE,** pode ver a última vez que a máquina foi avaliada. Na coluna DE PRONTIDÃO DO **AGENTE DE Atualização,** pode verificar a saúde do agente de atualização. Se houver algum problema, selecione o link para ir à documentação de resolução de problemas que pode ajudá-lo a corrigir o problema.

Para executar uma pesquisa de registo que devolve informações sobre a máquina, atualização ou implementação, selecione o item correspondente na lista. O painel de pesquisa de **registo** abre com uma consulta para o item selecionado:

![Visão padrão de gestão de atualização](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Ver atualizações em falta

Selecione **as atualizações Em falta** para visualizar a lista de atualizações que faltam nas suas máquinas. Cada atualização está listada e pode ser selecionada. São mostradas informações sobre o número de máquinas que requerem a atualização, o sistema operativo e um link para mais informações. O painel de **pesquisa de Registo** mostra mais detalhes sobre as atualizações.

![Atualizações em falta](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

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

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e da maturidade de Configuration Manager para ajudá-los a gerenciar atualizações de software. O Gestor de Configuração faz parte do ciclo de gestão de atualizações de software (SUM).

Para aprender a integrar a solução de gestão com o Gestor de Configuração, consulte Integrar o Gestor de [Configuração com gestão de atualizações.](oms-solution-updatemgmt-sccmintegration.md)

### <a name="third-party-patches-on-windows"></a>Patches de terceiros no Windows

A Update Management baseia-se no repositório de atualização configurado localmente para corrigir sistemas Windows suportados. Isto ou é WSUS ou Windows Update. Ferramentas como [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) permitem-lhe publicar atualizações personalizadas no WSUS. Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros. Para saber como configurar atualizações, consulte instalar [atualizações editora](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Máquinas Patch Linux

As seguintes secções explicam potenciais problemas com distros linux de remendar.

### <a name="unexpected-os-level-upgrades"></a>Atualizações inesperadas ao nível do OS

Em algumas variantes do Linux, como red hat enterprise linux, as atualizações de nível OS podem ocorrer através de pacotes. Isto pode levar a atualizações de gestão onde o número da versão OS muda. Uma vez que a Atualização utiliza os mesmos métodos para atualizar pacotes que um administrador usaria localmente no computador Linux, este comportamento é intencional.

Para evitar atualizar a versão OS através de 'Actualizações' de Gestão, utilize a função **Exclusão.**

Em Red Hat Enterprise Linux, o nome do pacote a excluir é redhat-release-server.x86_64.

![Pacotes a excluir para Linux](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Patches críticos/de segurança não são aplicados

Quando implementa atualizações para uma máquina Linux, pode selecionar classificações de atualização. Esta opção filtra as atualizações que são aplicadas à máquina que cumprem os critérios especificados. Este filtro é aplicado localmente na máquina quando a atualização é implementada.

Como a Update Management realiza o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas na Gestão de Atualização como tendo um impacto de segurança, mesmo que a máquina local não tenha essa informação. Como resultado, se aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança nessa máquina e, portanto, as atualizações não são aplicadas. No entanto, a Atualização de Gestão pode ainda reportar que a máquina não está em conformidade porque tem informações adicionais sobre a atualização relevante.

Implementar atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implementar corretamente atualizações para o CentOS, selecione todas as classificações para se certificar de que as atualizações são aplicadas. Para o SUSE, selecionar *apenas* **outras atualizações,** uma vez que a classificação pode fazer com que algumas atualizações de segurança também sejam instaladas se forem necessárias atualizações de segurança relacionadas com o zypper (gestor de pacotes) ou as suas dependências primeiro. Este comportamento é uma limitação do zigoto. Em alguns casos, poderá ser necessário reexecutar a implementação da atualização. Para verificar, verifique o registo da atualização.

### <a name="multi-tenant"></a>Implementações de atualizações de inquilinos cruzados

Se tiver máquinas noutro inquilino azure a reportar à Atualização de Gestão que precisa de corrigir, terá de utilizar a seguinte seleção para as marcar. Pode utilizar o [cmdlet New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com o interruptor `-ForUpdate` para criar uma programação e utilizar o [cmdlet New-AzureRmAutomationSoftwareUpdateE](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar as máquinas do outro inquilino para o parâmetro `-NonAzureComputer`. O exemplo que se segue mostra como fazê-lo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Ativar a Gestão de Atualizações

Para iniciar sistemas de remendos, é necessário ativar a solução De Gestão de Atualizações. Existem muitas formas de embarcar nas máquinas para atualizar a Gestão. Seguem-se as formas recomendadas e apoiadas de embarcar na solução:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Desde navegar em várias máquinas](automation-onboard-solutions-from-browse.md)
* [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Com um livro de execução azure Automation](automation-onboard-solutions.md)

## <a name="next-steps"></a>Passos seguintes

Utilize o seguinte tutorial para aprender a gerir as atualizações para os seus VMs do Windows:

> [!div class="nextstepaction"]
> [Gerir atualizações e patches para os seus VMs Do Windows Azure](automation-tutorial-update-management.md)

* Utilize pesquisas de registo nos [registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md) para visualizar dados de atualização detalhados.
* [Crie alertas](automation-tutorial-update-management.md#configure-alerts) para o estado de implementação da atualização.

* Para aprender a interagir com a Gestão de Atualizações através da API REST, consulte [as configurações](/rest/api/automation/softwareupdateconfigurations)de atualização do Software.
* Para aprender a resolver problemas na Atualização, consulte a [Resolução de Problemas de Gestão](troubleshoot/update-management.md)de Atualizações .
