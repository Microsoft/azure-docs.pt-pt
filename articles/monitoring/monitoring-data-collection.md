---
title: A monitorização dos dados recolhidos pelo Monitor do Azure | Documentos da Microsoft
description: A monitorização dos dados recolhidos pelo Azure Monitor é separada nas métricas que são leves e capaz de suportar quase cenários em tempo real e registos que são armazenados no Log Analytics para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: bwren
ms.openlocfilehash: 8d3e0874637bc3f13905c6038349b34c18b5fe56
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637802"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>A monitorização dos dados recolhidos pelo Azure Monitor
[O Azure Monitor](../azure-monitor/overview.md) é um serviço que o ajuda a monitorizar as suas aplicações e os recursos que dependem. Central para esta função é um armazenamento de telemetria e outros dados de recursos monitorizados. Este artigo fornece uma descrição completa de como os dados são armazenados e usados pelo Azure Monitor.

Todos os dados recolhidos pelo Monitor do Azure se encaixa em um dos dois tipos fundamentais, [métricas](#metrics) e [registos](#logs). As métricas são valores numéricos que descrevem algum aspeto de um sistema num momento específico no tempo. Elas são simples e capaz de oferecer suporte a cenários em tempo real em tempo quase. Os registos contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades de cada tipo. Telemetria, como eventos e rastreios são armazenadas como registos além do mais dados de desempenho para que ele possível combinar tudo para análise.

![Descrição geral do Azure Monitor](../azure-monitor/media/overview/overview.png)

## <a name="metrics"></a>Métricas
As métricas são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. Elas são simples e capaz de oferecer suporte a cenários em tempo real em tempo quase. Métricas são recolhidas em intervalos regulares, independentemente do valor é alterado. Eles são úteis para alertar porque eles podem ser convertidos com frequência, e um alerta pode ser acionado rapidamente com a lógica relativamente simples. 

Por exemplo, poderá recolher utilização do processador de uma máquina virtual a cada minuto ou o número de utilizadores com sessão iniciada sua aplicação a cada 10 minutos. Poderia acionar um alerta quando uma dessas recolhidos valores ou até mesmo a diferença entre dois valores excede um limiar definido.

Atributos específicos de métricas no Azure incluem o seguinte:

* Recolhidas a frequência de um minuto, salvo especificação em contrário na definição da métrica.
* Identificado exclusivamente por um nome de métrica e um espaço de nomes que age como uma categoria.
* Armazenados durante dias 93. Pode copiar as métricas para o Log Analytics para fins de tendência de longo prazo.

Cada valor de métrica tem as seguintes propriedades:
* A hora em que o valor foi recolhido.
* O tipo de medida que representa o valor.
* O recurso o valor está associado.
* O valor em si.
* Algumas métricas podem ter várias dimensões, conforme descrito na secção seguinte. Métricas personalizadas podem ter até 10 dimensões.

### <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
As dimensões de uma métrica são pares nome-valor que transportar dados adicionais para descrever o valor de métrica. Por exemplo, uma métrica _espaço em disco disponível_ pode ter uma dimensão chamada _unidade_ com valores _c:_, _D:_, que permitiria a visualização o espaço em disco disponível em todas as unidades de ou para cada unidade individualmente. 

O exemplo abaixo ilustra dois conjuntos de dados para uma métrica hipotética chamada _débito de rede_. O primeiro conjunto de dados não tem dimensões. O segundo conjunto de dados mostra os valores com duas dimensões, I_P Address_ e _direção_:

### <a name="network-throughput"></a>Débito de rede

 |Carimbo de data/hora        | Valor de métrica | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kbps | 
   | 8/9/2017 8:15 | 1,141.4 kbps |
   | 8/9/2017 8:16 | 1,110.2 kbps |

Esta métrica não dimensionais pode apenas resposta uma pergunta básica, como "o que era minha débito de rede num determinado momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Débito de rede + duas dimensões ("IP" e "Direção")

| Carimbo de data/hora          | Dimensão "IP" | Dimensão de "Direção" | Valor de métrica| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Direção = "Enviar"    | 646.5 kbps |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Direção = "Receber" | 420.1 kbps |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Direção = "Enviar"    | 150.0 kbps | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Direção = "Receber" | 115.2 kbps |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Direção = "Enviar"    | 515.2 kbps |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Direção = "Receber" | 371.1 kbps |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Direção = "Enviar"    | 155.0 kbps |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Direção = "Receber" | 100.1 kbps |

Esta métrica pode responder a perguntas como "qual era o débito de rede para cada endereço IP?" e "a quantidade de dados foi enviado e recebido?" Métricas multidimensionais trazer o valor de análise e diagnóstico adicional em comparação comparado as métricas não dimensionais.

### <a name="value-of-metrics"></a>Valor de métricas
Métricas individuais normalmente fornecem pouco insight por conta própria. Eles fornecem um valor único sem qualquer contexto que não seja a comparação com um limiar simple. Eles são valiosos quando combinada com outras métricas para identificar padrões e tendências, ou quando combinado com os registos que fornecem o contexto em torno de valores específicos. 

Por exemplo, um determinado número de utilizadores na sua aplicação num determinado momento pode eventualmente indicar pouco sobre o estado de funcionamento da aplicação. Mas uma queda repentina de usuários, indicado por vários valores da mesma métrica, pode indicar um problema. Excessivas exceções emitidas pela aplicação e indicado por uma métrica separada, podem identificar um problema de aplicativo que está a causar o menu. Eventos que o aplicativo cria para identificar falhas em seus componentes podem ajudar a identificar a causa raiz.

### <a name="sources-of-metric-data"></a>Origens de dados de métrica
Existem três fontes fundamentais de métricas recolhidas pelo Azure Monitor. Todas estas métricas estão disponíveis no arquivo de métrico em que eles podem ser avaliados em conjunto, independentemente da respetiva origem.

**Métricas de plataforma** são criados por recursos do Azure e dar-lhe visibilidade para o respetivo estado de funcionamento e desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](../monitoring-and-diagnostics/monitoring-supported-metrics.md) sem qualquer configuração necessária. 

