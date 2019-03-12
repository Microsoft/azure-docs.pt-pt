---
title: Utilizar a solução mapa de serviço no Azure | Documentos da Microsoft
description: O Mapa de Serviços é uma solução no Azure que deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes para implementar o mapa de serviço no seu ambiente e utilizá-lo numa variedade de cenários.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2018
ms.author: magoedte
ms.openlocfilehash: 143d14df3019aa0c5c5dd798f656f95c8ebde372
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731088"
---
# <a name="using-service-map-solution-in-azure"></a>Utilizar a solução mapa de serviço no Azure
O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Com o mapa de serviço, pode ver os servidores da forma que considerá-los: como sistemas interconectados que fornecem serviços críticos. Mapa de serviço mostra ligações entre servidores, processos, latência de ligação de entrada e saída e as portas em qualquer arquitetura ligado a TCP, sem qualquer configuração necessária que a instalação de um agente.

Este artigo descreve os detalhes de integração e a utilizar o mapa de serviço. Para obter informações sobre como configurar o mapa de serviço e o carregamento de agentes, consulte [solução mapa de serviço de configuração no Azure]( service-map-configure.md).

>[!NOTE]
>Se já tiver implementado o mapa de serviço, agora também pode ver seus mapas no Azure Monitor para as VMs, que inclui recursos adicionais para monitorizar o estado de funcionamento da VM e o desempenho. Para obter mais informações, consulte [do Azure Monitor para descrição geral de VMs](../../azure-monitor/insights/vminsights-overview.md).


## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Ativar o mapa de serviço
1. No portal do Azure, clique em **+ criar um recurso**.
2. Na barra de pesquisa, escreva **mapa de serviço** e prima **Enter**.
3. Na página de resultados de pesquisa do marketplace, selecione **mapa de serviço** da lista.<br><br> ![Selecione a solução mapa de serviço dos resultados da pesquisa do Azure Marketplace](./media/service-map/marketplace-search-results.png)<br>
4. Sobre o **mapa de serviço** painel de descrição geral, reveja os detalhes da solução e, em seguida, clique em **criar** para iniciar o processo de inclusão para a área de trabalho do Log Analytics.<br><br> ![Integrar a solução mapa de serviço](./media/service-map/service-map-onboard.png).
5. Na **configurar uma solução** painel, selecione um existente ou criar uma nova área de trabalho do Log Analytics.  Para obter mais informações sobre como criar uma nova área de trabalho, consulte [criar uma área de trabalho do Log Analytics no portal do Azure](../../azure-monitor/learn/quick-create-workspace.md). Depois de fornecer as informações necessárias, clique em **criar**.  

Enquanto as informações são confirmadas e a solução é implementada, pode acompanhar o progresso em **notificações** no menu. 

