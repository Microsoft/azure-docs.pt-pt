---
title: Utilização da solução de Mapa de Serviço em Azure Microsoft Docs
description: O Mapa de Serviços é uma solução no Azure que deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes para implementar o Mapa de Serviços no seu ambiente e usá-lo em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: f7268f698dcc26dbe99b517c9dd4584be67c3a82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448466"
---
# <a name="using-service-map-solution-in-azure"></a>Utilizar a solução Mapa de Serviços no Azure

O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Com o Mapa de Serviços, pode ver os seus servidores da mesma forma como os conceptualiza: como sistemas interligados que fornecem serviços críticos. O Mapa de Serviços mostra as ligações entre os servidores, os processos, a latência da ligação de entrada e de saída e as portas em qualquer arquitetura ligada por TCP, em que a única configuração necessária é a instalação de um agente.

Este artigo descreve os detalhes do embarque e utilização do Mapa de Serviços. Os pré-requisitos da solução são os seguintes:

* Um espaço de trabalho Log Analytics numa [região apoiada.](vminsights-configure-workspace.md#supported-regions)

* O [agente Log Analytics](vminsights-enable-overview.md#agents) instalado no computador Windows ou servidor Linux ligado ao mesmo espaço de trabalho com o que ativou a solução.

* O [agente Desapeito](vminsights-enable-overview.md#agents) instalado no computador Windows ou no servidor Linux.

>[!NOTE]
>Se já implementou o Mapa de Serviços, pode agora também ver os seus mapas no Azure Monitor para VMs, que inclui funcionalidades adicionais para monitorizar a saúde e o desempenho do VM. Para saber mais, consulte [o Azure Monitor para obter uma visão geral dos VMs](./vminsights-overview.md). Para saber mais sobre as diferenças entre a solução de Mapa de Serviço e o Monitor Azure para a funcionalidade mapa de VMs, consulte as seguintes [FAQ](../faq.md#azure-monitor-for-vms).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Ativar mapa de serviço

1. Ativar a solução de Mapa de Serviços a partir do [Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) ou utilizando o processo descrito nas [soluções de monitorização Add da Galeria de Soluções.](solutions.md)
1. [Instale o agente Desac dependência no Windows](./vminsights-enable-hybrid.md#install-the-dependency-agent-on-windows) ou [instale o agente Desadependição no Linux](./vminsights-enable-hybrid.md#install-the-dependency-agent-on-linux) em cada computador onde pretende obter dados. O Agente de Dependência pode monitorizar as ligações dos vizinhos imediatos, pelo que poderá não precisar de um agente em cada computador.

Aceda ao Mapa de Serviços no portal Azure a partir do seu espaço de trabalho Log Analytics e selecione as **soluções** de opção a partir do painel esquerdo.<br><br> ![Selecione a opção Soluções no espaço de ](./media/service-map/select-solution-from-workspace.png) trabalho.<br> A partir da lista de soluções, selecione **ServiceMap (workspaceName)** e na página de visão geral do Mapa de Serviço clique no tile resumo do Mapa de Serviço.<br><br> ![Azulejo sumário do mapa de serviço ](./media/service-map/service-map-summary-tile.png) .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de utilização: Sensibilizar os seus processos de TI para a dependência

### <a name="discovery"></a>Deteção

O Mapa de Serviços constrói automaticamente um mapa comum de dependências através dos seus servidores, processos e serviços de terceiros. Descobre e mapeia todas as dependências da TCP, identificando ligações surpresa, sistemas remotos de terceiros de que depende, e dependências para áreas escuras tradicionais da sua rede, como o Ative Directory. O Mapa de Serviços descobre ligações de rede falhadas que os seus sistemas geridos estão a tentar fazer, ajudando-o a identificar potenciais erros de configuração do servidor, falha de serviço e problemas de rede.

### <a name="incident-management"></a>Gestão de Incidentes

O Mapa de Serviços ajuda a eliminar as suposições do isolamento de problemas mostrando-lhe como os sistemas estão conectados e afetando uns aos outros. Além de identificar ligações falhadas, ajuda a identificar equilibradores de carga mal configurados, carga surpreendente ou excessiva em serviços críticos, e clientes fraudulentos, como máquinas de desenvolvimento que falam com sistemas de produção. Ao utilizar fluxos de trabalho integrados com Change Tracking, também pode ver se um evento de mudança numa máquina ou serviço de back-end explica a causa principal de um incidente.

### <a name="migration-assurance"></a>Garantia migratória

Ao utilizar o Mapa de Serviços, pode efetivamente planear, acelerar e validar migrações do Azure, o que ajuda a garantir que nada seja deixado para trás e que não ocorram interrupções surpresa. Pode descobrir todos os sistemas interdependentes que precisam de migrar em conjunto, avaliar a configuração e capacidade do sistema e identificar se um sistema de execução ainda está a servir os utilizadores ou se é candidato ao desmantelamento em vez de migração. Após a conclusão da mudança, pode verificar a carga e identidade do cliente para verificar se os sistemas de teste e os clientes estão a ligar-se. Se as definições de planeamento e firewall da sub-rede tiverem problemas, as ligações falhadas nos mapas do Mapa de Serviço apontam-no para os sistemas que necessitam de conectividade.

### <a name="business-continuity"></a>Continuidade do negócio

Se estiver a utilizar a Recuperação do Site Azure e precisar de ajuda para definir a sequência de recuperação para o ambiente da sua aplicação, o Mapa de Serviço pode automaticamente mostrar-lhe como os sistemas dependem uns dos outros para garantir que o seu plano de recuperação é fiável. Ao escolher um servidor ou grupo crítico e visualizar os seus clientes, pode identificar quais os sistemas frontais a recuperar após a restauração e disponibilização do servidor. Inversamente, ao analisar as dependências de back-end dos servidores críticos, é possível identificar quais os sistemas a recuperar antes de os seus sistemas de foco serem restaurados.

### <a name="patch-management"></a>Gestão de patches

O Mapa de Serviços melhora a sua utilização da Avaliação de Atualização do Sistema, mostrando-lhe quais outras equipas e servidores dependem do seu serviço, para que possa notificá-los com antecedência antes de retirar os seus sistemas para remendamento. O Mapa de Serviços também melhora a gestão de patchs mostrando-lhe se os seus serviços estão disponíveis e devidamente ligados depois de serem remendados e reiniciados.

## <a name="mapping-overview"></a>Visão geral do mapeamento

Os agentes do Mapa de Serviço recolhem informações sobre todos os processos ligados à TCP no servidor onde estão instalados e detalhes sobre as ligações de entrada e saída para cada processo.

A partir da lista no painel esquerdo, pode selecionar máquinas ou grupos que tenham agentes do Mapa de Serviço para visualizar as suas dependências ao longo de um intervalo de tempo especificado. Os mapas de dependência da máquina focam-se numa máquina específica, e mostram todas as máquinas que são clientes TCP ou servidores diretos dessa máquina.  Os mapas do Grupo De Máquinas mostram conjuntos de servidores e suas dependências.

![Visão geral do mapa de serviço](media/service-map/service-map-overview.png)

As máquinas podem ser expandidas no mapa para mostrar os grupos de processos de execução e processos com ligações de rede ativas durante o intervalo de tempo selecionado. Quando uma máquina remota com um agente do Mapa de Serviço é expandida para mostrar detalhes do processo, apenas são mostrados os processos que comunicam com a máquina de focagem. A contagem de máquinas frontais sem agente que se ligam à máquina de focagem é indicada no lado esquerdo dos processos a que se ligam. Se a máquina de focagem estiver a fazer uma ligação a uma máquina de back-end que não tenha agente, o servidor de back-end está incluído num Grupo de Portas do Servidor, juntamente com outras ligações ao mesmo número de porta.

Por predefinição, os mapas do Mapa de Serviço mostram os últimos 30 minutos de informação de dependência. Ao utilizar os controlos de tempo na parte superior esquerda, pode consultar mapas para intervalos históricos de até uma hora para mostrar como as dependências pareciam no passado (por exemplo, durante um incidente ou antes de ocorrer uma mudança). Os dados do Mapa de Serviço são armazenados durante 30 dias em espaços de trabalho pagos e durante 7 dias em espaços de trabalho gratuitos.

## <a name="status-badges-and-border-coloring"></a>Crachás de estado e coloração na fronteira

Na parte inferior de cada servidor no mapa pode ser uma lista de crachás de estado que transmitem informações sobre o estado do servidor. Os crachás indicam que existem algumas informações relevantes para o servidor a partir de uma das integrações de soluções. Clicar num crachá leva-o diretamente aos detalhes do estado no painel direito. Os crachás de estado atualmente disponíveis incluem Alertas, Balcão de Serviço, Alterações, Segurança e Atualizações.

Dependendo da gravidade dos crachás de estado, as bordas dos nóns de máquina podem ser coloridas de vermelho (crítico), amarelo (aviso) ou azul (informativo). A cor representa o estado mais grave de qualquer um dos crachás de estado. Uma borda cinzenta indica um nó que não tem indicadores de estado.

![Emblemas de estado](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupos de Processo

Os Grupos de Processo combinam processos associados a um produto ou serviço comum num grupo de processos.  Quando um nó de máquina é expandido, apresentará processos autónomos juntamente com grupos de processo.  Se alguma ligação de entrada e saída a um processo dentro de um grupo de processos falhou, então a ligação é mostrada como falhada para todo o grupo de processos.

## <a name="machine-groups"></a>Grupos de Máquinas

Os Grupos de Máquinas permitem-lhe ver mapas centrados em torno de um conjunto de servidores, não apenas um para que possa ver todos os membros de uma aplicação de vários níveis ou cluster de servidores num mapa.

Os utilizadores selecionam quais os servidores que pertencem a um grupo em conjunto e escolhem um nome para o grupo.  Em seguida, pode optar por visualizar o grupo com todos os seus processos e ligações, ou vê-lo apenas com os processos e ligações que se relacionam diretamente com os outros membros do grupo.

![Grupo de Máquinas](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Criação de um Grupo de Máquinas

Para criar um grupo, selecione a máquina ou máquinas que deseja na lista de Máquinas e clique em **Adicionar ao grupo**.

![Criar Grupo](media/service-map/machine-groups-create.png)

Lá, pode escolher **Criar novo** e dar um nome ao grupo.

![Grupo de Nome](media/service-map/machine-groups-name.png)

>[!NOTE]
>Os grupos de máquinas estão limitados a 10 servidores.

### <a name="viewing-a-group"></a>Visualização de um Grupo

Uma vez criados alguns grupos, pode vê-los escolhendo o separador Grupos.

![Separador de grupos](media/service-map/machine-groups-tab.png)

Em seguida, selecione o nome do Grupo para visualizar o mapa para esse Grupo de Máquinas.
![Grupo máquina ](media/service-map/machine-group.png) As máquinas que pertencem ao grupo estão delineadas a branco no mapa.

A expansão do Grupo irá listar as máquinas que compõem o Grupo Máquina.

![Máquinas do Grupo máquina](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrar por processos

Pode alternar a visão do mapa entre mostrar todos os processos e ligações do Grupo e apenas os que se relacionam diretamente com o Grupo máquina.  A vista padrão é mostrar todos os processos.  Pode alterar a vista clicando no ícone do filtro acima do mapa.

![Grupo filtro](media/service-map/machine-groups-filter.png)

Quando todos os processos estiverem **selecionados,** o mapa incluirá todos os processos e ligações em cada uma das máquinas do Grupo.

![Grupo de Máquinas todos os processos](media/service-map/machine-groups-all.png)

Se alterar a visão para mostrar **apenas processos ligados**ao grupo, o mapa será reduzido apenas aos processos e ligações que estão diretamente ligados a outras máquinas do grupo, criando uma visão simplificada.

![Processos filtrados do Grupo máquina](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Adicionar máquinas a um grupo

Para adicionar máquinas a um grupo existente, verifique as caixas ao lado das máquinas desejada e, em seguida, clique em **Adicionar ao grupo**.  Em seguida, escolha o grupo a que pretende adicionar as máquinas.
 
### <a name="removing-machines-from-a-group"></a>Remoção de máquinas de um grupo

Na Lista de Grupos, expanda o nome do grupo para listar as máquinas do Grupo máquina.  Em seguida, clique no menu de elipses ao lado da máquina que pretende remover e escolher **Remover**.

![Remover máquina do grupo](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Remover ou renomear um grupo

Clique no menu de elipses ao lado do nome de grupo na Lista de Grupos.

![Menu de grupo de máquinas](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ícones de papel

Certos processos servem papéis específicos em máquinas: servidores web, servidores de aplicações, base de dados, e assim por diante. O Mapa de Serviço anota o processo e as caixas de máquinas com ícones de função para ajudar a identificar de relance o papel que um processo ou servidor desempenha.

| Ícone de papel | Descrição |
|:--|:--|
| ![Servidor Web](media/service-map/role-web-server.png) | Servidor Web |
| ![Servidor de aplicações](media/service-map/role-application-server.png) | Servidor de aplicações |
| ![Servidor de base de dados](media/service-map/role-database.png) | Servidor de base de dados |
| ![Servidor LDAP](media/service-map/role-ldap.png) | Servidor LDAP |
| ![Servidor SMB](media/service-map/role-smb.png) | Servidor SMB |

![Ícones de papel](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Ligações falhadas

As ligações falhadas são mostradas nos mapas do Mapa de Serviço para processos e computadores, com uma linha vermelha tracejada indicando que um sistema de clientes não está a conseguir chegar a um processo ou porta. As ligações falhadas são reportadas a partir de qualquer sistema com um agente do Mapa de Serviço implantado se esse sistema for o único a tentar a ligação falhada. O Mapa de Serviço mede este processo observando tomadas TCP que não estabelecem uma ligação. Esta falha pode resultar de uma firewall, de uma configuração errada no cliente ou servidor ou de um serviço remoto indisponível.

![Screenshot de uma parte de um Mapa de Serviço realçando uma linha vermelha tracejada que indica uma ligação falhada entre o processo backup.pl e o Porto 4475.](media/service-map/failed-connections.png)

Compreender as ligações falhadas pode ajudar na resolução de problemas, validação de migração, análise de segurança e compreensão arquitetónica geral. As ligações falhadas são por vezes inofensivas, mas muitas vezes apontam diretamente para um problema, como um ambiente de falha súbita tornando-se subitamente inacessível, ou dois níveis de aplicação incapazes de falar após uma migração em nuvem.

## <a name="client-groups"></a>Grupos de Clientes

Os Grupos de Clientes são caixas no mapa que representam máquinas de clientes que não têm Agentes de Dependência. Um único Grupo cliente representa os clientes para um processo ou máquina individual.

![Grupos de Clientes](media/service-map/client-groups.png)

Para ver os endereços IP dos servidores num Grupo cliente, selecione o grupo. O conteúdo do grupo está listado no painel **De Propriedades do Grupo cliente.**

![Propriedades do Grupo cliente](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de porta de servidor

Os Grupos de Portas do Servidor são caixas que representam portas de servidores em servidores que não têm Agentes de Dependência. A caixa contém a porta do servidor e uma contagem do número de servidores com ligações a essa porta. Expanda a caixa para ver os servidores e ligações individuais. Se houver apenas um servidor na caixa, o nome ou endereço IP está listado.

![Grupos de porta de servidor](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu de contexto

Clicar na elipse (...) no canto superior direito de qualquer servidor exibe o menu de contexto para esse servidor.

![Screenshot mostrando o menu de contexto aberto para um servidor no Mapa de Serviço. O menu tem as opções Carregar mapa do servidor e mostrar auto-ligações.](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Mapa do servidor de carga

Clicar no **Mapa do Servidor de Carga** leva-o a um novo mapa com o servidor selecionado como a nova máquina de focagem.

### <a name="show-self-links"></a>Mostrar auto-links

Clicar em **'Mostrar Auto-Links'** retira o nó do servidor, incluindo quaisquer ligações auto-ligadas, que são ligações TCP que iniciam e terminam em processos dentro do servidor. Se forem mostradas ligações auto-ligações, o comando do menu muda para **Ocultar Ligações De Si mesmos,** para que possa desligá-los.

## <a name="computer-summary"></a>Resumo do computador

O painel **de resumo da máquina** inclui uma visão geral do sistema operativo de um servidor, contagem de dependência e dados de outras soluções. Esses dados incluem métricas de desempenho, bilhetes de serviço, rastreio de alterações, segurança e atualizações.

![Painel de resumo da máquina](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriedades de computador e processo

Ao navegar num mapa do Mapa de Serviços, pode selecionar máquinas e processos para ganhar contexto adicional sobre as suas propriedades. As máquinas fornecem informações sobre o nome DNS, endereços IPv4, CPU e capacidade de memória, tipo VM, sistema operativo e versão, última hora de reinicialização, e os IDs dos seus agentes OMS e Service Map.

![Painel de propriedades da máquina](media/service-map/machine-properties.png)

Pode recolher detalhes do processo a partir de metadados do sistema operativo sobre processos de execução, incluindo nome de processo, descrição do processo, nome do utilizador e domínio (no Windows), nome da empresa, nome do produto, versão do produto, diretório de trabalho, linha de comando e tempo de início do processo.

![Painel de propriedades de processo](media/service-map/process-properties.png)

O **painel de resumo** do processo fornece informações adicionais sobre a conectividade do processo, incluindo as suas portas ligadas, ligações de entrada e saída e ligações falhadas.

![Painel de resumo do processo](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integração de alertas

O Mapa de Serviços integra-se com alertas Azure para mostrar alertas disparados para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone se houver alertas atuais e o painel **de alertas** de máquina lista os alertas.

![Painel de alertas de máquina](media/service-map/machine-alerts.png)

Para permitir que o Mapa de Serviço apresente alertas relevantes, crie uma regra de alerta que dispare para um computador específico. Para criar alertas adequados:
- Incluir uma cláusula para grupo por computador (por exemplo, **por intervalo de computador 1 minuto).**
- Opte por alertar com base na medição métrica.

## <a name="log-events-integration"></a>Integração de eventos de registo

O Mapa de Serviços integra-se com a Log Search para mostrar uma contagem de todos os eventos de registo disponíveis para o servidor selecionado durante o intervalo de tempo selecionado. Pode clicar em qualquer linha da lista de contagens de eventos para saltar para registar e ver os eventos de registo individual.

![Painel de eventos de registo de máquinas](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integração do Balcão de Serviço

A integração do Mapa de Serviço com o Conector de Gestão de Serviços de TI é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics. A integração no Mapa de Serviços está rotulada como "Service Desk". Para obter mais informações, consulte [Centralmente gerir itens de trabalho ITSM utilizando o Conector de Gestão de Serviços de TI.](../platform/itsmc-overview.md)

O **painel de serviço de máquinas** lista todos os eventos de Gestão de Serviços de TI para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone se existirem itens atuais e o painel de serviço de máquinas os lista.

![Painel de mesa de serviço de máquina](media/service-map/service-desk.png)

Para abrir o artigo na solução ITSM ligada, clique em **Ver Artigo de Trabalho**.

Para ver os detalhes do item na Pesquisa de Registo, clique **em Mostrar na Pesquisa de Registo**.
As métricas de ligação são escritas para duas novas tabelas no Log Analytics 

## <a name="change-tracking-integration"></a>Integração de Change Tracking

A integração do Mapa de Serviço com o Change Tracking é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics.

O **painel de rastreio de mudança de máquina** lista todas as alterações, com a primeira mais recente, juntamente com um link para perfurar para registar procurar detalhes adicionais.

![Screenshot do painel de rastreio de mudança de máquina no mapa de serviço.](media/service-map/change-tracking.png)

A imagem a seguir é uma visão detalhada de um evento ConfigurationChange que poderá ver depois de selecionar **Show in Log Analytics**.

![Evento ConfigurationChange](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integração de desempenho

O **painel de desempenho** da máquina apresenta métricas de desempenho padrão para o servidor selecionado. As métricas incluem utilização de CPU, utilização de memória, bytes de rede enviados e recebidos, e uma lista dos principais processos por bytes de rede enviados e recebidos.

![Painel de desempenho da máquina](media/service-map/machine-performance.png)

Para ver os dados de desempenho, poderá ser necessário [ativar os contadores de desempenho apropriados do Log Analytics](../platform/data-sources-performance-counters.md).  Os contadores que pretende ativar:

Windows:
- Processador(*) \\ % tempo do processador
- Memória \\ % Bytes comprometidos em uso
- Adaptador de rede(*) \\ Bytes Enviados/seg
- Adaptador de rede(*) \\ Bytes Recebidos/seg

Linux:
- Processador(*) \\ % tempo do processador
- Memória (*) \\ % Memória Usada
- Adaptador de rede(*) \\ Bytes Enviados/seg
- Adaptador de rede(*) \\ Bytes Recebidos/seg

Para obter os dados de desempenho da rede, também deve ter ativado a solução De Dados de Fio 2.0 no seu espaço de trabalho.
 
## <a name="security-integration"></a>Integração de segurança

A integração do Mapa de Serviço com Segurança e Auditoria é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics.

O painel **de segurança** da máquina mostra dados da solução de Segurança e Auditoria para o servidor selecionado. O painel lista um resumo de quaisquer problemas de segurança pendentes para o servidor durante o intervalo de tempo selecionado. Clicar em qualquer um dos problemas de segurança perfura-se numa Pesquisa de Registo para obter detalhes sobre eles.

![Painel de segurança da máquina](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integração de atualizações

A integração do Mapa de Serviço com a Gestão de Atualização é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics.

O **painel de atualizações** da máquina exibe dados da solução de Gestão de Atualização para o servidor selecionado. O painel lista um resumo de quaisquer atualizações em falta para o servidor durante o intervalo de tempo selecionado.

![Screenshot do painel de atualizações da máquina no mapa de serviço.](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registos do Log Analytics

O computador do Mapa de Serviço e os dados de inventário de processos estão disponíveis para [pesquisa](../log-query/log-query-overview.md) no Log Analytics. Pode aplicar estes dados em cenários que incluem planeamento de migração, análise de capacidade, descoberta e resolução de problemas de desempenho a pedido.

Um registo é gerado por hora para cada computador e processo único, além dos registos que são gerados quando um processo ou computador começa ou está a bordo do Mapa de Serviço. Estes registos têm as propriedades nas seguintes tabelas. Os campos e valores no mapa de eventos ServiceMapComputer_CL para os campos do recurso Máquina na API do ServiceMap Azure Resource Manager. Os campos e valores no mapa de eventos ServiceMapProcess_CL para os campos do recurso processuais na API do ServiceMap Azure Resource Manager. O campo ResourceName_s corresponde ao campo de nomes no recurso correspondente ao Gestor de Recursos. 

>[!NOTE]
>À medida que as funcionalidades do Mapa de Serviço crescem, estes campos estão sujeitos a alterações.

Existem propriedades geradas internamente que pode usar para identificar processos e computadores únicos:

- Computador: Utilize *o ResourceId* ou *ResourceName_s* identificar um computador de forma única dentro de um espaço de trabalho do Log Analytics.
- Processo: Use *o ResourceId* para identificar de forma única um processo dentro de um espaço de trabalho Log Analytics. *ResourceName_s* é único no contexto da máquina em que o processo está em curso (MachineResourceName_s) 

Como podem existir vários registos para um processo especificado e computador num intervalo de tempo especificado, as consultas podem devolver mais do que um registo para o mesmo computador ou processo. Para incluir apenas o registo mais recente, adicione "/ dedup ResourceId" para a consulta.

### <a name="connections"></a>Ligações

As métricas de ligação são escritas para uma nova tabela em Log Analytics - VMConnection. Esta tabela fornece informações sobre as ligações para uma máquina (entrada e saída). As métricas de ligação também são expostas com APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo.  As ligações TCP resultantes da aceitação numa tomada de audição são de entrada, enquanto as criadas através da ligação a um determinado IP e à porta são de saída. A direção de uma ligação é representada pela propriedade Direction, que pode ser definida para **entrada** ou **saída**. 

Os registos nestas tabelas são gerados a partir de dados reportados pelo agente Dependency. Cada registo representa uma observação durante um minuto de intervalo. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registo contém informações para identificar a respetiva entidade, isto é, ligação ou porta, bem como métricas associadas a essa entidade. Atualmente, apenas é reportada a atividade da rede que ocorre utilizando tCP sobre iPv4.

Para gerir custos e complexidade, os registos de ligação não representam ligações individuais de rede física. Múltiplas ligações de rede física são agrupadas numa ligação lógica, que é então refletida na respetiva tabela.  Ou seja, os registos na tabela *VMConnection* representam um agrupamento lógico e não as ligações físicas individuais que estão a ser observadas. A ligação física da rede partilhando o mesmo valor para os seguintes atributos durante um determinado intervalo de um minuto, são agregadas num único registo lógico em *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
| `Direction` |Direção da ligação, valor é *entrada* ou *saída* |
| `Machine` |O computador FQDN |
| `Process` |Identidade do processo ou grupos de processos, iniciando/aceitando a ligação |
| `SourceIp` |Endereço IP da fonte |
| `DestinationIp` |Endereço IP do destino |
| `DestinationPort` |Número do porto do destino |
| `Protocol` |Protocolo usado para a ligação.  Valores é *tcp*. |

Para ter em conta o impacto do agrupamento, as informações sobre o número de ligações físicas agrupadas são fornecidas nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
| `LinksEstablished` |O número de ligações de rede física que foram estabelecidas durante a janela de tempo de reporte |
| `LinksTerminated` |O número de ligações de rede física que foram encerradas durante a janela de tempo de reporte |
| `LinksFailed` |O número de ligações de rede física que falharam durante a janela de tempo de reporte. Atualmente, esta informação está disponível apenas para ligações de saída. |
| `LinksLive` |O número de ligações de rede física que foram abertas no final da janela de tempo de reporte|

#### <a name="metrics"></a>Métricas

Para além das métricas de contagem de ligação, as informações sobre o volume de dados enviados e recebidos numa determinada ligação lógica ou porta de rede também estão incluídas nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
| `BytesSent` |Número total de bytes enviados durante a janela de tempo de reporte |
| `BytesReceived` |Número total de bytes que foram recebidos durante a janela de tempo de reporte |
| `Responses` |O número de respostas observadas durante a janela de tempo de reporte. 
| `ResponseTimeMax` |O maior tempo de resposta (milissegundos) observado durante a janela de tempo de reporte.  Se não tiver valor, a propriedade está em branco.|
| `ResponseTimeMin` |O menor tempo de resposta (milissegundos) observado durante a janela de tempo de reporte.  Se não tiver valor, a propriedade está em branco.|
| `ResponseTimeSum` |A soma de todos os tempos de resposta (milissegundos) observados durante a janela de tempo de reporte.  Se não tiver valor, a propriedade está em branco|

O terceiro tipo de dados que estão a ser reportados é o tempo de resposta - quanto tempo é que um chamador gasta à espera de um pedido enviado através de uma ligação a ser processado e respondido pelo ponto final remoto. O tempo de resposta reportado é uma estimativa do verdadeiro tempo de resposta do protocolo de aplicação subjacente. É calculado utilizando heurística com base na observação do fluxo de dados entre a fonte e a extremidade de destino de uma ligação de rede física. Conceptualmente, é a diferença entre o último byte de um pedido deixa o remetente, e o momento em que o último byte da resposta volta a ele. Estes dois picos de tempo são usados para delinear eventos de pedido e resposta numa determinada ligação física. A diferença entre eles representa o tempo de resposta de um único pedido. 

Nesta primeira versão desta funcionalidade, o nosso algoritmo é uma aproximação que pode funcionar com um grau de sucesso variado, dependendo do protocolo de aplicação real usado para uma determinada ligação de rede. Por exemplo, a abordagem atual funciona bem para protocolos baseados em resposta a pedidos, como HTTP(S), mas não funciona com protocolos unidirecionais ou baseados em filas de mensagens.

Eis alguns pontos importantes a ter em conta:

1. Se um processo aceitar ligações no mesmo endereço IP, mas em várias interfaces de rede, será reportado um registo separado para cada interface. 
2. Os registos com o WILDCARD IP não contêm qualquer atividade. Estão incluídos para representar o facto de que um porto da máquina está aberto ao tráfego de entrada.
3. Para reduzir a verbosidade e o volume de dados, os registos com o WILDCARD IP serão omitidos quando houver um registo correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registo IP wildcard é omitido, a propriedade de gravação IsWildcardBind com o endereço IP específico, será definida como "True" para indicar que a porta está exposta em todas as interfaces da máquina de reporte.
4. As portas que estão ligadas apenas numa interface específica têm o IsWildcardBind definido como "Falso".

#### <a name="naming-and-classification"></a>Nomeação e Classificação

Por conveniência, o endereço IP da extremidade remota de uma ligação está incluído na propriedade RemoteIp. Para ligações de entrada, o RemoteIp é o mesmo que o SourceIp, enquanto para as ligações de saída, é o mesmo que o DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes canónicos DNS relatados pela máquina para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification estão reservadas para uso futuro. 

#### <a name="geolocation"></a>Geolocalização

*A VMConnection* também inclui informações de geolocalização para a extremidade remota de cada registo de ligação nas seguintes propriedades do registo: 

| Propriedade | Descrição |
|:--|:--|
| `RemoteCountry` |O nome do país/região que acolhe o RemoteIp.  Por exemplo, *Estados Unidos* |
| `RemoteLatitude` |A latitude de geolocalização.  Por exemplo, *47.68* |
| `RemoteLongitude` |A longitude da geolocalização.  Por exemplo, *-122.12* |

#### <a name="malicious-ip"></a>IP malicioso

Todas as propriedades RemoteIp na tabela *VMConnection* são verificadas contra um conjunto de IPs com atividade maliciosa conhecida. Se o RemoteIp for identificado como malicioso, as seguintes propriedades serão povoadas (estão vazias, quando o IP não for considerado malicioso) nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
| `MaliciousIp` |O endereço RemoteIp |
| `IndicatorThreadType` |O indicador de ameaça detetado é um dos seguintes valores, *Botnet*, *C2*, *CryptoMining,* *Darknet*, *DDos,* *MaliciousUrl,* *Malware*, *Phishing,* *Proxy,* *PUA,* *Watchlist*.   |
| `Description` |Descrição da ameaça observada. |
| `TLPLevel` |O nível do Protocolo de Semáforos (TLP) é um dos valores definidos, *Branco,* *Verde,* *Âmbar,* *Vermelho.* |
| `Confidence` |Os valores são *0 a 100.* |
| `Severity` |Os valores são *0 - 5,* onde *5* é o mais grave e *0* não é nada grave. O valor predefinido é *de 3*.  |
| `FirstReportedDateTime` |A primeira vez que o fornecedor reportou o indicador. |
| `LastReportedDateTime` |A última vez que o indicador foi visto pela Interflow. |
| `IsActive` |Indica que os indicadores são desativados com valor *verdadeiro* ou *falso.* |
| `ReportReferenceLink` |Ligações a relatórios relacionados com um dado observável. |
| `AdditionalInformation` |Fornece informações adicionais, se aplicável, sobre a ameaça observada. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL recordes

Os registos com um tipo de *ServiceMapComputer_CL* têm dados de inventário para servidores com agentes do Mapa de Serviço. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | O identificador único para uma máquina dentro do espaço de trabalho |
| `ResourceName_s` | O identificador único para uma máquina dentro do espaço de trabalho |
| `ComputerName_s` | O computador FQDN |
| `Ipv4Addresses_s` | Uma lista dos endereços IPv4 do servidor |
| `Ipv6Addresses_s` | Uma lista dos endereços IPv6 do servidor |
| `DnsNames_s` | Uma variedade de nomes DNS |
| `OperatingSystemFamily_s` | Janelas ou Linux |
| `OperatingSystemFullName_s` | O nome completo do sistema operativo  |
| `Bitness_s` | A mordição da máquina (32-bit ou 64-bit)  |
| `PhysicalMemory_d` | A memória física em MB |
| `Cpus_d` | O número de CPUs |
| `CpuSpeed_d` | A velocidade do CPU em MHz|
| `VirtualizationState_s` | *desconhecido,* *físico,* *virtual,* *hipervisor* |
| `VirtualMachineType_s` | *hyperv*, *vmware*, e assim por diante |
| `VirtualMachineNativeMachineId_g` | O VM ID atribuído pelo seu hipervisor |
| `VirtualMachineName_s` | O nome do VM |
| `BootTime_t` | A hora do arranque |

### <a name="servicemapprocess_cl-type-records"></a>registos do tipo ServiceMapProcess_CL

Os registos com um tipo de *ServiceMapProcess_CL* têm dados de inventário para processos ligados à TCP em servidores com agentes do Mapa de Serviço. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | O identificador único para um processo dentro do espaço de trabalho |
| `ResourceName_s` | O identificador único para um processo dentro da máquina em que está em execução|
| `MachineResourceName_s` | O nome de recurso da máquina |
| `ExecutableName_s` | O nome do processo executável |
| `StartTime_t` | A hora de início da piscina do processo |
| `FirstPid_d` | O primeiro PID na piscina de processos |
| `Description_s` | A descrição do processo |
| `CompanyName_s` | O nome da empresa |
| `InternalName_s` | O nome interno |
| `ProductName_s` | O nome do produto |
| `ProductVersion_s` | A versão do produto |
| `FileVersion_s` | A versão do ficheiro |
| `CommandLine_s` | A linha de comando |
| `ExecutablePath _s` | O caminho para o arquivo executável |
| `WorkingDirectory_s` | O diretório de trabalho |
| `UserName` | A conta em que o processo está a executar |
| `UserDomain` | O domínio sob o qual o processo está a executar |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

### <a name="list-all-known-machines"></a>Listar todas as máquinas conhecidas

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Listar a capacidade de memória física de todos os computadores geridos.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>Lista nome de computador, DNS, IP e OS.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Encontre todos os processos com "sql" na linha de comando

`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Encontre uma máquina (registo mais recente) pelo nome de recurso

`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Encontre uma máquina (registo mais recente) por endereço IP

`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listar todos os processos conhecidos numa máquina especificada

`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores que executam o SQL

`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões únicas do produto do curl no meu datacenter

`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computador de todos os computadores que executam o CentOS

`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Resumir as ligações de saída de um grupo de máquinas

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>API REST

Todos os dados do servidor, processo e dependência no Mapa de Serviços estão disponíveis através do Mapa de [Serviço REST API](/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço 'Mapa de Serviço'. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço Demômia de Serviço. Para fornecer capacidades precisas e eficientes de resolução de problemas, os dados incluem informações sobre a configuração do seu software, tais como sistema operativo e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não recolhe nomes, endereços ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a [Declaração de Privacidade dos Serviços Online da Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [pesquisas de registo](../log-query/log-query-overview.md) no Log Analytics para recuperar dados recolhidos pelo Mapa de Serviços.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver algum problema em instalar ou executar o Mapa de Serviços, esta secção pode ajudá-lo. Se continuar a não conseguir resolver o problema, contacte o Suporte da Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalação de agentes de dependência

#### <a name="installer-prompts-for-a-reboot"></a>O instalador pede um reinício
O agente de dependência *geralmente* não necessita de um reinício após a instalação ou remoção. No entanto, em certos casos raros, o Windows Server necessita de um reboot para continuar com uma instalação. Isto acontece quando uma dependência, normalmente a biblioteca redistribuível Microsoft Visual C++ requer um reboot por causa de um ficheiro bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>É apresentada a mensagem “Não é possível instalar o Dependency Agent: falha na instalação das bibliotecas de runtime do Visual Studio (código = [code_number])”

O Microsoft Dependency Agent é criado nas bibliotecas de runtime do Microsoft Visual Studio. Receberá uma mensagem se ocorrer um problema durante a instalação das bibliotecas. 

Os instaladores da biblioteca de runtime criam registos na pasta %LOCALAPPDATA%\temp. O ficheiro é `dd_vcredist_arch_yyyymmddhhmmss.log` , onde o *arco* está ou `x86` e `amd64` *yyymmdhhmmss* é a data e a hora (relógio 24 horas) quando o registo foi criado. O registo fornece detalhes sobre o problema que está a bloquear a instalação.

Pode ser útil instalar primeiro as [bibliotecas de runtime mais recentes](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

A tabela que se segue lista os números de código e as resoluções sugeridas.

| Código | Descrição | Resolução |
|:--|:--|:--|
| 0x17 | O instalador da biblioteca requer uma atualização do Windows que não foi instalada. | Veja o registo de instalação da biblioteca mais recente.<br><br>Se uma referência `Windows8.1-KB2999226-x64.msu` for seguida por uma `Error 0x80240017: Failed to execute MSU package,` linha, não tem os pré-requisitos para instalar o KB2999226. Siga as instruções na secção de pré-requisitos no artigo [Universal C Runtime no Windows](https://support.microsoft.com/kb/2999226). Pode ter de executar o Windows Update e reiniciar várias vezes para instalar os pré-requisitos.<br><br>Volte a executar o instalador do Microsoft Dependency Agent. |

### <a name="post-installation-issues"></a>Problemas de pós-instalação

#### <a name="server-doesnt-appear-in-service-map"></a>Servidor não aparece no Mapa de Serviço

Se a instalação do seu agente de dependência tiver sido bem sucedida, mas não vê a sua máquina na solução 'Mapa de Serviço':
* O Dependency Agent foi instalado com sucesso? Pode verificar esta situação ao confirmar que o serviço está instalado e em execução.<br><br>
**Windows**: Procure o serviço chamado **Microsoft Dependency agent**.
**Linux**: Procure o processo de execução **microsoft-dependency-agent**.

* Está no [nível livre](https://azure.microsoft.com/pricing/details/monitor/)do Log Analytics? O plano Gratuito permite até cinco máquinas virtuais do Mapa de Serviços exclusivas. Quaisquer máquinas subsequentes não aparecerão no Mapa de Serviços, mesmo que as cinco anteriores já não estejam a enviar dados.

* O seu servidor está a enviar registos e dados perf para registos do Monitor Azure? Aceda ao Azure Monitor\Registos e execute a seguinte consulta no computador: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Conseguiu uma variedade de eventos nos resultados? Os dados são recentes? Em caso afirmativo, o agente do Log Analytics está a funcionar corretamente e a comunicar com a área de trabalho. Caso contrário, verifique o agente no computador: [Resolução de problemas do agente do Log Analytics para Windows](../platform/agent-windows-troubleshoot.md) ou [Resolução de problemas do agente do Log Analytics para Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servidor aparece no Mapa de Serviços mas não tem processos

Se vir a sua máquina no Mapa de Serviço, mas não tiver dados de processo ou ligação, isso indica que o agente Dependency está instalado e a funcionar, mas o controlador de kernel não carregou. 

Verifique o `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) ou `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux). As últimas linhas do ficheiro devem indicar o motivo pelo qual kernel não carregou. Por exemplo, o kernel poderá não ser suportado pelo Linux se tiver atualizado o kernel.

## <a name="suggestions"></a>Sugestões

Tem algum feedback sobre o Mapa de Serviços ou esta documentação?  Visite a nossa [página De Voz do Utilizador,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)onde pode sugerir funcionalidades ou votar as sugestões existentes.