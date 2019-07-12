---
title: Métricas no Azure Monitor | Documentos da Microsoft
description: Descreve as métricas no Azure Monitor, que são simples de dados com capacidade de oferecer suporte a cenários em tempo real em tempo quase de monitorização.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 684491b546a0456d936ae199cdfb93180aa05043
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607031"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados do Azure Monitor baseia-se em dois tipos de dados fundamentais: Métricas e registos. Este artigo descreve as métricas. Consulte a [inicia sessão no Azure Monitor](data-platform-logs.md) para obter uma descrição detalhada dos registos e a [plataforma de dados do Azure Monitor](data-platform.md) para obter uma comparação dos dois.

Métricas no Azure Monitor são leves e capaz de suportar perto de cenários em tempo real, tornando-os particularmente útil para deteção de alerta e rápida de problemas. Este artigo descreve como as métricas são estruturadas, o que pode fazer com eles e identifica as origens de dados diferentes que armazenam dados em métricas.

## <a name="what-are-metrics"></a>Quais são as métricas?
As métricas são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. As métricas são recolhidas em intervalos regulares e são úteis para alertar porque eles podem ser convertidos com frequência, e um alerta pode ser acionado rapidamente com a lógica relativamente simples.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que pode fazer com a métrica de Monitor do Azure?
A tabela seguinte lista as diferentes formas que pode utilizar dados de métrica no Azure Monitor.