**Métricas da aplicação** são criados pelo Application Insights para as suas aplicações monitorizadas e ajudam a detetar problemas de desempenho e controle as tendências na forma como a aplicação está a ser utilizada. Isto inclui esses valores, conforme _tempo de resposta do servidor_ e _exceções do Browser_.

**Métricas personalizadas** são métricas que definir, além da métrica padrão que estão automaticamente disponíveis. Métricas personalizadas têm de ser criadas em relação a um único recurso na mesma região que esse recurso. Pode criar métricas personalizadas através dos seguintes métodos:
    - [Definir métricas personalizadas em seu aplicativo](../application-insights/app-insights-api-custom-events-metrics.md) que é monitorizado pelo Application Insights. Elas são, além do padrão definidas de métricas da aplicação.
    - Publicar métricas personalizadas das suas máquinas de virtuais do Windows usando [extensão de diagnóstico de Windows (WAD)](../monitoring-and-diagnostics/azure-diagnostics.md).
    - Publicar métricas personalizadas das suas máquinas de virtuais de Linux utilizar [InfluxData Telegraf agente](https://www.influxdata.com/time-series-platform/telegraf/).
    - Escreva métricas personalizadas a partir de um serviço do Azure com as API de métricas personalizadas.
    
![Descrição geral das métricas](media/monitoring-data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>O que pode fazer com a métrica?
Tarefas que pode realizar com métricas incluem o seguinte:

- Uso [Explorador de métricas](../monitoring-and-diagnostics/monitoring-metric-charts.md) para analisar métricas recolhidas e desenhá-los num gráfico. Controlar o desempenho de um recurso (por exemplo, uma VM, Web site ou aplicação lógica) ao afixar gráficos para uma [dashboard do Azure](../azure-portal/azure-portal-dashboards.md).
- Configurar uma [regra de alerta de métrica](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) que envia uma notificação ou demora [automatizada ação](../monitoring-and-diagnostics/monitoring-action-groups.md) quando a métrica ultrapassa um limiar.
- Uso [dimensionamento automático](../monitoring-and-diagnostics/monitoring-overview-autoscale.md) para aumentar ou diminuir recursos com base numa métrica cruzar um limiar.
- Métricas de rota para o Log Analytics para analisar dados de métricos com os dados de registo e para armazenar valores de métrica durante mais de 93 dias. 
- Stream métricas para uma [Hub de eventos](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) para encaminhar sejam [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) ou a sistemas externos.
- [Arquivo](../monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data.md) o histórico de desempenho ou o estado de funcionamento do seu recurso de conformidade, auditoria ou criação de relatórios offline.
- Aceda a valores de métrica de uma linha de comando ou um aplicativo personalizado usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) ou [REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Ver métricas
Métricas no Azure são recolhidas na base de dados de métricas do Azure Monitor. Esta é uma série de tempo otimizada para os valores de métrica rápidos de obtenção e arquivos de 93 dias de base de dados. Copie as métricas para o Log Analytics para análise de longo prazo e tendências.

Dados métricos são usados numa variedade de formas, conforme descrito acima. Uso [Explorador de métricas](../monitoring-and-diagnostics/monitoring-metric-charts.md) diretamente analisar os dados no arquivo de métrico e os valores de várias métricas do gráfico ao longo do tempo. Pode ver os gráficos interativamente ou afixá-los a um dashboard para visualizá-los com outras visualizações. Também pode obter métricas utilizando o [do Azure de monitorização de REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Explorador de Métricas](media/monitoring-data-collection/metrics-explorer.png)





## <a name="logs"></a>Registos
Os registos contêm diferentes tipos de dados organizados em registos com diferentes conjuntos de propriedades de cada tipo. Registos de podem conter valores numéricos, como as métricas, mas normalmente contêm dados de texto com descrições detalhadas. Eles ainda mais diferem entre as métricas em que elas variam em sua estrutura e, muitas vezes não são recolhidos em intervalos regulares.

Um tipo comum de entrada de log é um evento que são recolhidos esporadicamente. Eles são criados por uma aplicação ou serviço e, normalmente, incluem informações suficientes para fornecer contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso em particular foi criado ou modificado, um novo anfitrião iniciado em resposta ao aumento de tráfego, ou foi detetado um erro num aplicativo.

Os registos são especialmente úteis para combinar dados de várias origens, para análises complexas e para fins de tendência ao longo do tempo. Como o formato dos dados pode variar, os aplicativos podem criar registos personalizados usando a estrutura que eles precisam. As métricas são replicadas até mesmo nos registos para combiná-los com outros dados de monitorização para fins de tendência e outras análises de dados.



### <a name="log-analytics"></a>Log Analytics
Registos recolhidos pelo Monitor do Azure são armazenados no Log Analytics que recolhe telemetria e outros dados de várias origens. Ele fornece uma linguagem de consulta avançada e um mecanismo de análise que lhe dá informações sobre o funcionamento das suas aplicações e recursos. Other Azure services como [Centro de segurança do Azure](../security-center/security-center-intro.md) armazenam os dados no Log Analytics para fornecer uma plataforma de dados comuns em toda a gestão do Azure.

> [!IMPORTANT]
> Dados do Application Insights são armazenados no Log Analytics, como outros dados de registo, exceto que ela é armazenada numa partição separada. Isso oferece suporte a mesma funcionalidade que outros dados do Log Analytics, mas tem de utilizar o [consola do Application Insights](/application-insights/app-insights-analytics.md) ou o [API do Application Insights](https://dev.applicationinsights.io/) para aceder a estes dados. Pode utilizar um [consulta entre recursos](../log-analytics/log-analytics-cross-workspace-search.md) para analisar dados de aplicativos com outros dados de registo.


### <a name="sources-of-log-data"></a>Origens de dados de registo
Log Analytics pode recolher dados de várias origens, tanto no Azure e de recursos no local. Origens de dados escritos para o Log Analytics incluem o seguinte:

- [Registos de atividades](../log-analytics/log-analytics-activity.md) dos recursos do Azure que incluem informações sobre a respetiva configuração e o estado de funcionamento e [registos de diagnóstico](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) que fornecem informações sobre o funcionamento deles.
- Agentes nas [Windows](../log-analytics/log-analytics-windows-agent.md) e [Linux](../log-analytics/log-analytics-linux-agents.md) máquinas virtuais que enviar telemetria a partir do sistema operativo convidado e aplicativos para o Log Analytics de acordo com [origens de dados](../log-analytics/log-analytics-data-sources.md) que configurar.
- Aplicação dados recolhidos pelo [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Dados fornecem informações sobre uma determinada aplicação ou serviço a partir [soluções de monitorização](../monitoring/monitoring-solutions.md) ou funcionalidades, como informações de contentor, informações de VM ou informações de grupo de recursos.
- Dados de segurança recolhidos pelo [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/).
- [Métricas](#metrics) dos recursos do Azure. Isto permite-lhe para armazenar métricas durante mais de 93 dias e analisá-lo com outros dados de registo.
- Telemetria escrita [armazenamento do Azure](../log-analytics/log-analytics-azure-storage-iis-table.md).
- Dados personalizados de qualquer cliente de REST API utilizando o [HTTP Data Collector API](../log-analytics/log-analytics-data-collector-api.md) cliente ou a partir de um [aplicação lógica do Azure](https://docs.microsoft.com/azure/logic-apps/) fluxo de trabalho.

![Componentes do Log Analytics](media/monitoring-data-collection/logs-overview.png)




### <a name="what-can-you-do-with-logs"></a>O que pode fazer com os registos?
Tarefas que pode realizar com registos incluem o seguinte:

- Utilize o [página do Log Analytics](../log-analytics/query-language/get-started-analytics-portal.md) no portal do Azure para escrever consultas de análise de dados de registo.  Afixar os resultados renderizados como tabelas ou de gráficos para uma [dashboard do Azure](../azure-portal/azure-portal-dashboards.md).
- Configurar uma [regra de alerta de registo](../monitoring-and-diagnostics/alert-log.md) que envia uma notificação ou demora [automatizada ação](../monitoring-and-diagnostics/monitoring-action-groups.md) quando os resultados da consulta correspondem um resultado específico.
- Criar um fluxo de trabalho com base nos dados de utilização do Log Analytics [Logic Apps]().
- Exportar os resultados de uma consulta para [Power BI](../log-analytics/log-analytics-powerbi.md) utilizar visualizações diferentes e partilhar com utilizadores fora do Azure.
- Aceda a valores de métrica de uma linha de comando ou um aplicativo personalizado usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) ou [REST API](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Visualização de dados de registo
Todos os dados do Log Analytics é recuperado usando um [consulta de registo](../log-analytics/log-analytics-queries.md) que especifica um determinado conjunto de dados. As consultas são escritas utilizando o [linguagem de consulta do Log Analytics](../log-analytics/query-language/get-started-queries.md) que é uma linguagem de consulta avançada para obter, consolidar e analisar os dados recolhidos rapidamente. Utilize o [página do Log Analytics](../log-analytics/log-analytics-log-search-portals.md) no portal do Azure para analisar diretamente os dados na sua métrica armazenar e os valores de várias métricas do gráfico ao longo do tempo. Pode ver os gráficos interativamente ou afixá-los a um dashboard para visualizá-los com outras visualizações. Também pode obter métricas utilizando o [do Azure de monitorização de REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Registos](media/monitoring-data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Converter os dados de monitorização

### <a name="metrics-to-logs"></a>Métricas para os registos
Pode copiar as métricas ao Log Analytics para executar análises complexas com outros tipos de dados ao utilizar a sua linguagem de consulta avançada. Também pode manter os dados de registo por períodos mais longos do que de métricas, que lhe permitem realizar tendências ao longo do tempo. Quando as métricas ou outros dados de desempenho são armazenados no Log Analytics, que dados age como um registo. Utilize métricas para suportar perto de análise em tempo real e os alertas ao utilizar registos para fins de tendência e análise com outros dados.

Pode obter orientações para recolher métricas de recursos do Azure ao [registos do serviço de recolha do Azure e as métricas de utilização do Log Analytics](../log-analytics/log-analytics-azure-storage.md). Obtenha orientações para recolher métricas de recursos de recursos de PaaS do Azure no [configurar a recolha de métricas de recursos de PaaS do Azure com o Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Registos para métricas
Conforme descrito acima, as métricas estão mais responsivas do que os registos, para que possa criar alertas com menor latência e a um custo menor. O log Analytics recolhe uma quantidade significativa de dados numéricos que seriam adequado para as métricas, mas não estão armazenados na base de dados de métricas do Azure.  Um exemplo comum é que os dados de desempenho coletados por agentes e soluções de gestão. Alguns destes valores podem ser copiados para a base de dados de métricas, onde eles estão disponíveis para alertas e análise com o Explorador de métricas.

A explicação desta funcionalidade está disponível em [criar alertas de métrica para os registos no Azure Monitor](../monitoring-and-diagnostics/monitoring-metric-alerts-logs.md). A lista de valores de suporte está disponível em [suportado métricas com o Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Dados de Stream a sistemas externos
Além de usar as ferramentas no Azure para analisar dados de monitorização, pode ter um requisito reencaminhá-lo para uma ferramenta externa, como um produto de management (SIEM) de eventos e informações de segurança. Este encaminhamento é geralmente feito diretamente a partir de recursos monitorizados através de [os Hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/). 

Pode obter orientações para os diferentes tipos de dados em [Azure Stream a monitorização dos dados para um hub de eventos para consumo por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [monitorização dos dados disponíveis](monitoring-data-sources.md) diferentes recursos no Azure.