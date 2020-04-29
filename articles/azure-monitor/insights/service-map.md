---
title: Utilização da solução de Mapa de Serviço solução em Azure [ Microsoft Docs
description: O Mapa de Serviços é uma solução no Azure que deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes para a implementação do Mapa de Serviços no seu ambiente e usá-lo em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: f2f3e84462307f43ffe432fe878476d979f489f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480917"
---
# <a name="using-service-map-solution-in-azure"></a>Utilizar a solução Mapa de Serviços no Azure

O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Com o Mapa de Serviços, pode ver os seus servidores da mesma forma como os conceptualiza: como sistemas interligados que fornecem serviços críticos. O Mapa de Serviços mostra as ligações entre os servidores, os processos, a latência da ligação de entrada e de saída e as portas em qualquer arquitetura ligada por TCP, em que a única configuração necessária é a instalação de um agente.

Este artigo descreve os detalhes do embarque e utilização do Mapa de Serviço. Para obter informações sobre a configuração dos pré-requisitos para esta solução, consulte [Ativar o Monitor Azure para vMs .](vminsights-enable-overview.md#prerequisites) Resumindo, é preciso o seguinte:

* Um espaço de trabalho log Analytics para ativar esta solução.

* O agente Log Analytics instalado no computador Windows ou no servidor Linux configurado para reportar o mesmo espaço de trabalho com o qual ativou a solução.

* O agente Dependency instalado no computador Windows ou no servidor Linux.

>[!NOTE]
>Se já implementou o Mapa de Serviços, pode agora também ver os seus mapas no Monitor Azure para VMs, que inclui funcionalidades adicionais para monitorizar a saúde e desempenho da VM. Para saber mais, consulte [o Monitor Azure para visualização geral dos VMs.](../../azure-monitor/insights/vminsights-overview.md) Para saber mais sobre as diferenças entre a solução do Mapa de Serviços e a funcionalidade Do Mapa do Monitor de VMs, consulte as seguintes [FAQ](../faq.md#azure-monitor-for-vms).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Ativar o mapa de serviço

1. Ative a solução Service Map do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) ou utilizando o processo descrito em [Adicionar soluções de monitorização a partir da Galeria soluções.](solutions.md)
1. [Instale o agente Dependency no Windows](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) ou [instale o agente dependency no Linux](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) em cada computador onde pretende obter dados. O Agente de Dependência pode monitorizar as ligações dos vizinhos imediatos, pelo que poderá não precisar de um agente em cada computador.

Aceda ao Mapa de Serviços no portal Azure a partir do seu espaço de trabalho Log Analytics e selecione as **soluções** de opção a partir do painel esquerdo.<br><br> ![Selecione a opção Soluções no espaço de](./media/service-map/select-solution-from-workspace.png)trabalho.<br> A partir da lista de soluções, selecione **ServiceMap (workspaceName)** e na página de visão geral da solução do Mapa de Serviço clique no resumo do mapa de serviço.<br><br> ![Azulejo resumo do mapa](./media/service-map/service-map-summary-tile.png)de serviço.

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Utilizar casos: Sensibilizar a dependência dos seus processos de TI

### <a name="discovery"></a>Deteção

O Service Map constrói automaticamente um mapa de referência comum de dependências em todos os seus servidores, processos e serviços de terceiros. Descobre e mapeia todas as dependências de TCP, identificando ligações surpresa, sistemas remotos de terceiros de que depende, e dependências de áreas escuras tradicionais da sua rede, como o Ative Directory. O Service Map descobre ligações de rede falhadas que os seus sistemas geridos estão a tentar fazer, ajudando-o a identificar potenciais problemas de configuração do servidor, falha de serviço e problemas de rede.

### <a name="incident-management"></a>Gestão de Incidentes

O Service Map ajuda a eliminar a adivinhação do isolamento de problemas, mostrando-lhe como os sistemas estão conectados e afetando uns aos outros. Além de identificar ligações falhadas, ajuda a identificar equilibradores de carga mal configurados, carga surpreendente ou excessiva em serviços críticos, e clientes fraudulentos, como máquinas de desenvolvimento que falam com sistemas de produção. Ao utilizar fluxos de trabalho integrados com O Rastreio de Mudanças, também pode ver se um evento de mudança numa máquina ou serviço de back-end explica a causa principal de um incidente.

### <a name="migration-assurance"></a>Garantia de migração

Ao utilizar o Mapa de Serviços, pode planear, acelerar e validar as migrações azure, o que ajuda a garantir que nada é deixado para trás e que não ocorrem interrupções surpresa. Pode descobrir todos os sistemas interdependentes que precisam de migrar em conjunto, avaliar a configuração e capacidade do sistema e identificar se um sistema de funcionamento ainda serve os utilizadores ou se é candidato ao desmantelamento em vez de migração. Após a mudança estar concluída, pode verificar a carga e identidade do cliente para verificar se os sistemas de teste e os clientes estão a ligar-se. Se as definições de planeamento de sub-redes e de firewall tiverem problemas, as ligações falhadas nos mapas do Mapa de Serviçoapontam-no para os sistemas que precisam de conectividade.

### <a name="business-continuity"></a>Continuidade do negócio

Se estiver a utilizar a Recuperação do Site Azure e precisar de ajuda para definir a sequência de recuperação para o ambiente de aplicação, o Service Map pode mostrar-lhe automaticamente como os sistemas dependem uns dos outros para garantir que o seu plano de recuperação é fiável. Ao escolher um servidor ou grupo crítico e visualizar os seus clientes, pode identificar quais os sistemas frontais a recuperar após o servidor ser restaurado e disponível. Inversamente, olhando para as dependências traseiras dos servidores críticos, pode identificar quais os sistemas a recuperar antes de os seus sistemas de foco serem restaurados.

### <a name="patch-management"></a>Gestão de patches

O Mapa de Serviço saca a sua utilização da Avaliação de Atualização do Sistema, mostrando-lhe quais as outras equipas e servidores que dependem do seu serviço, para que possa notificá-los com antecedência antes de retirar os seus sistemas para remendar. O Service Map também melhora a gestão de patchs mostrando-lhe se os seus serviços estão disponíveis e devidamente conectados depois de remendados e reiniciados.

## <a name="mapping-overview"></a>Visão geral do mapeamento

Os agentes do Mapa de Serviço recolhem informações sobre todos os processos ligados ao TCP no servidor onde estão instalados e detalhes sobre as ligações de entrada e saída para cada processo.

A partir da lista no painel esquerdo, pode selecionar máquinas ou grupos que tenham agentes do Mapa de Serviço para visualizar as suas dependências ao longo de um intervalo de tempo especificado. Os mapas de dependência da máquina concentram-se numa máquina específica, e mostram todas as máquinas que são clientes diretos de TCP ou servidores dessa máquina.  Os mapas do Grupo Máquina mostram conjuntos de servidores e suas dependências.

![Visão geral do mapa de serviço](media/service-map/service-map-overview.png)

As máquinas podem ser expandidas no mapa para mostrar os grupos e processos de processo de execução com ligações de rede ativas durante o intervalo de tempo selecionado. Quando uma máquina remota com um agente do Mapa de Serviço é expandida para mostrar detalhes do processo, apenas são mostrados os processos que comunicam com a máquina de foco. A contagem de máquinas frontais sem agente que se ligam à máquina de focagem é indicada no lado esquerdo dos processos a que se ligam. Se a máquina de foco estiver a fazer uma ligação a uma máquina de back-end que não tem agente, o servidor de back-end está incluído num Grupo de Porta de Servidor, juntamente com outras ligações com o mesmo número de porta.

Por padrão, os mapas do Mapa de Serviço mostram os últimos 30 minutos de informação sobre dependência. Ao utilizar os controlos de tempo na parte superior esquerda, pode consultar mapas para intervalos históricos de até uma hora para mostrar como as dependências pareciam no passado (por exemplo, durante um incidente ou antes de ocorrer uma mudança). Os dados do Mapa de Serviço saem armazenados durante 30 dias em espaços de trabalho pagos e durante 7 dias em espaços de trabalho gratuitos.

## <a name="status-badges-and-border-coloring"></a>Crachás de estado e coloração na fronteira

Na parte inferior de cada servidor no mapa pode estar uma lista de crachás de estado que transmitem informações de estado sobre o servidor. Os crachás indicam que há algumas informações relevantes para o servidor a partir de uma das integrações de soluções. Clicar num crachá leva-o diretamente aos detalhes do estado no painel certo. Os crachás de estado atualmente disponíveis incluem Alertas, Balcão de Serviço, Alterações, Segurança e Atualizações.

Dependendo da gravidade das insígnias de estado, as bordas do nó da máquina podem ser coloridas de vermelho (crítico), amarelo (aviso) ou azul (informativo). A cor representa o estado mais grave de qualquer um dos crachás de estado. Uma fronteira cinzenta indica um nó que não tem indicadores de estado.

![Crachás de estado](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupos de Processo

Os Grupos de Processo combinam processos que estão associados a um produto ou serviço comum num grupo de processos.  Quando um nó de máquina é expandido, apresentará processos autónomos juntamente com grupos de processos.  Se alguma ligação de entrada e saída a um processo dentro de um grupo de processo sintetizou, então a ligação é mostrada como falhada para todo o grupo de processos.

## <a name="machine-groups"></a>Grupos de Máquinas

Os Grupos de Máquinas permitem-lhe ver mapas centrados em torno de um conjunto de servidores, e não apenas um para que possa ver todos os membros de uma aplicação de vários níveis ou cluster de servidor num mapa.

Os utilizadores selecionam quais servidores pertencem a um grupo e escolhem um nome para o grupo.  Pode então optar por ver o grupo com todos os seus processos e ligações, ou vê-lo apenas com os processos e ligações que se relacionam diretamente com os outros membros do grupo.

![Grupo máquina](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Criação de um Grupo máquina

Para criar um grupo, selecione a máquina ou as máquinas que deseja na lista de Máquinas e clique em **Adicionar ao grupo**.

![Criar Grupo](media/service-map/machine-groups-create.png)

Lá, pode escolher **Criar novo** e dar um nome ao grupo.

![Grupo de Nomes](media/service-map/machine-groups-name.png)

>[!NOTE]
>Os grupos de máquinas estão limitados a 10 servidores.

### <a name="viewing-a-group"></a>Visualização de um grupo

Depois de criar alguns grupos, pode vê-los escolhendo o separador Grupos.

![Separador de grupos](media/service-map/machine-groups-tab.png)

Em seguida, selecione o nome de grupo para visualizar o mapa para esse Grupo máquina.
![Grupo](media/service-map/machine-group.png) máquina As máquinas que pertencem ao grupo estão delineadas em branco no mapa.

A expansão do Grupo listará as máquinas que compõem o Grupo máquina.

![Máquinas do Grupo máquina](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrar por processos

Pode alternar a vista do mapa entre mostrar todos os processos e ligações do Grupo e apenas os que se relacionam diretamente com o Grupo máquina.  A visão padrão é mostrar todos os processos.  Pode alterar a vista clicando no ícone do filtro acima do mapa.

![Grupo de Filtros](media/service-map/machine-groups-filter.png)

Quando todos os processos forem **selecionados,** o mapa incluirá todos os processos e ligações em cada uma das máquinas do Grupo.

![Grupo máquina todos os processos](media/service-map/machine-groups-all.png)

Se alterar a visão de mostrar apenas **processos ligados**ao grupo, o mapa será reduzido apenas aos processos e ligações que estão diretamente ligados a outras máquinas do grupo, criando uma visão simplificada.

![Processos filtrados do Grupo Máquina](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Adicionar máquinas a um grupo

Para adicionar máquinas a um grupo existente, verifique as caixas ao lado das máquinas que deseja e, em seguida, clique em **Adicionar ao grupo**.  Em seguida, escolha o grupo a que pretende adicionar as máquinas.
 
### <a name="removing-machines-from-a-group"></a>Remoção de máquinas de um grupo

Na Lista de Grupos, expanda o nome do grupo para listar as máquinas no Grupo máquina.  Em seguida, clique no menu de elipse ao lado da máquina que pretende remover e escolher **Remover**.

![Remover a máquina do grupo](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Remoção ou renomeação de um grupo

Clique no menu de elipse ao lado do nome de grupo na Lista de Grupos.

![Menu de grupo de máquinas](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ícones de papel

Certos processos servem papéis particulares em máquinas: servidores web, servidores de aplicações, base de dados, e assim por diante. O Mapa de Serviço anota caixas de processo e máquinas com ícones de funções para ajudar a identificar num ápice a função que um processo ou servidor desempenha.

| Ícone de papel | Descrição |
|:--|:--|
| ![Servidor Web](media/service-map/role-web-server.png) | Servidor Web |
| ![Servidor de aplicações](media/service-map/role-application-server.png) | Servidor de aplicações |
| ![Servidor de base de dados](media/service-map/role-database.png) | Servidor de base de dados |
| ![Servidor LDAP](media/service-map/role-ldap.png) | Servidor LDAP |
| ![Servidor SMB](media/service-map/role-smb.png) | Servidor SMB |

![Ícones de papel](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Ligações falhadas

As ligações falhadas são mostradas nos mapas do Mapa de Serviço para processos e computadores, com uma linha vermelha tracejada indicando que um sistema de cliente está a falhar em alcançar um processo ou porta. As ligações falhadas são reportadas a partir de qualquer sistema com um agente de mapas de serviço implantado se esse sistema for o que tenta a ligação falhada. O Mapa de Serviço mede este processo observando tomadas TCP que não estabelecem uma ligação. Esta falha pode resultar de uma firewall, de uma configuração errada no cliente ou servidor, ou de um serviço remoto indisponível.

![Ligações falhadas](media/service-map/failed-connections.png)

Compreender ligações falhadas pode ajudar na resolução de problemas, validação da migração, análise de segurança e compreensão arquitetónica geral. As ligações falhadas são por vezes inofensivas, mas muitas vezes apontam diretamente para um problema, como um ambiente de falha súbita, tornando-se subitamente inacessível, ou dois níveis de aplicação incapazes de falar após uma migração na nuvem.

## <a name="client-groups"></a>Grupos de Clientes

Grupos de Clientes são caixas no mapa que representam máquinas de clientes que não têm Agentes de Dependência. Um único Grupo cliente representa os clientes para um processo ou máquina individual.

![Grupos de Clientes](media/service-map/client-groups.png)

Para ver os endereços IP dos servidores num Grupo cliente, selecione o grupo. Os conteúdos do grupo estão listados no painel **Client Group Properties.**

![Propriedades do Grupo Cliente](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de porta de servidor

Os Grupos de Portas do Servidor são caixas que representam portas de servidores em servidores que não têm Agentes de Dependência. A caixa contém a porta do servidor e uma contagem do número de servidores com ligações a essa porta. Expanda a caixa para ver os servidores e ligações individuais. Se houver apenas um servidor na caixa, o nome ou endereço IP está listado.

![Grupos de porta de servidor](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu Contexto

Clicar na elipse (...) na direita superior de qualquer servidor exibe o menu de contexto desse servidor.

![Ligações falhadas](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Mapa do servidor de carga

Clicar no Mapa do Servidor de **Carga** leva-o a um novo mapa com o servidor selecionado como a nova máquina de foco.

### <a name="show-self-links"></a>Mostrar auto-ligações

Clicar em **Mostrar Auto-Links** rebaseia o nó do servidor, incluindo quaisquer auto-links, que são ligações TCP que começam e terminam em processos dentro do servidor. Se forem mostradas autoligações, o comando do menu muda para **Ocultar Auto-Ligações,** para que possa desligá-los.

## <a name="computer-summary"></a>Resumo do computador

O painel **de resumo** da máquina inclui uma visão geral do sistema operativo de um servidor, contagens de dependência e dados de outras soluções. Estes dados incluem métricas de desempenho, bilhetes de secretária de serviço, rastreio de alterações, segurança e atualizações.

![Painel de resumo da máquina](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriedades de computador e processo

Ao navegar num mapa de mapas de serviço, pode selecionar máquinas e processos para ganhar contexto adicional sobre as suas propriedades. As máquinas fornecem informações sobre o nome DNS, endereços IPv4, CPU e capacidade de memória, tipo VM, sistema operativo e versão, última hora de reinício, e os IDs dos seus agentes OMS e Service Map.

![Painel de propriedades da máquina](media/service-map/machine-properties.png)

Pode recolher detalhes do processo a partir de metadados do sistema operativo sobre processos de execução, incluindo nome de processo, descrição do processo, nome e domínio do utilizador (no Windows), nome da empresa, nome do produto, versão do produto, diretório de trabalho, linha de comando e tempo de início do processo.

![Painel de propriedades de processo](media/service-map/process-properties.png)

O painel resumo do **processo** fornece informações adicionais sobre a conectividade do processo, incluindo as portas ligadas, ligações de entrada e saída, e ligações falhadas.

![Painel de resumo do processo](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Alertas de integração

O Mapa de Serviço integra-se com alertas Azure para mostrar alertas disparados para o servidor selecionado na faixa hortemsa selecionada. O servidor apresenta um ícone se houver alertas atuais, e o painel de alertas de **máquinas** lista os alertas.

![Painel de alertas de máquina](media/service-map/machine-alerts.png)

Para permitir que o Mapa de Serviço saque alertas relevantes, crie uma regra de alerta que dispara para um computador específico. Para criar alertas adequados:
- Incluir uma cláusula para agrupar por computador (por exemplo, **por intervalo de computador 1 minuto).**
- Opte por alertar com base na medição métrica.

## <a name="log-events-integration"></a>Integração de eventos de log

O Mapa de Serviço integra-se com a Pesquisa de Registos para mostrar uma contagem de todos os eventos de registo disponíveis para o servidor selecionado durante o intervalo de tempo selecionado. Pode clicar em qualquer linha na lista de contagens de eventos para saltar para Registar Pesquisa e ver os eventos de registo individuais.

![Painel de eventos de log de máquina](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integração do Balcão de Serviço

A integração do Mapa de Serviços com o Conector de Gestão de Serviços de TI é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics. A integração no Mapa de Serviços está rotulada como "Balcão de Serviço". Para mais informações, consulte centralmente gerir itens de [trabalho ITSM utilizando o Conector de Gestão de Serviços de TI](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

O painel **do Balcão de Serviço** da Máquina lista todos os eventos de Gestão de Serviços de TI para o servidor selecionado na faixa horda selecionada. O servidor apresenta um ícone se houver itens atuais e o painel do Balcão de Serviço de Máquinas os lista.

![Painel de mesa de serviço de máquina](media/service-map/service-desk.png)

Para abrir o item na sua solução ITSM conectada, clique no **View Work Item**.

Para ver os detalhes do item em 'Pesquisa de Registo', clique **em Mostrar em Pesquisa de Registo**.
As métricas de ligação são escritas para duas novas tabelas no Log Analytics 

## <a name="change-tracking-integration"></a>Alteração de Rastreio de Integração

A integração do Mapa de Serviço com o Change Tracking é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics.

O painel de rastreio de **alterações** automáticas lista todas as alterações, com a primeira mais recente, juntamente com um link para perfurar para obter detalhes adicionais.

![Painel de rastreio de alterações de máquina](media/service-map/change-tracking.png)

A imagem que se segue é uma visão detalhada de um evento De ConfiguraçãoChange que poderá ver depois de selecionar **Mostrar em Log Analytics**.

![Evento ConfiguraçãoChange](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integração de desempenho

O painel **de desempenho** da máquina apresenta métricas de desempenho padrão para o servidor selecionado. As métricas incluem a utilização do CPU, a utilização da memória, os bytes de rede enviados e recebidos, e uma lista dos principais processos por bytes de rede enviados e recebidos.

![Painel de desempenho da máquina](media/service-map/machine-performance.png)

Para ver os dados de desempenho, poderá ser necessário [ativar os contadores](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)de desempenho adequados do Log Analytics .  Os contadores que pretende ativar:

Windows:
- Processador (*)\\% Tempo do processador
- Memória\\% Bytes Comprometidos Em Uso
- Adaptador de rede(*)\\Bytes Enviado/seg
- Adaptador de rede(*)\\Bytes Recebidos/seg

Linux:
- Processador (*)\\% Tempo do processador
- Memória(*)\\% Memória Usada
- Adaptador de rede(*)\\Bytes Enviado/seg
- Adaptador de rede(*)\\Bytes Recebidos/seg

Para obter os dados de desempenho da rede, também deve ter ativado a solução Wire Data 2.0 no seu espaço de trabalho.
 
## <a name="security-integration"></a>Integração de segurança

A integração do Mapa de Serviços com segurança e auditoria é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics.

O painel **de Segurança automática** mostra dados da solução de Segurança e Auditoria para o servidor selecionado. O painel lista um resumo de quaisquer problemas de segurança pendentes para o servidor durante o intervalo de tempo selecionado. Clicar em qualquer um dos problemas de segurança faz um exercício para baixo em uma Pesquisa de Registo para obter detalhes sobre eles.

![Painel de segurança da máquina](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integração de atualizações

A integração do Mapa de Serviço com a Atualização é automática quando ambas as soluções estão ativadas e configuradas no seu espaço de trabalho Log Analytics.

O painel de **atualizações** da máquina apresenta dados da solução de Gestão de Atualizações para o servidor selecionado. O painel lista um resumo de quaisquer atualizações em falta para o servidor durante o intervalo de tempo selecionado.

![Painel de rastreio de alterações de máquina](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registos do Log Analytics

Os dados do computador do Mapa de Serviço e do inventário de processos estão disponíveis para [pesquisa](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics. Pode aplicar estes dados a cenários que incluem planeamento migratório, análise de capacidade, descoberta e resolução de problemas de desempenho a pedido.

Um registo é gerado por hora para cada computador e processo exclusivos, além dos registos que são gerados quando um processo ou computador começa ou está a bordo do Mapa de Serviço. Estes registos têm as propriedades nas seguintes tabelas. Os campos e valores do mapa de eventos ServiceMapComputer_CL para campos do recurso Máquina na API serviceMap Azure Resource Manager. Os campos e valores do mapa de eventos ServiceMapProcess_CL para os campos do recurso Process na API serviceMap Azure Resource Manager. O campo ResourceName_s corresponde ao campo de nomes no recurso correspondente do Gestor de Recursos. 

>[!NOTE]
>À medida que as funcionalidades do Mapa de Serviço crescem, estes campos estão sujeitos a alterações.

Existem propriedades geradas internamente que pode usar para identificar processos e computadores únicos:

- Computador: Utilize o *ResourceId* ou *ResourceName_s* para identificar de forma única um computador dentro de um espaço de trabalho de Log Analytics.
- Processo: Utilize o *ResourceId* para identificar de forma única um processo dentro de um espaço de trabalho de Log Analytics. *ResourceName_s* é único no contexto da máquina em que o processo está a decorrer (MachineResourceName_s) 

Como vários registos podem existir para um processo e computador especificados numa faixa de tempo especificada, as consultas podem devolver mais do que um registo para o mesmo computador ou processo. Para incluir apenas o registo mais recente, adicione "[ dedup ResourceId " para a consulta.

### <a name="connections"></a>Ligações

As métricas de ligação são escritas para uma nova tabela no Log Analytics - VMConnection. Esta tabela fornece informações sobre as ligações para uma máquina (entrada e saída). As Métricas de Ligação também são expostas com APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo.  As ligações TCP resultantes da aceitação numa tomada de audição estão a entrar, enquanto as criadas através da ligação a um determinado IP e à porta estão de saída. A direção de uma ligação é representada pela propriedade Direction, que pode ser definida para **entrada** ou **saída**. 

Os registos nestas tabelas são gerados a partir de dados reportados pelo agente dependency. Cada registo representa uma observação num intervalo de um minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registo contém informações para identificar a respetiva entidade, isto é, conexão ou porta, bem como métricas associadas a essa entidade. Atualmente, apenas é reportada a atividade de rede que ocorre usando TCP sobre iPv4.

Para gerir o custo e a complexidade, os registos de ligação não representam ligações físicas individuais da rede. Várias ligações físicas de rede são agruparadas numa conexão lógica, que é então refletida na respetiva tabela.  Ou seja, os registos na tabela *VMConnection* representam um agrupamento lógico e não as ligações físicas individuais que estão a ser observadas. A ligação física da rede que partilha o mesmo valor para os seguintes atributos durante um determinado intervalo de um minuto, são agregados num único registo lógico em *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
| `Direction` |Direção da ligação, o valor é *de entrada* ou *saída* |
| `Machine` |O computador FQDN |
| `Process` |Identidade de processo ou grupos de processos, início/aceitação da ligação |
| `SourceIp` |Endereço IP da fonte |
| `DestinationIp` |Endereço IP do destino |
| `DestinationPort` |Número do porto do destino |
| `Protocol` |Protocolo usado para a ligação.  Valores é *tcp*. |

Para ter em conta o impacto do agrupamento, são fornecidas informações sobre o número de ligações físicas agrunadas nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
| `LinksEstablished` |O número de ligações físicas de rede que foram estabelecidas durante a janela de tempo de reporte |
| `LinksTerminated` |O número de ligações físicas de rede que foram terminadas durante a janela de tempo de reporte |
| `LinksFailed` |O número de ligações físicas à rede que falharam durante o período de reporte. Esta informação está atualmente disponível apenas para ligações de saída. |
| `LinksLive` |O número de ligações físicas de rede que estavam abertas no final da janela de tempo de reporte|

#### <a name="metrics"></a>Métricas

Para além das métricas de contagem de ligação, estão também incluídas informações sobre o volume de dados enviados e recebidos numa determinada ligação lógica ou porta de rede nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
| `BytesSent` |Número total de bytes que foram enviados durante a janela de reporte |
| `BytesReceived` |Número total de bytes que foram recebidos durante a janela de reporte |
| `Responses` |O número de respostas observadas durante a janela de tempo de reporte. 
| `ResponseTimeMax` |O maior tempo de resposta (milissegundos) observado durante a janela de tempo de reporte.  Se não tiver valor, a propriedade está em branco.|
| `ResponseTimeMin` |O menor tempo de resposta (milissegundos) observado durante a janela de tempo de reporte.  Se não tiver valor, a propriedade está em branco.|
| `ResponseTimeSum` |A soma de todos os tempos de resposta (milissegundos) observados durante a janela de tempo de reporte.  Se não tiver valor, a propriedade está em branco|

O terceiro tipo de dados a ser reportado é o tempo de resposta - quanto tempo um chamador gasta à espera de um pedido enviado sobre uma ligação a ser processado e respondido pelo ponto final remoto. O tempo de resposta reportado é uma estimativa do verdadeiro tempo de resposta do protocolo de candidatura subjacente. É calculado utilizando heurística com base na observação do fluxo de dados entre a origem e o destino de uma ligação de rede física. Conceptualmente, é a diferença entre o último byte de um pedido que deixa o remetente, e o momento em que o último byte da resposta chega ao mesmo. Estes dois selos temporais são usados para delinear eventos de pedido e resposta numa determinada ligação física. A diferença entre eles representa o tempo de resposta de um único pedido. 

Neste primeiro lançamento desta funcionalidade, o nosso algoritmo é uma aproximação que pode funcionar com um grau de sucesso variado dependendo do protocolo de aplicação real utilizado para uma determinada ligação de rede. Por exemplo, a abordagem atual funciona bem para protocolos baseados em resposta a pedidos, como http(S), mas não funciona com protocolos de ida ou de fila de mensagens.

Eis alguns pontos importantes a ter em conta:

1. Se um processo aceitar ligações no mesmo endereço IP, mas em várias interfaces de rede, será reportado um registo separado para cada interface. 
2. Os registos com o WILDCARD IP não conterão nenhuma atividade. Estão incluídos para representar o facto de que uma porta da máquina está aberta ao tráfego de entrada.
3. Para reduzir a verbosidade e o volume de dados, os registos com IP wildcard serão omitidos quando houver um registo correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registo IP wildcard for omitido, a propriedade de registo IsWildcardBind com o endereço IP específico, será definida como "True" para indicar que a porta está exposta em todas as interfaces da máquina de reporte.
4. As portas que estão ligadas apenas a uma interface específica têm o IsWildcardBind definido para "Falso".

#### <a name="naming-and-classification"></a>Nomeação e Classificação

Por conveniência, o endereço IP da extremidade remota de uma ligação está incluído na propriedade RemoteIp. Para ligações de entrada, o RemoteIp é o mesmo que o SourceIp, enquanto para ligações de saída, é o mesmo que o DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes canónicos DNS relatados pela máquina para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification estão reservadas para uso futuro. 

#### <a name="geolocation"></a>Geolocalização

A *VMConnection* também inclui informações de geolocalização para a extremidade remota de cada registo de ligação nas seguintes propriedades do registo: 

| Propriedade | Descrição |
|:--|:--|
| `RemoteCountry` |O nome do país/região que acolhe o RemoteIp.  Por exemplo, *Estados Unidos* |
| `RemoteLatitude` |A latitude geolocalização.  Por exemplo, *47.68* |
| `RemoteLongitude` |A longitude geolocalização.  Por exemplo, *-122.12* |

#### <a name="malicious-ip"></a>IP malicioso

Todas as propriedades RemoteIp na tabela *VMConnection* são verificadas contra um conjunto de IPs com atividade maliciosa conhecida. Se o RemoteIp for identificado como malicioso, as seguintes propriedades serão povoadas (estão vazias, quando o IP não for considerado malicioso) nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
| `MaliciousIp` |O endereço RemoteIp |
| `IndicatorThreadType` |O indicador de ameaça detetado é um dos seguintes valores, *Botnet*, *C2*, *CryptoMining,* *Darknet,* *DDos,* *MaliciousUrl,* *Malware*, *Phishing,* *Proxy,* *PUA,* *Watchlist*.   |
| `Description` |Descrição da ameaça observada. |
| `TLPLevel` |O nível do Protocolo do Semáforo (TLP) é um dos valores definidos, *Branco,* *Verde,* *Âmbar,* *Vermelho*. |
| `Confidence` |Os valores são *0 a 100*. |
| `Severity` |Os valores são *0 - 5*, onde *5* é o mais grave e *0* não é severo de todo. O valor predefinido é *de 3*.  |
| `FirstReportedDateTime` |A primeira vez que o fornecedor reportou o indicador. |
| `LastReportedDateTime` |A última vez que o indicador foi visto pela Interflow. |
| `IsActive` |Indica que os indicadores são desativados com valor *verdadeiro* ou *falso.* |
| `ReportReferenceLink` |Ligações a relatórios relacionados com um dado observável. |
| `AdditionalInformation` |Fornece informações adicionais, se aplicável, sobre a ameaça observada. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL registos

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
| `Bitness_s` | A bitness da máquina (32-bit ou 64-bit)  |
| `PhysicalMemory_d` | A memória física em MB |
| `Cpus_d` | O número de CPUs |
| `CpuSpeed_d` | A velocidade do CPU em MHz|
| `VirtualizationState_s` | *desconhecido,* *físico,* *virtual,* *hipervisor* |
| `VirtualMachineType_s` | *hyperv*, *vmware,* e assim por diante |
| `VirtualMachineNativeMachineId_g` | O ID VM atribuído pelo seu hipervisor |
| `VirtualMachineName_s` | O nome do VM |
| `BootTime_t` | O tempo de arranque |

### <a name="servicemapprocess_cl-type-records"></a>Registos do tipo ServiceMapProcess_CL

Os registos com um tipo de *ServiceMapProcess_CL* têm dados de inventário para processos ligados ao TCP em servidores com agentes do Mapa de Serviço. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | O identificador único para um processo dentro do espaço de trabalho |
| `ResourceName_s` | O identificador único para um processo dentro da máquina em que está em execução|
| `MachineResourceName_s` | O nome do recurso da máquina |
| `ExecutableName_s` | O nome do processo executável |
| `StartTime_t` | A hora de início da piscina de processo |
| `FirstPid_d` | O primeiro PID na piscina de processo |
| `Description_s` | A descrição do processo |
| `CompanyName_s` | O nome da empresa |
| `InternalName_s` | O nome interno |
| `ProductName_s` | O nome do produto |
| `ProductVersion_s` | A versão do produto |
| `FileVersion_s` | A versão de ficheiro |
| `CommandLine_s` | A linha de comando |
| `ExecutablePath _s` | O caminho para o arquivo executável |
| `WorkingDirectory_s` | O diretório de trabalho |
| `UserName` | A conta sob a qual o processo está a executar |
| `UserDomain` | O domínio sob o qual o processo está executando |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

### <a name="list-all-known-machines"></a>Listar todas as máquinas conhecidas

ServiceMapComputer_CL resumir arg_max (TimeGenerated, *) por ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Enumera rita a capacidade física de memória de todos os computadores geridos.

ServiceMapComputer_CL resumir arg_max (TimeGenerated, *) by ResourceId [ projeto PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Lista de nomes de computador, DNS, IP e OS.

ServiceMapComputer_CL resumir arg_max (TimeGenerated, *) by ResourceId [ ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Encontre todos os processos com "sql" na linha de comando

ServiceMapProcess_CL onde CommandLine_s contains_cs "sql" [ sql' [ resumir arg_max (TimeGenerated, *) por ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Encontre uma máquina (registo mais recente) pelo nome de recurso

pesquisa em (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" [ resumir arg_max (TimeGenerated, *) por ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Encontre uma máquina (registo mais recente) por endereço IP

pesquisa em (ServiceMapComputer_CL) "10.229.243.232" [ resumir arg_max (TimeGenerated, *) por ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Enumerar todos os processos conhecidos numa máquina especificada

ServiceMapProcess_CL onde MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" / resumir arg_max (TimeGenerated, *) por ResourceId

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores que executam o SQL

ServiceMapComputer_CL onde ResourceName_s em (((ServiceMapProcess_CL)\*"\*sql " [ MachineResourceName_s distinto)) [ ComputerName_s distinta

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Enumere todas as versões únicas de produto de caracol no meu datacenter

ServiceMapProcess_CL onde ExecutableName_s == "curl" [ ProductVersion_s distinto

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computador de todos os computadores que executam o CentOS

ServiceMapComputer_CL onde OperatingSystemFullName_s contains_cs "CentOS" [ ComputerName_s distinta

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

Todos os dados do servidor, processo e dependência no Mapa de Serviços estão disponíveis através do Mapa de [Serviço REST API](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço Service Map. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço Service Map. Para fornecer capacidades precisas e eficientes de resolução de problemas, os dados incluem informações sobre a configuração do seu software, tais como sistema operativo e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a Declaração de [Privacidade dos Serviços Online](https://go.microsoft.com/fwlink/?LinkId=512132)da Microsoft .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre pesquisas de [log](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics para recuperar dados recolhidos pelo Service Map.

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver algum problema em instalar ou executar o Mapa de Serviço, esta secção pode ajudá-lo. Se ainda não conseguir resolver o seu problema, contacte o Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalação de agentes de dependência

#### <a name="installer-prompts-for-a-reboot"></a>Solicitações do instalador para um reboot
O agente de dependência *geralmente* não necessita de um reboot após a instalação ou remoção. No entanto, em certos casos raros, o Windows Server necessita de um reboot para continuar com uma instalação. Isto acontece quando uma dependência, normalmente a biblioteca redistribuível Microsoft Visual C++ requer um reboot por causa de um ficheiro bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Mensagem "Incapaz de instalar agente de dependência: As bibliotecas de execução do estúdio visual não foram instaladas (código = [code_number]" aparece

O agente Microsoft Dependency é construído nas bibliotecas de tempo de execução do Microsoft Visual Studio. Receberá uma mensagem se houver algum problema durante a instalação das bibliotecas. 

Os instaladores da biblioteca de tempo de execução criam registos na pasta %LOCALAPPDATA%\temp. O ficheiro `dd_vcredist_arch_yyyymmddhhmmss.log`é onde `x86` `amd64` o *arco* é ou *yyymmddhhmmssé* a data e hora (relógio 24 horas) quando o tronco foi criado. O registo fornece detalhes sobre o problema que está bloqueando a instalação.

Pode ser útil instalar as [últimas bibliotecas](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) em tempo de execução primeiro.

A tabela que se segue lista os números de código e as resoluções sugeridas.

| Código | Descrição | Resolução |
|:--|:--|:--|
| 0x17 | O instalador da biblioteca requer uma atualização do Windows que não tenha sido instalada. | Veja o mais recente registo de instalação da biblioteca.<br><br>Se uma `Windows8.1-KB2999226-x64.msu` referência for seguida `Error 0x80240017: Failed to execute MSU package,` por uma linha, não tem os pré-requisitos para instalar o KB2999226. Siga as instruções na secção de pré-requisitos no universal [C Runtime no](https://support.microsoft.com/kb/2999226) artigo Windows. Poderá ser necessário executar o Windows Update e reiniciar várias vezes para instalar os pré-requisitos.<br><br>Volte a executar o instalador do agente Microsoft Dependency. |

### <a name="post-installation-issues"></a>Problemas de pós-instalação

#### <a name="server-doesnt-appear-in-service-map"></a>Servidor não aparece no Mapa de Serviços

Se a instalação do seu agente dependency tiver sucesso, mas não vê a sua máquina na solução Service Map:
* O agente da Dependência está instalado com sucesso? Pode validar isto verificando se o serviço está instalado e em funcionamento.<br><br>
**Windows**: Procure o serviço chamado **Microsoft Dependency agent**.
**Linux**: Procure o processo de execução do **agente de dependência**da Microsoft .

* Está no [nível livre](https://azure.microsoft.com/pricing/details/monitor/)de Log Analytics? O plano Gratuito permite até cinco máquinas exclusivas do Mapa de Serviço. Quaisquer máquinas subsequentes não aparecerão no Mapa de Serviços, mesmo que as cinco anteriores já não estejam a enviar dados.

* O seu servidor está a enviar dados de registo e perf para registos do Monitor Do Azure? Vá ao Azure Monitor\Logs e faça a seguinte consulta para o seu computador: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Teve uma variedade de eventos nos resultados? Os dados são recentes? Em caso afirmativo, o seu agente Log Analytics está a funcionar corretamente e a comunicar com o espaço de trabalho. Caso contrário, verifique o agente na sua máquina: [Agente de Log Analytics para resolução](../platform/agent-windows-troubleshoot.md) de problemas do Windows ou agente Delog Analytics para resolução de [problemas do Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servidor aparece no Mapa de Serviços mas não tem processos

Se vir a sua máquina no Mapa de Serviço, mas não tiver dados de processo ou de ligação, isso indica que o agente Dependency está instalado e em funcionamento, mas o controlador de kernel não carregou. 

Verifique `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` o (Windows) ou `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux). As últimas linhas do ficheiro devem indicar porque é que o núcleo não carregou. Por exemplo, o núcleo pode não ser suportado no Linux se atualizar o seu núcleo.

## <a name="feedback"></a>Comentários

Tem algum feedback sobre o Mapa de Serviços ou sobre esta documentação?  Visite a nossa [página de Voz do Utilizador,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)onde pode sugerir funcionalidades ou votar as sugestões existentes.
