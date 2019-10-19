---
title: Visão geral de Azure Monitor | Microsoft Docs
description: Descrição geral dos serviços e das funcionalidades da Microsoft que contribuem para uma estratégia completa de monitorização para os seus serviços e aplicações do Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: d02ea18422e20aa2fede4f25cdffa985607e7b5c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597348"
---
# <a name="azure-monitor-overview"></a>Visão geral de Azure Monitor

O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos e serviços, fornecendo uma solução abrangente para coletar, analisar e agir na telemetria de seus ambientes locais e de nuvem. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem.

Apenas alguns exemplos do que você pode fazer com Azure Monitor incluem:

- Detecte e diagnostique problemas entre aplicativos e dependências com [Application insights](app/app-insights-overview.md).
- Correlacione problemas de infraestrutura com [Azure monitor para VMs](insights/vminsights-overview.md) e [Azure monitor para contêineres](insights/container-insights-overview.md).
- Aprofunde-se nos dados de monitoramento com [log Analytics](log-query/log-query-overview.md) para solução de problemas e diagnósticos detalhados.
- Suporte a operações em escala com [alertas inteligentes](platform/alerts-smartgroups-overview.md) e [ações automatizadas](platform/alerts-action-rules.md).
- Crie visualizações com [painéis](learn/tutorial-logs-dashboards.md) e [pastas de trabalho](app/usage-workbooks.md)do Azure.

## <a name="overview"></a>Visão geral
O diagrama a seguir fornece uma exibição de alto nível do Azure Monitor. No centro do diagrama estão os armazenamentos de dados para métricas e logs, que são os dois tipos fundamentais de uso de dados por Azure Monitor. À esquerda estão as [fontes de dados de monitoramento](platform/data-sources.md) que preenchem esses [armazenamentos de dados](platform/data-platform.md). À direita estão as funções diferentes que Azure Monitor executa com esses dados coletados, como análise, alertas e streaming para sistemas externos.


![Visão geral de Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Plataforma de dados de monitoramento
Todos os dados coletados pelo Azure Monitor se adaptam a um dos dois tipos fundamentais, [métricas e logs](platform/data-platform.md). [Métricas](platform/data-platform-metrics.md) são valores numéricos que descrevem um aspecto de um sistema em um determinado momento. Eles são leves e capazes de dar suporte a cenários quase em tempo real. [Os logs](platform/data-platform-logs.md) contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada tipo. A telemetria, como eventos e rastreamentos, é armazenada como logs além dos dados de desempenho para que todos possam ser combinados para análise.

Para muitos recursos do Azure, você verá os dados coletados pelo Azure Monitor diretamente na página de visão geral na portal do Azure. Examine qualquer máquina virtual, por exemplo, e você verá vários gráficos exibindo métricas de desempenho. Clique em qualquer um dos grafos para abrir os dados no [Metrics Explorer](platform/metrics-charts.md) no portal do Azure, o que permite que você regráficoize os valores de várias métricas ao longo do tempo.  Você pode exibir os gráficos interativamente ou fixá-los em um painel para exibi-los com outras visualizações.

![Métricas](media/overview/metrics.png)

Os dados de log coletados pelo Azure Monitor podem ser analisados com [consultas](log-query/log-query-overview.md) para recuperar, consolidar e analisar rapidamente os dados coletados.  Você pode criar e testar consultas usando [log Analytics](log-query/portals.md) na portal do Azure e, em seguida, analisar diretamente os dados usando essas ferramentas ou salvar consultas para uso com [visualizações](visualizations.md) ou [regras de alerta](platform/alerts-overview.md).

Azure Monitor usa uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) usada pelo data Explorer do Azure que é adequada para consultas de log simples, mas também inclui funcionalidades avançadas, como agregações, junções e análise inteligente. Você pode aprender rapidamente a linguagem de consulta usando [várias lições](log-query/get-started-queries.md).  É fornecida orientação específica para os utilizadores que já estejam familiarizados com [SQL](log-query/sql-cheatsheet.md) e [Splunk](log-query/splunk-cheatsheet.md).