Mapa de serviço de acesso no portal do Azure da sua área de trabalho do Log Analytics e selecione a opção **soluções** no painel à esquerda.<br><br> ![Selecione a opção de soluções na área de trabalho](./media/service-map/select-solution-from-workspace.png).<br> Na lista de soluções, selecione **ServiceMap(workspaceName)** e, em que o clique de página de descrição geral do mapa de serviço solução no mosaico de resumo do mapa de serviço.<br><br> ![Mosaico de resumo de mapa de serviço](./media/service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de utilização: Tornar seu departamento de TI processa com reconhecimento de dependência

### <a name="discovery"></a>Deteção
Mapa de serviço cria automaticamente um mapa de referência comuns de dependências entre os servidores, processos e serviços de terceiros. Ele Deteta e mapeia todas as dependências TCP, identificação de ligações de surpresa, sistemas de terceiros remotos que confiar e dependências para áreas escuros tradicionais da sua rede, como o Active Directory. Mapa de serviço Deteta as ligações de rede com falha que sistemas geridos estão a tentar fazer, ajudando-o a identificar potenciais erro de configuração do servidor, indisponibilidade de serviço e problemas de rede.

### <a name="incident-management"></a>gestão de incidentes
Mapa de serviço ajuda a eliminar as suposições de isolamento de problema, que mostra como os sistemas estão ligados e afetar entre si. Além de identificar ligações falhadas, ele ajuda a identificar balanceadores de carga configurado incorretamente, uma carga surpreendente ou excessiva nos serviços críticos e não autorizados clientes, tais como computadores de desenvolvedores falando em sistemas de produção. Ao utilizar fluxos de trabalho integrados com o controlo de alterações, também pode ver se um evento de alteração numa máquina de back-end ou serviço explica a causa de raiz de um incidente.

### <a name="migration-assurance"></a>Garantia de migração
Ao utilizar o mapa de serviço, pode efetivamente planear, acelerar e validar as migrações do Azure, que ajuda a garantir que nada seja deixado e não ocorrerem falhas de surpresa. Pode descobrir todos os sistemas interdependentes, que têm de migrar em conjunto, avaliar a configuração do sistema e a capacidade e identificar se um sistema em execução ainda está a servir os utilizadores ou é uma candidata para desativar em vez de migração. Após a migração estiver concluída, pode verificar a de carga de cliente e de identidade para verificar que os clientes e sistemas de teste estão a ligar. Se as definições de firewall e de planejamento de sub-rede tem problemas, ligações falhadas no mapa de serviço maps ponto para os sistemas que necessitam de conectividade.

### <a name="business-continuity"></a>Continuidade de negócio
Se estiver a utilizar o Azure Site Recovery e precisa de ajuda a definir a sequência de recuperação para o seu ambiente de aplicativo, o mapa de serviço pode automaticamente mostram-lhe como sistemas dependem uns dos outros para assegurar que o seu plano de recuperação é confiável. Escolhendo um servidor crítico ou de grupo e visualizar os seus clientes, é possível identificar os sistemas de front-end para recuperar após o servidor é restaurado e está disponível. Por outro lado, ao examinar as dependências de back-end de servidores críticos, pode identificar quais os sistemas para recuperar antes de seus sistemas de foco são restaurados.

### <a name="patch-management"></a>Gerenciamento de patches
Mapa de serviço melhora a utilização da avaliação da atualização do sistema, mostrando-lhe que outras equipes e servidores dependem do seu serviço, portanto, pode notificá-los antecipadamente antes de interrompa seus sistemas para a aplicação de patches. Mapa de serviço também melhora o gerenciamento de patches, mostrando-lhe se seus serviços estão disponíveis e corretamente ligada após são corrigidos e reiniciados.

## <a name="mapping-overview"></a>Visão geral do mapeamento
Agentes de mapa de serviço recolher informações sobre todos os processos ligado a TCP no servidor onde estão instalados e detalhes sobre as ligações de entrada e saídas para cada processo.

Na lista no painel esquerdo, pode selecionar máquinas ou grupos que têm agentes de mapa de serviço para visualizar as respetivas dependências através de um intervalo de tempo especificado. Dependência de máquina mapeia o foco numa máquina específica e mostram todas as máquinas clientes TCP diretos ou servidores dessa máquina.  Mapas de grupo de máquinas mostram conjuntos de servidores e as respetivas dependências.

![Descrição geral de mapa de serviço](media/service-map/service-map-overview.png)

As máquinas que podem ser expandidas no mapa para mostrar a execução de processo grupos e processos com ligações de rede ativas durante o intervalo de tempo selecionado. Quando uma máquina remota com um agente de mapa de serviço é expandida para mostrar os detalhes do processo, são apresentados apenas esses processos que comunicam com a máquina de foco. A contagem de máquinas de front-end sem agente que ligar à máquina de foco é indicada no lado esquerdo dos processos de que estes se ligam a. Se a máquina de foco está a efetuar uma ligação para uma máquina de back-end que tenha sem agente, o servidor de back-end está incluído num grupo de porta do servidor, juntamente com outras ligações para o mesmo número de porta.

Por predefinição, o maps de mapa de serviço mostram os últimos 30 minutos de informações de dependência. Com os controles de tempo no canto superior esquerdo, pode consultar o maps para intervalos de tempo do histórico de até uma hora para mostrar como dependências viu no passado (por exemplo, durante um incidente ou antes de ocorrer uma alteração). Dados de mapa de serviço são armazenados durante 30 dias em áreas de trabalho pagas e durante sete dias em áreas de trabalho gratuitas.

## <a name="status-badges-and-border-coloring"></a>Destaques de estado e as cores de limite
Na parte inferior de cada servidor no mapa pode ser uma lista de destaques de estado transmitir informações de estado sobre o servidor. Destaques indicarem que existem algumas informações relevantes para o servidor de um das integrações de solução. Clicar num distintivo leva-o diretamente para os detalhes do Estado no painel da direita. Os destaques de estado atualmente disponíveis incluem alertas, serviço de atendimento, as alterações, segurança e atualizações.

Dependendo da gravidade dos destaques de estado, limites de nó de computador podem ser colorido vermelho (crítico), amarelo (aviso) ou azul (informativos). A cor representa o estado mais grave de qualquer um dos destaques de estado. Um limite cinzento indica um nó que possui não indicadores de estado.

![Destaques de estado](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupos de processo
Grupos de processo combinam processos que estão associados a um produto ou serviço comum num grupo de processos.  Quando um nó de máquina é expandido apresentará processos autónomo juntamente com os grupos de processo.  Se quaisquer ligações de entrada e saídas para um processo dentro de um grupo de processo falhou, em seguida, a ligação é mostrado como falhado para o grupo de todo o processo.

## <a name="machine-groups"></a>Grupos de Máquinas
Grupos de máquinas permitem-lhe ver mapas centrados em torno de um conjunto de servidores, não apenas um para que possa ver todos os membros de um cluster de aplicação ou servidor de várias camado num mapa.

Os utilizadores selecionar quais os servidores pertencem um grupo em conjunto e escolha um nome para o grupo.  Em seguida, pode optar por ver o grupo com todos os seus processos e ligações ou abri-lo com apenas os processos e as ligações que estão diretamente relacionam a outros membros do grupo.

![Grupo de máquinas](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Criação de um grupo de máquina
Para criar um grupo, selecione a máquina ou máquinas que pretende nas máquinas lista e clique em **adicionar ao grupo**.

![Create Group](media/service-map/machine-groups-create.png)

Aqui, pode escolher **criar novo** e dê um nome de grupo.

![Nome do grupo](media/service-map/machine-groups-name.png)

>[!NOTE]
>Grupos de máquinas estão limitados a 10 servidores.

### <a name="viewing-a-group"></a>Visualização de um grupo
Depois de criar alguns grupos, pode visualizá-los ao escolher o separador de grupos.

![Separador de grupos](media/service-map/machine-groups-tab.png)

Em seguida, selecione o nome do grupo para ver o mapa para esse grupo de máquina.
![Grupo de máquinas](media/service-map/machine-group.png) as máquinas que pertencem ao grupo são descritas em branco no mapa.

Expandir o grupo irá listar as máquinas que compõem o grupo de máquina.

![Máquinas do grupo de máquinas](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrar por processos
Pode ativar/desativar a vista do mapa entre a mostrar todos os processos e as ligações no grupo e apenas os que estão diretamente relacionam ao grupo de máquina.  A exibição padrão é mostrar todos os processos.  Pode alterar o modo de exibição clicando no ícone de filtro acima do mapa.

![Filtro de grupo](media/service-map/machine-groups-filter.png)

Quando **todos os processos** é selecionado, o mapa irá incluir todos os processos e ligações em cada uma das máquinas no grupo.

![Grupo de máquinas, todos os processos](media/service-map/machine-groups-all.png)

Se alterar a vista para mostrar apenas **processos ligados por grupo**, o mapa será reduzido para apenas os processos e as ligações que estão ligadas diretamente a outras máquinas no grupo, criar uma vista simplificada.

![Grupo de máquina filtrado processos](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>A adicionar máquinas a um grupo
Ao adicionar computadores a um grupo existente, selecione as caixas junto as máquinas que pretende e, em seguida, clique em **adicionar ao grupo**.  Em seguida, selecione o grupo que pretende adicionar as máquinas.
 
### <a name="removing-machines-from-a-group"></a>Remover máquinas de um grupo
Na lista de grupos, expanda o nome do grupo para listar as máquinas no grupo de máquina.  Em seguida, clique no menu de reticências junto a máquina que pretende remover e escolha **remover**.

![Remover máquina de grupo](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Remover ou mudar o nome de um grupo
Clique no menu de reticências junto ao nome do grupo na lista do grupo.

![Menu de grupo do computador](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ícones de função
Determinados processos atendem a funções específicas em máquinas: servidores web, servidores de aplicações, base de dados e assim por diante. Mapa de serviço annotates caixas de processos e computadores com ícones de função para ajudar a identificar rapidamente a função de um processo ou o servidor funciona.

| Ícone de função | Descrição |
|:--|:--|
| ![Servidor Web](media/service-map/role-web-server.png) | Servidor Web |
| ![Servidor de aplicações](media/service-map/role-application-server.png) | Servidor de aplicações |
| ![Servidor de bases de dados](media/service-map/role-database.png) | Servidor de bases de dados |
| ![Servidor LDAP](media/service-map/role-ldap.png) | Servidor LDAP |
| ![Servidor SMB](media/service-map/role-smb.png) | Servidor SMB |

![Ícones de função](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Ligações com falhas
Ligações falhadas são exibidas no mapa de serviço mapas para processos e computadores, com uma linha vermelha tracejada que indica que é um sistema de cliente que está a conseguir chegar a um processo ou a porta. Ligações falhadas forem comunicadas a partir de qualquer sistema com um agente de mapa de serviço implementado se esse sistema for a tentar a falha de ligação. Mapa de serviço mede este processo ao observar os sockets TCP que não obedeçam a estabelecer uma ligação. Esta falha pode resultar de uma firewall, uma configuração incorreta no cliente ou servidor ou um serviço remoto indisponível.

![Ligações com falhas](media/service-map/failed-connections.png)

Noções sobre ligações falhadas podem ajudar na resolução de problemas, validação da migração, a análise de segurança e a compreensão arquitetônica gerais. Ligações falhadas, às vezes, são inofensivos, mas, muitas vezes, apontam diretamente para um problema, como um ambiente de ativação pós-falha, de repente, tornando-se inacessível ou duas camadas da aplicação que está a ser não é possível comunicar com o após a migração para a cloud.

## <a name="client-groups"></a>Grupos de Clientes
Grupos de clientes são caixas no mapa, que representam computadores cliente que não têm os agentes de dependência. Um único grupo de cliente representa os clientes para uma máquina ou processo individual.

![Grupos de Clientes](media/service-map/client-groups.png)

Para ver os endereços IP dos servidores num grupo de cliente, selecione o grupo. O conteúdo do grupo está listado na **das propriedades do grupo de cliente** painel.

![Propriedades do grupo de cliente](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de portas de servidor
Grupos de portas de servidor são caixas que representam as portas de servidor nos servidores que não têm os agentes de dependência. A caixa contém a porta do servidor e uma contagem do número de servidores com ligações para essa porta. Expanda a caixa para ver os servidores individuais e as ligações. Se houver apenas um servidor na caixa, o nome ou endereço IP está listado.

![Grupos de portas de servidor](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu de contexto
Ao clicar no botão de reticências (...) na parte superior direita de qualquer servidor apresenta o menu de contexto para esse servidor.

![Ligações com falhas](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Carregar mapa do servidor
Clicar **carregar mapa do servidor** leva-o para um novo mapa com o servidor selecionado como a nova máquina de foco.

### <a name="show-self-links"></a>Mostrar auto-ligações
Clicar **Mostrar Self-Links** redesenha o nó de servidor, incluindo algum de auto-ligações, que são as conexões TCP, que inicia e terminam no processos dentro do servidor. Se auto-ligações são apresentados, as alterações do comando de menu para **ocultar Self-Links**, de modo a que pode desativá-las.

## <a name="computer-summary"></a>Resumo do computador
O **resumo da máquina** painel inclui uma visão geral do sistema operativo de um servidor, contagens de dependência e dados a partir de outras soluções. Esses dados incluem métricas de desempenho, pedidos de suporte técnico do serviço, controlo de alterações, segurança e atualizações.

![Painel de resumo de máquina](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriedades do computador e de processo
Quando navega, um mapa do mapa de serviço, pode selecionar máquinas e processos para obter o contexto adicional sobre as respetivas propriedades. Máquinas fornecem informações sobre DNS name, IPv4 endereços, CPU e memória, capacidade, tipo da VM, sistema operativo e versão, reiniciar pela última vez e os IDs dos seus agentes do OMS e do mapa de serviço.

![Painel de propriedades da máquina](media/service-map/machine-properties.png)

Pode recolher detalhes do processo de sistema operacional de metadados sobre a execução de processos, incluindo o nome do processo, descrição de processo, nome de utilizador e domínio (no Windows), o nome da empresa, nome do produto, versão do produto, diretório de trabalho, linha de comandos e processo hora de início.

![Painel de propriedades do processo](media/service-map/process-properties.png)

O **resumo do processo de** painel fornece informações adicionais acerca da conetividade do processo, incluindo suas portas vinculadas, ligações de entrada e saídas e ligações falhadas.

![Painel de resumo do processo](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integração de alertas
Mapa de serviço se integra com alertas do Azure para mostrar alertas acionados para o servidor selecionado no intervalo de tempo selecionado. O servidor exibe um ícone, se existirem alertas atuais e o **máquina alertas** painel lista os alertas.

![Painel de alertas do computador](media/service-map/machine-alerts.png)

Para ativar o mapa de serviço apresentar os alertas relevantes, crie uma regra de alerta que é acionado para um computador específico. Para criar alertas adequadas:
- Incluir uma cláusula ao grupo por computador (por exemplo, **por minuto do intervalo de computador**).
- Escolha um alerta com base na medida da métrica.

## <a name="log-events-integration"></a>Integração de eventos de registo
Mapa de serviço integra-se a pesquisa de registos a mostrar uma contagem de todos os eventos de log disponíveis para o servidor selecionado durante o intervalo de tempo selecionado. Pode clicar em qualquer linha na lista de contagens de eventos para saltar para pesquisa de registos e ver os eventos de registo individuais.

![Painel de eventos do registo de máquina](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integração de serviço de atendimento
Integração de mapa de serviço com o conector de gestão do serviço de TI é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Log Analytics. A integração no mapa de serviço está identificado como "Serviço de atendimento." Para obter mais informações, consulte [centralmente gerir itens de trabalho ITSM com o conector de gestão do serviço de TI](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

O **máquina Service Desk** painel apresenta uma lista de todos os eventos de gerenciamento de serviços para o servidor selecionado no intervalo de tempo selecionado. O servidor exibe um ícone, se não existem itens atuais e o painel do serviço de atendimento de máquina lista-los.

![Painel do serviço de atendimento de máquina](media/service-map/service-desk.png)

Para abrir o item na sua solução ITSM ligada, clique em **vista de Item de trabalho**.

Para ver os detalhes do item na pesquisa de registos, clique em **Mostrar na pesquisa de registos**.
Métricas de ligação são escritas para duas novas tabelas no Log Analytics 

## <a name="change-tracking-integration"></a>Alterar a integração de controlo
Integração de mapa de serviço com o controlo de alterações é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Log Analytics.

O **controlo de alterações de máquina** painel apresenta uma lista de todas as alterações, com a mais recente primeiro, juntamente com uma ligação para desagregar para pesquisa de registos para obter mais detalhes.

![Painel de controlo de alterações de máquina](media/service-map/change-tracking.png)

A imagem seguinte é uma visão detalhada de um evento de ConfigurationChange que poderá ver depois de selecionar **Mostrar no Log Analytics**.

![ConfigurationChange eventos](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integração de desempenho
O **desempenho de máquina** painel apresenta métricas de desempenho padrão para o servidor selecionado. As métricas incluem a utilização da CPU, utilização da memória, os bytes de rede enviados e recebidos e uma lista dos processos principais por bytes de rede enviados e recebidos.

![Painel de desempenho da máquina](media/service-map/machine-performance.png)

Para ver dados de desempenho, se pretender [ativar os contadores de desempenho do Log Analytics adequados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Os contadores que pretende ativar:

Windows:
- Processor(*)\\% tempo do processador
- Memória\\% Bytes dedicados em utilização
- Rede Adapter(*)\\Bytes enviados/seg
- Rede Adapter(*)\\Bytes recebidos/seg

Linux:
- Processor(*)\\% tempo do processador
- Memory(*)\\% de memória utilizada
- Rede Adapter(*)\\Bytes enviados/seg
- Rede Adapter(*)\\Bytes recebidos/seg

Para obter os dados de desempenho de rede, tem também tiver ativado a solução do Wire Data 2.0 na sua área de trabalho.
 
## <a name="security-integration"></a>Integração de segurança
Integração de mapa de serviço com segurança e auditoria é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Log Analytics.

O **segurança de máquina** painel mostra os dados da solução de segurança e auditoria do servidor selecionado. O painel lista um resumo dos problemas de segurança pendentes para o servidor durante o intervalo de tempo selecionado. Ao clicar em qualquer uma das explorações de problemas de segurança para baixo para uma pesquisa de registos para obter detalhes sobre os mesmos.

![Painel de segurança da máquina](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integração de atualizações
Integração de mapa de serviço com a gestão de atualizações é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Log Analytics.

O **atualizações de máquinas** painel apresenta os dados da solução de gerenciamento de atualização para o servidor selecionado. O painel lista um resumo de todas as atualizações em falta para o servidor durante o intervalo de tempo selecionado.

![Painel de controlo de alterações de máquina](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registos do Log Analytics
Dados de inventário de computador e o processo de mapa de serviço estão disponíveis para [pesquisa](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics. Pode aplicar esses dados para cenários que incluem planos de migração, análise de capacidade, deteção e resolução de problemas de desempenho a pedido.

Um registo é gerado por hora para cada computador exclusivo e o processo, além dos registos que são gerados quando um processo ou o computador inicia ou é integrado com o mapa de serviço. Estes registos têm as propriedades nas tabelas seguintes. Os campos e os valores nos eventos ServiceMapComputer_CL mapeiam para campos do recurso de máquina na API ServiceMap do Azure Resource Manager. Os campos e os valores nos eventos ServiceMapProcess_CL mapeiam os campos do recurso de processo na API ServiceMap do Azure Resource Manager. O campo de ResourceName_s corresponda o campo de nome do recurso de Gestor de recursos correspondente. 

>[!NOTE]
>À medida que aumentam a funcionalidades de mapa de serviço, estes campos estão sujeitos a alterações.

Existem propriedades internamente geradas, pode usar para identificar processos exclusivos e computadores:

- Computador: Uso *ResourceId* ou *ResourceName_s* para identificar exclusivamente um computador dentro de uma área de trabalho do Log Analytics.
- Processo: Uso *ResourceId* para identificar exclusivamente um processo numa área de trabalho do Log Analytics. *ResourceName_s* é exclusivo dentro do contexto da máquina em que o processo está em execução (MachineResourceName_s) 

Uma vez que vários registos podem existir num processo especificado e o computador num intervalo de tempo especificado, as consultas podem devolver mais de um registo para o mesmo computador ou processo. Para incluir apenas o registo mais recente, adicione "| eliminação de duplicados ResourceId"para a consulta.

### <a name="connections"></a>Ligações
Métricas de ligação são escritas para uma nova tabela no Log Analytics - VMConnection. Esta tabela fornece informações sobre as ligações para uma máquina (entrada e saída). Métricas de ligação também são expostas com APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo.  Ligações de TCP resultantes de "*aceitar*ing - num soquete de escuta são entrada, enquanto os criados por *ligar*, ing para um determinado IP e porta são saída. A direção de uma conexão é representada pela propriedade de direção, o que pode ser definida para o **entrada** ou **saída**. 

Registos nestas tabelas são gerados a partir de dados reportados pelo agente de dependência. Cada registo representa uma observação ao longo de um intervalo de tempo de um minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registro contém informações para identificar a entidade respectiva, ou seja, ligação ou porta, bem como as métricas associadas com essa entidade. Atualmente, apenas as atividades de rede que ocorre usando TCP sobre IPv4 é comunicada.

Para gerir o custo e a complexidade, registos de ligação não representam conexões de rede físico individuais. Várias ligações de rede física são agrupadas numa conexão lógica, que, em seguida, é refletida na respetiva tabela.  Significado, regista na *VMConnection* tabela representam um agrupamento lógico e não as individuais físicas ligações que estão a ser observadas. Ligação de rede física que partilham o mesmo valor para os seguintes atributos durante um intervalo específico de um minuto, são agregados num único registo lógico na *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
|Direção |Direção da conexão, o valor é *entrada* ou *saída* |
|Computador |O FQDN do computador |
|Processo |Identidade de processo ou grupos de processos, iniciar/aceitar a ligação |
|SourceIp |Endereço IP de origem |
|DestinationIp |Endereço IP de destino |
|DestinationPort |Número de porta de destino |
|Protocolo |Protocolo utilizado para a ligação.  É de valores *tcp*. |

Para levar em conta o impacto de agrupamento, são fornecidas informações sobre o número de ligações físicos agrupados nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|LinksEstablished |O número de ligações de rede física que foram estabelecidas durante a janela de tempo de criação de relatórios |
|LinksTerminated |O número de ligações de rede física que forem terminadas durante a janela de tempo de criação de relatórios |
|LinksFailed |O número de ligações de rede física que falharam durante a janela de tempo de criação de relatórios. Estas informações estão atualmente disponíveis apenas para ligações de saída. |
|LinksLive |O número de ligações de rede física que foram abertas no final da janela de tempo de criação de relatórios|

#### <a name="metrics"></a>Métricas

Para além das métricas de contagem de ligação, informações sobre o volume de dados enviados e receberam numa determinada ligação lógica ou a porta de rede também inclui as seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|BytesSent |Número total de bytes que foram enviados durante a janela de tempo de criação de relatórios |
|BytesReceived |Número total de bytes que foram recebidos durante a janela de tempo de criação de relatórios |
|Respostas |O número de respostas foi observada durante a janela de tempo de criação de relatórios. 
|ResponseTimeMax |O maior tempo de resposta (milissegundos) foi observado durante a janela de tempo de criação de relatórios.  Se nenhum valor, a propriedade está em branco.|
|ResponseTimeMin |O menor tempo de resposta (milissegundos) foi observado durante a janela de tempo de criação de relatórios.  Se nenhum valor, a propriedade está em branco.|
|ResponseTimeSum |A soma de todos os tempos de resposta (milissegundos) foi observada durante a janela de tempo de criação de relatórios.  Se nenhum valor, a propriedade está em branco|

O terceiro tipo de dados está a ser comunicados é o tempo de resposta - quanto um chamador passa aguardando para um pedido enviado através de uma ligação para serem processados e emitida por ponto final remoto. O tempo de resposta reportado é uma estimativa do tempo de resposta verdadeiro do protocolo subjacente do aplicativo. É calculada usando a heurística com base na observação do fluxo de dados entre o final de origem e de destino de uma ligação de rede física. Conceitualmente, ele é a diferença entre a hora que do último byte de um pedido deixa o remetente e a hora ao último byte de resposta receber retorne a ele. Estes dois carimbos de data / é usados para delinear a pedido e resposta a eventos numa determinada ligação física. A diferença entre eles representa o tempo de resposta de um único pedido. 

Neste primeiro lançamento desse recurso, o nosso algoritmo é uma aproximação que poderá funcionar com variados graus de sucesso, dependendo do protocolo de real do aplicativo usado para uma ligação de rede fornecido. Por exemplo, a abordagem atual funciona bem para protocolos de solicitação-resposta com base, como HTTP (S), mas não funcionam com unidirecional ou protocolos baseada na fila de mensagens.

Aqui estão alguns pontos importantes a considerar:

1. Se um processo aceitar ligações no mesmo endereço IP, mas ao longo de várias interfaces de rede, será reportado um registo separado para cada interface. 
2. Registos com o IP de caráter universal não irão conter nenhuma atividade. Eles estão incluídos para representar o fato de que uma porta na máquina é aberto a entrada de tráfego.
3. Para reduzir verbosidade e volume de dados, registos com o IP de caráter universal serão omitidos quando existe um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registo IP de caráter universal for omitido, a propriedade de registo de IsWildcardBind com o endereço IP específico, será de ser definida como "Verdadeiro" para indicar que a porta está exposta através de cada interface da máquina de geração de relatórios.
4. Portas que estão vinculadas apenas numa interface específica ter IsWildcardBind definido como "False".

#### <a name="naming-and-classification"></a>Nomenclatura e classificação
Para sua comodidade, o endereço IP do final de uma conexão remota está incluído na propriedade RemoteIp. Para ligações de entrada, RemoteIp é o mesmo como SourceIp, enquanto para ligações de saída, é o mesmo que DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes de canônicos de DNS comunicados pela máquina para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification estão reservadas para utilização futura. 

#### <a name="geolocation"></a>Geolocalização
*VMConnection* também inclui informações de localização geográfica para o final de cada registo de ligação remota nas seguintes propriedades do registo: 

| Propriedade | Descrição |
|:--|:--|
|RemoteCountry |O nome do país RemoteIp de alojamento.  Por exemplo, *dos Estados Unidos* |
|RemoteLatitude |A latitude da localização geográfica.  Por exemplo, *47.68* |
|RemoteLongitude |A longitude da localização geográfica.  Por exemplo, *-122.12* |

#### <a name="malicious-ip"></a>IP malicioso
Cada propriedade RemoteIp *VMConnection* tabela é comparada com um conjunto de IPs com atividades maliciosas conhecidas. Se o RemoteIp é identificado como malicioso as seguintes propriedades serão preenchidas (elas estiverem vazias, quando o IP não é considerado malicioso) nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|MaliciousIp |O endereço de RemoteIp |
|IndicatorThreadType |Indicador de ameaça detetada é um dos seguintes valores *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *software maligno*, *Phishing*, *Proxy*, *PUA*, *Lista de observação*.   |
|Descrição |Descrição da ameaça observada. |
|TLPLevel |Nível de protocolo de semáforo (TLP) é um dos valores definidos, *White*, *verde*, *Amber*, *Red*. |
|Confiança |Os valores são *0 – 100*. |
|Gravidade |Os valores são *0 – 5*, onde *5* é o mais grave e *0* não for grave em todos os. Valor predefinido é *3*.  |
|FirstReportedDateTime |Na primeira vez que o fornecedor reportou o indicador. |
|LastReportedDateTime |A última vez que o indicador foi visto por Interflow. |
|IsActive |Indica a indicadores são desativados com *True* ou *falso* valor. |
|ReportReferenceLink |Links para relatórios relacionados com um determinado observable. |
|AdditionalInformation |Fornece informações adicionais, se aplicável, sobre a ameaça observada. |

### <a name="servicemapcomputercl-records"></a>Registos de ServiceMapComputer_CL
Registos com um tipo de *ServiceMapComputer_CL* tiver dados de inventário para servidores com os agentes de mapa de serviço. Estes registos têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para o computador da área de trabalho |
| ResourceName_s | O identificador exclusivo para o computador da área de trabalho |
| ComputerName_s | O FQDN do computador |
| Ipv4Addresses_s | Endereços de uma lista de IPv4 do servidor |
| Ipv6Addresses_s | Endereços de uma lista de IPv6 do servidor |
| DnsNames_s | Uma matriz de nomes de DNS |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemFullName_s | O nome completo do sistema operativo  |
| Bitness_s | O número de bits da máquina (32 bits ou 64 bits)  |
| PhysicalMemory_d | A memória física em MB |
| Cpus_d | O número de CPUs |
| CpuSpeed_d | A velocidade de CPU em MHz|
| VirtualizationState_s | *unknown*, *physical*, *virtual*, *hypervisor* |
| VirtualMachineType_s | *Hyper-v*, *vmware*e assim por diante |
| VirtualMachineNativeMachineId_g | O ID da VM como atribuído pelo seu hipervisor |
| VirtualMachineName_s | O nome da VM |
| BootTime_t | O tempo de inicialização |

### <a name="servicemapprocesscl-type-records"></a>Registos do tipo de ServiceMapProcess_CL
Registos com um tipo de *ServiceMapProcess_CL* tiver dados de inventário para processos ligados por TCP em servidores com os agentes de mapa de serviço. Estes registos têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para um processo dentro da área de trabalho |
| ResourceName_s | O identificador exclusivo para um processo na máquina em que está em execução|
| MachineResourceName_s | O nome do recurso da máquina |
| ExecutableName_s | O nome do executável de processo |
| StartTime_t | A hora de início do conjunto de processo |
| FirstPid_d | O PID primeiro no conjunto de processos |
| Description_s | A descrição de processo |
| CompanyName_s | O nome da empresa |
| InternalName_s | O nome interno |
| ProductName_s | O nome do produto |
| ProductVersion_s | A versão do produto |
| FileVersion_s | A versão do ficheiro |
| CommandLine_s | A linha de comandos |
| ExecutablePath _s | O caminho para o ficheiro executável |
| WorkingDirectory_s | O diretório de trabalho |
| Nome de Utilizador | A conta sob a qual o processo está em execução |
| UserDomain | O domínio em que o processo está em execução |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

### <a name="list-all-known-machines"></a>Listar todas as máquinas conhecidas
ServiceMapComputer_CL | resumir arg_max(TimeGenerated, *) por ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste a capacidade de memória física de todos os computadores geridos.
ServiceMapComputer_CL | resumir arg_max(TimeGenerated, *) por ResourceId | projeto PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Nome de computador da lista, o DNS, o IP e o sistema operacional.
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comandos
ServiceMapProcess_CL | onde CommandLine_s contains_cs "sql" | resumir arg_max(TimeGenerated, *) por ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Encontrar uma máquina (registo mais recente) ao nome do recurso
pesquisa no "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" (ServiceMapComputer_CL) | resumir arg_max(TimeGenerated, *) por ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar uma máquina (registo mais recente) por endereço IP
pesquisa no "10.229.243.232" (ServiceMapComputer_CL) | resumir arg_max(TimeGenerated, *) por ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Lista de todos os processos num computador especificado
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-computers-running-sql"></a>Lista de todos os computadores que executam o SQL
ServiceMapComputer_CL | onde ResourceName_s no ((pesquisa no (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | ComputerName_s distintos

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões de produto exclusiva de curl em meu datacenter
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computadores de todos os computadores em execução no CentOS
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s

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
Todos os dados de servidor, o processo e dependência no mapa de serviço estão disponíveis através da [API de REST do mapa de serviço](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Dados de utilização e diagnóstico
A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço do mapa de serviço. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço de mapa de serviço. Para fornecer capacidades de resolução de problemas exatas e eficientes, os dados incluem informações sobre a configuração de software, como o sistema operativo e o versão, o endereço IP, o nome DNS e o nome da estação de trabalho. Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha de dados e a utilização, consulte a [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [pesquisas de registos](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics para obter os dados recolhidos pelo mapa de serviço.


## <a name="troubleshooting"></a>Resolução de problemas
Consulte a [secção do documento de mapa de serviço de configuração de resolução de problemas]( service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Comentários
Tem comentários para nós sobre o mapa de serviço ou esta documentação?  Visite nosso [página do Uservoice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde pode sugerir funcionalidades ou sugestões existentes de votos.