|  |  |
|:---|:---|
| Analisar | Uso [Explorador de métricas](metrics-charts.md) para analisar métricas recolhidas num gráfico e comparar as métricas de recursos diferentes. |
| Visualizar | Fixar um gráfico a partir do Explorador de métricas para uma [dashboard do Azure](../learn/tutorial-app-dashboards.md).<br>Criar uma [livro](../app/usage-workbooks.md) combinar com vários conjuntos de dados num relatório interativo. Exportar os resultados de uma consulta para [Grafana](grafana-plugin.md) para tirar partido das respetivas vistas e combine com outras origens de dados. |
| Alerta | Configurar uma [regra de alerta de métrica](alerts-metric.md) que envia uma notificação ou demora [automatizada ação](action-groups.md) quando o valor da métrica ultrapassar um limiar. |
| Automatizar |  Uso [dimensionamento automático](autoscale-overview.md) para aumentar ou diminuir recursos com base num valor métrico cruzar um limiar. |
| Exportar | [Encaminhar métricas para os registos](diagnostic-logs-stream-log-store.md) para analisar dados no Azure Monitor métricas juntamente com dados nos Logs de Monitor do Azure e para armazenar valores de métrica durante mais de 93 dias.<br>Stream métricas para uma [Hub de eventos](stream-monitoring-data-event-hubs.md) para encaminhá-las a sistemas externos. |
| Obter | Aceda a valores de métrica de uma linha de comandos com [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Aceda a valores de métrica de aplicativo personalizado usando [REST API](rest-api-walkthrough.md).<br>Aceda a valores de métrica de uma linha de comandos, utilizando [CLI](/cli/azure/monitor/metrics). |
| Arquivo | [Arquivo](..//learn/tutorial-archive-data.md) o histórico de desempenho ou o estado de funcionamento do seu recurso de conformidade, auditoria ou criação de relatórios offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>É como os dados no Azure, monitorizar as métricas estruturado?
Dados recolhidos pelo Azure monitorizar as métricas são armazenados num banco de dados de séries de tempo que está otimizado para analisar os dados de horários. Cada conjunto de valores de métrica é uma série de tempo com as seguintes propriedades:

* O tempo que o valor foi recolhido
* O recurso o valor está associado
* Um espaço de nomes que funciona como uma categoria para a métrica
* Um nome de métrica
* O valor em si
* Algumas métricas podem ter várias dimensões, conforme descrito em [métricas multidimensionais](#multi-dimensional-metrics). Métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios para dados de métrica é que ela muitas vezes tem informações limitadas para fornecer contexto para valores recolhidos. O Azure Monitor resolve esse desafio com métricas multidimensionais. As dimensões de uma métrica são pares nome-valor que transportar dados adicionais para descrever o valor de métrica. Por exemplo, uma métrica _espaço em disco disponível_ poderia ter uma dimensão chamada _unidade_ com valores _c:_ , _D:_ , que permitiria a visualização o espaço em disco disponível em todas as unidades de ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados para uma métrica hipotética chamada _débito de rede_. O primeiro conjunto de dados não tem dimensões. O segundo conjunto de dados mostra os valores com duas dimensões, _endereço IP_ e _direção_:

### <a name="network-throughput"></a>Débito de rede

| Carimbo de data/hora     | Valor de métrica |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kbps |
| 8/9/2017 8:15 | 1,141.4 kbps |
| 8/9/2017 8:16 | 1,110.2 kbps |

Esta métrica não dimensionais pode apenas resposta uma pergunta básica, como "o que era minha débito de rede num determinado momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Débito de rede + duas dimensões ("IP" e "Direção")

| Carimbo de data/hora     | Dimensão "IP"   | Dimensão de "Direção" | Valor de métrica|
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

## <a name="interacting-with-azure-monitor-metrics"></a>Interagir com a métrica de Monitor do Azure
Uso [Explorador de métricas](metrics-charts.md) interativamente analisar os dados na base de dados de métrica e os valores de várias métricas do gráfico ao longo do tempo. Pode fixar os gráficos para um dashboard para visualizá-los com outras visualizações. Também pode obter métricas utilizando o [do Azure de monitorização de REST API](rest-api-walkthrough.md).

![Explorador de Métricas](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Origens de métricas de Monitor do Azure
Existem três fontes fundamentais de métricas recolhidas pelo Azure Monitor. Depois destas métricas são recolhidas da base de dados de métrica do Azure Monitor, eles podem ser avaliados em conjunto, independentemente da respetiva origem.

**Métricas de plataforma** são criados por recursos do Azure e dar-lhe visibilidade para o respetivo estado de funcionamento e desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem qualquer configuração necessária. Métricas de plataforma são recolhidas a partir de recursos do Azure, a frequência de um minuto, salvo especificação em contrário na definição da métrica. 

**Métricas de SO convidado** são recolhidos a partir do sistema operativo convidado de uma máquina virtual. Ativar as métricas de SO convidado para máquinas de virtuais do Windows com [extensão de diagnóstico de Windows (WAD)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais do Linux com [InfluxData Telegraf agente](https://www.influxdata.com/time-series-platform/telegraf/).

**Métricas da aplicação** são criados pelo Application Insights para as suas aplicações monitorizadas e ajudam a detetar problemas de desempenho e controle as tendências na forma como a aplicação está a ser utilizada. Isto inclui esses valores, conforme _tempo de resposta do servidor_ e _exceções do Browser_.

**Métricas personalizadas** são métricas que definir, além das métricas padrão que estão automaticamente disponíveis. Pode [definir métricas personalizadas em seu aplicativo](../app/api-custom-events-metrics.md) que é monitorizado pelo Application Insights ou criar métricas personalizadas para um serviço do Azure utilizando o [API de métricas personalizado](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de métricas
Para a maioria dos recursos no Azure, as métricas são armazenadas durante dias 93. Existem algumas exceções:
  * **Métricas de SO convidado clássicas**. Métricas de SO convidado clássico são retidas durante 14 dias. Para retenção mais longa, recomendamos que utilize novas métricas de SO convidado que são recolhidas com [extensão de diagnóstico de Windows (WAD)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais do Linux com [InfluxData Telegraf agente](https://www.influxdata.com/time-series-platform/telegraf/).
  * **Métricas do Application Insights com base em log**. Nos bastidores, [as métricas baseadas no registo](../app/pre-aggregated-metrics-log-metrics.md) se convertem em consultas de registo. Sua retenção corresponde a retenção de eventos nos registos subjacentes. Para recursos do Application Insights, os registos são armazenados durante 90 dias. 

> [!NOTE]
> Pode [enviar métricas de plataforma para os recursos do Azure Monitor para uma área de trabalho do Log Analytics](diagnostic-logs-stream-log-store.md) para fins de tendência de longo prazo.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre [registos de dados no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre o [monitorização dos dados disponíveis](data-sources.md) diferentes recursos no Azure.