![Registos](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Quais dados Azure Monitor coleta?
Azure Monitor pode coletar dados de uma variedade de fontes. Você pode considerar o monitoramento de dados para seus aplicativos em camadas que variam de seu aplicativo, qualquer sistema operacional e serviço do qual ele se baseia, até a própria plataforma. Azure Monitor coleta dados de cada uma das seguintes camadas:

- **Dados de monitoramento de aplicativos**: dados sobre o desempenho e a funcionalidade do código que você escreveu, independentemente de sua plataforma.
- **Dados de monitoramento do SO convidado**: dados sobre o sistema operacional no qual seu aplicativo está sendo executado. Isso pode estar em execução no Azure, em outra nuvem ou local. 
- **Dados de monitoramento de recursos do Azure**: dados sobre a operação de um recurso do Azure.
- **Dados de monitoramento de assinatura do Azure**: dados sobre a operação e o gerenciamento de uma assinatura do Azure, bem como dados sobre a integridade e a operação do próprio Azure. 
- **Dados de monitoramento de locatário do Azure**: dados sobre a operação de serviços do Azure no nível do locatário, como Azure Active Directory.

Assim que você criar uma assinatura do Azure e começar a adicionar recursos, como máquinas virtuais e aplicativos Web, Azure Monitor começará a coletar dados.  [Os logs de atividade](platform/activity-logs-overview.md) registram quando os recursos são criados ou modificados. As [métricas](platform/data-platform.md) informam como o recurso está sendo executado e os recursos que ele está consumindo. 

Estenda os dados que você está coletando para a operação real dos recursos, [habilitando o diagnóstico](platform/resource-logs-overview.md) e [adicionando um agente](platform/agent-windows.md) para computar recursos. Isso coletará a telemetria para a operação interna do recurso e permitirá que você configure diferentes [fontes de dados](platform/agent-data-sources.md) para coletar logs e métricas do sistema operacional convidado Windows e Linux. 

Habilite o monitoramento do [aplicativo de serviços de aplicativos](app/azure-web-apps.md) ou da [VM e do aplicativo do conjunto de dimensionamento de máquinas virtuais](app/azure-vm-vmss-apps.md)para permitir que Application insights colete informações detalhadas sobre o aplicativo, incluindo exibições de página, solicitações de aplicativo e exceção. Verifique melhor a disponibilidade do seu aplicativo Configurando um [teste de disponibilidade](app/monitor-web-app-availability.md) para simular o tráfego do usuário.

### <a name="custom-sources"></a>Fontes personalizadas
Azure Monitor pode coletar dados de log de qualquer cliente REST usando a [API do coletor de dados](platform/data-collector-api.md). Isso permite que você crie cenários de monitoramento personalizados e estenda o monitoramento para recursos que não expõem a telemetria por meio de outras fontes.



## <a name="insights"></a>Insights
Os dados de monitoramento só serão úteis se puderem aumentar sua visibilidade na operação do ambiente computacional. O Azure Monitor inclui vários recursos e ferramentas que fornecem informações valiosas sobre seus aplicativos e outros recursos dos quais eles dependem. O [monitoramento de soluções](insights/solutions.md) e recursos como [Application insights](app/app-insights-overview.md) e [Azure monitor para contêineres](insights/container-insights-overview.md) fornece informações aprofundadas sobre diferentes aspectos do seu aplicativo e serviços específicos do Azure. 

### <a name="application-insights"></a>Estatísticas das Aplicações
[Application insights](app/app-insights-overview.md) monitora a disponibilidade, o desempenho e o uso de seus aplicativos Web, sejam eles hospedados na nuvem ou no local. Ele aproveita a poderosa plataforma de análise de dados no Azure Monitor para fornecer informações aprofundadas sobre as operações do seu aplicativo e diagnosticar erros sem esperar que um usuário os relate. O Application Insights inclui pontos de conexão para uma variedade de ferramentas de desenvolvimento e se integra ao Visual Studio para dar suporte aos seus processos DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores
[Azure monitor para contêineres](insights/container-insights-overview.md) é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em clusters kubernetes gerenciados hospedados no AKs (serviço kubernetes do Azure). Ele oferece visibilidade de desempenho coletando métricas de memória e processador de controladores, nós e contêineres que estão disponíveis no kubernetes por meio da API de métricas. Os registos do contentor também são recolhidos.  Depois de habilitar o monitoramento de clusters kubernetes, essas métricas e logs são coletados automaticamente para você por meio de uma versão em contêiner do agente de Log Analytics para Linux.

![Integridade do contêiner](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
O [Azure monitor para VMs](insights/vminsights-overview.md) monitora suas máquinas virtuais (VM) do Azure em escala analisando o desempenho e a integridade de suas VMs Windows e Linux, incluindo seus processos diferentes e dependências interconectadas em outros recursos e externos processar. A solução inclui suporte para monitoramento de desempenho e dependências de aplicativo para VMs hospedadas no local ou outro provedor de nuvem.  


![Informações de VM](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Soluções de monitorização
[Soluções de monitoramento](insights/solutions.md) em Azure monitor são conjuntos de lógicas empacotados que fornecem informações para um determinado aplicativo ou serviço. Eles incluem a lógica para coletar dados de monitoramento para o aplicativo ou serviço, [consultas](log-query/log-query-overview.md) para analisar esses dados e [exibições](../log-analytics/log-analytics-view-designer.md) para visualização. As soluções de monitoramento estão [disponíveis da Microsoft e de](insights/solutions-inventory.md) parceiros para fornecer monitoramento para vários serviços do Azure e outros aplicativos.

![Soluções de monitorização](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Respondendo a situações críticas
Além de permitir que você analise os dados de monitoramento interativamente, uma solução de monitoramento eficaz deve ser capaz de responder proativamente às condições críticas identificadas nos dados coletados. Isso pode enviar um texto ou email a um administrador responsável por investigar um problema. Ou você pode iniciar um processo automatizado que tenta corrigir uma condição de erro.


### <a name="alerts"></a>Alertas
Os [alertas no Azure monitor](platform/alerts-overview.md) notificam proativamente sobre condições críticas e potencialmente tentam realizar ações corretivas. As regras de alerta com base nas métricas fornecem alertas quase em tempo real com base em valores numéricos, enquanto as regras baseadas em logs permitem uma lógica complexa entre os dados de várias fontes.

As regras de alerta no Azure Monitor usam [grupos de ação](platform/action-groups.md), que contêm conjuntos exclusivos de destinatários e ações que podem ser compartilhadas entre várias regras. Com base em seus requisitos, os grupos de ações podem executar ações como usar WebHooks para que os alertas iniciem ações externas ou se integrem às suas ferramentas de ITSM.

![Alertas](media/overview/alerts.png)

### <a name="autoscale"></a>Dimensionamento automático
O dimensionamento automático permite que você tenha a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite que você crie regras que usam métricas coletadas por Azure Monitor para determinar quando adicionar recursos automaticamente para lidar com os aumentos de carga e também economizar dinheiro removendo os recursos que estão ociosos. Especifique um número mínimo e máximo de instâncias e a lógica para quando aumentar ou diminuir os recursos.

![Dimensionamento automático](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizando dados de monitoramento
[Visualizações](visualizations.md) como gráficos e tabelas são ferramentas eficientes para resumir dados de monitoramento e apresentá-los a públicos diferentes. Azure Monitor tem seus próprios recursos para visualização de dados de monitoramento e aproveita outros serviços do Azure para publicá-los em públicos diferentes.

### <a name="dashboards"></a>Dashboards
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados, incluindo métricas e logs, em um único painel no [portal do Azure](https://portal.azure.com). Opcionalmente, você pode compartilhar o painel com outros usuários do Azure. Elementos ao longo de Azure Monitor podem ser adicionados a um painel do Azure além da saída de qualquer consulta de log ou gráfico de métricas. Por exemplo, você pode criar um painel que combina blocos que mostram um grafo de métricas, uma tabela de logs de atividades, um gráfico de uso de Application Insights e a saída de uma consulta de log.

![Dashboard](media/overview/dashboard.png)

### <a name="views"></a>Vistas
[Exibições](../log-analytics/log-analytics-view-designer.md) visualmente apresentam dados de log em Azure monitor.  Cada exibição inclui um único bloco que faz uma busca detalhada de uma combinação de visualizações, como gráficos de barras e linhas, além de listas que resumem dados críticos.  As soluções de monitoramento incluem exibições que resumem dados para um aplicativo específico, e você pode criar suas próprias exibições para apresentar dados de qualquer consulta de log. Como outros elementos no Azure Monitor, as exibições podem ser adicionadas aos painéis do Azure.

![Ver](media/overview/view.png)

### <a name="power-bi"></a>Power BI
O [Power bi](https://powerbi.microsoft.com) é um serviço de análise de negócios que fornece visualizações interativas em uma variedade de fontes de dados e é um meio eficaz de disponibilizar dados para outras pessoas dentro e fora da sua organização. Você pode configurar Power BI para [importar automaticamente os dados de log do Azure monitor](../log-analytics/log-analytics-powerbi.md) para aproveitar essas visualizações adicionais.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrar e exportar dados
Com freqüência, você terá a necessidade de integrar Azure Monitor com outros sistemas e criar soluções personalizadas que usam seus dados de monitoramento. Outros serviços do Azure funcionam com Azure Monitor para fornecer essa integração.

### <a name="event-hub"></a>Hub de Eventos
Os [hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs) são uma plataforma de streaming e um serviço de ingestão de eventos que podem transformar e armazenar dados usando qualquer provedor de análise em tempo real ou adaptadores de lote/armazenamento. Use os hubs de eventos para [transmitir dados de Azure monitor](platform/stream-monitoring-data-event-hubs.md) para o Siem do parceiro e ferramentas de monitoramento.


### <a name="logic-apps"></a>Logic Apps
Os [aplicativos lógicos](https://azure.microsoft.com/services/logic-apps) são um serviço que permite automatizar tarefas e processos de negócios usando fluxos de trabalho que se integram a diferentes sistemas e serviços. As atividades estão disponíveis para ler e gravar métricas e logs em Azure Monitor, o que permite criar fluxos de trabalho que se integram a uma variedade de outros sistemas.


### <a name="api"></a>API
Várias APIs estão disponíveis para ler e gravar métricas e logs de e para Azure Monitor além de acessar alertas gerados. Você também pode configurar e recuperar alertas. Isso fornece possibilidades essencialmente ilimitadas para criar soluções personalizadas que se integram ao Azure Monitor.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:

* [Métricas e logs](platform/data-platform.md) para os dados coletados por Azure monitor.
* [Fontes de dados](platform/data-sources.md) para saber como os diferentes componentes do seu aplicativo enviam telemetria.
* [Consultas de log](log-query/log-query-overview.md) para analisar os dados coletados.
* [Práticas recomendadas](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) para monitorar serviços e aplicativos de nuvem.
