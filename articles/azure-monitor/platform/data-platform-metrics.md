---
title: Métricas em Monitor Azure / Microsoft Docs
description: Descreve métricas no Azure Monitor que são dados de monitorização leves capazes de suportar cenários em tempo real próximo.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: b05007e2ea7815afbba2a7a71368686cf7c049fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87325615"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados Azure Monitor baseia-se em dois tipos fundamentais de dados: Métricas e Registos. Este artigo descreve métricas. Consulte [os Registos no Monitor Azure](data-platform-logs.md) para obter uma descrição detalhada dos registos e da plataforma de [dados do Azure Monitor](data-platform.md) para uma comparação dos dois.

As métricas no Azure Monitor são leves e capazes de suportar cenários em tempo real, tornando-os particularmente úteis para alertar e detetar rapidamente problemas. Este artigo descreve como as métricas são estruturadas, o que pode fazer com elas, e identifica diferentes fontes de dados que armazenam dados em métricas.

## <a name="what-are-metrics"></a>O que são métricas?
As métricas são valores numéricos que descrevem alguns aspetos de um sistema num determinado momento. As métricas são recolhidas em intervalos regulares e são úteis para alertar porque podem ser amostradas frequentemente, e um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que se pode fazer com as Métricas do Monitor Azure?
A tabela que se segue lista as diferentes formas de utilizar dados métricos no Azure Monitor.

|  | Descrição |
|:---|:---|
| **Análise** | Use [o explorador de métricas](metrics-charts.md) para analisar métricas recolhidas num gráfico e comparar métricas de diferentes recursos. |
| **Visualização** | Pin um gráfico de explorador de métricas para um [painel Azure](../learn/tutorial-app-dashboards.md).<br>Crie um [livro para](./workbooks-overview.md) combinar com vários conjuntos de dados num relatório interativo. Exporte os resultados de uma consulta à [Grafana](grafana-plugin.md) para alavancar o seu dashboarding e combinar com outras fontes de dados. |
| **Alerta** | Configure uma [regra de alerta métrico](alerts-metric.md) que envia uma notificação ou toma [medidas automatizadas](action-groups.md) quando o valor métrico cruza um limiar. |
| **Automatizar** |  Utilize [a Autoescala](autoscale-overview.md) para aumentar ou diminuir os recursos com base num valor métrico que cruza um limiar. |
| **Exportar** | [Rota métricas para Logs](./resource-logs.md#send-to-azure-storage) para analisar dados em Azure Monitor Metrics juntamente com dados em Registos monitores Azure e para armazenar valores métricos por mais de 93 dias.<br>Stream Metrics para um [Centro de Eventos](stream-monitoring-data-event-hubs.md) para encaminhá-los para sistemas externos. |
| **Recuperar** | Aceder a valores métricos de uma linha de comando utilizando  [cmdlets PowerShell](/powershell/module/az.applicationinsights)<br>Aceder a valores métricos de aplicação personalizada utilizando [API REST.](rest-api-walkthrough.md)<br>Aceder a valores métricos de uma linha de comando utilizando  [OCLI](/cli/azure/monitor/metrics). |
| **Arquivo** | [Archive](./platform-logs-overview.md) o desempenho ou histórico de saúde do seu recurso para fins de conformidade, auditoria ou reporte offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Como são estruturados os dados em Azure Monitor Metrics?
Os dados recolhidos pela Azure Monitor Metrics são armazenados numa base de dados de séries temporais que é otimizada para analisar dados com carimbo temporal. Cada conjunto de valores métricos é uma série de tempo com as seguintes propriedades:

* O tempo que o valor foi recolhido
* O recurso a que o valor está associado
* Um espaço de nome que funciona como uma categoria para a métrica
* Um nome métrico
* O valor em si
* Algumas métricas podem ter múltiplas dimensões, como descrito em [métricas multidimensionais.](#multi-dimensional-metrics) As métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios para os dados métricos é que muitas vezes tem informação limitada para fornecer contexto para valores recolhidos. O Azure Monitor aborda este desafio com métricas multidimensionais. Dimensões de uma métrica são pares de valor-nome que transportam dados adicionais para descrever o valor métrico. Por exemplo, um _espaço de disco disponível métrico_ poderia ter uma dimensão chamada _Drive_ com valores _C:_ _, D:_, que permitiria visualizar o espaço disponível do disco em todas as unidades ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados para uma métrica hipotética chamada _Network Throughput_. O primeiro conjunto de dados não tem dimensões. O segundo conjunto de dados mostra os valores com duas dimensões, _endereço IP_ e _Direção:_

### <a name="network-throughput"></a>Produção de Rede

| Timestamp     | Valor métrico |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1.331,8 Kbps |
| 8/9/2017 8:15 | 1.141,4 Kbps |
| 8/9/2017 8:16 | 1.110,2 Kbps |

Esta métrica não dimensional só pode responder a uma pergunta básica como "qual era a minha produção de rede num dado momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Produção de rede + duas dimensões ("IP" e "Direção")

| Timestamp     | Dimensão "IP"   | Dimensão "Direção" | Valor métrico|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direção="Enviar"    | 646,5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direção="Receber" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direção="Enviar"    | 150,0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direção="Receber" | 115,2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direção="Enviar"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direção="Receber" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direção="Enviar"    | 155,0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direção="Receber" | 100.1 Kbps |

Esta métrica pode responder a questões como "qual era o rendimento da rede para cada endereço IP?", e "quanto dados foram enviados versus recebidos?" As métricas multidimensionais têm valor analítico e diagnóstico adicional em comparação com métricas não dimensionais.

## <a name="interacting-with-azure-monitor-metrics"></a>Interagir com Métricas do Azure Monitor
Utilize [o Metrics Explorer](metrics-charts.md) para analisar interativamente os dados na sua base de dados métricas e mapear os valores de várias métricas ao longo do tempo. Pode fixar as tabelas num painel de instrumentos para os visualizar com outras visualizações. Também pode obter métricas utilizando a [API de monitorização Azure.](rest-api-walkthrough.md)

![Explorador de Métricas](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Fontes de Métricas do Monitor Azure
Existem três fontes fundamentais de métricas recolhidas pelo Azure Monitor. Uma vez recolhidas estas métricas na base de dados métrica do Azure Monitor, podem ser avaliadas em conjunto, independentemente da sua origem.

**As métricas da plataforma** são criadas pelos recursos Azure e dão-lhe visibilidade para a sua saúde e desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem qualquer configuração necessária. As métricas da plataforma são recolhidas a partir de recursos Azure numa frequência de um minuto, salvo especificação em contrário na definição da métrica. 

**As métricas de SO do hóspede** são recolhidas a partir do sistema operativo convidado de uma máquina virtual. Ative as métricas de SO dos hóspedes para máquinas virtuais do Windows com [extensão de diagnóstico do Windows (WAD)](./diagnostics-extension-overview.md) e para máquinas virtuais Linux com [Agente Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

**As métricas de aplicação** são criadas pela Application Insights para as suas aplicações monitorizadas e ajudam-no a detetar problemas de desempenho e a acompanhar as tendências de utilização da sua aplicação. Isto inclui valores como _o tempo de resposta do Servidor_ e _exceções ao Navegador._

**As métricas personalizadas** são métricas que define para além das métricas padrão que estão automaticamente disponíveis. Pode [definir métricas personalizadas na sua aplicação](../app/api-custom-events-metrics.md) que são monitorizadas pela Application Insights ou criar métricas personalizadas para um serviço Azure utilizando as [métricas personalizadas API](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de Métricas
Para a maioria dos recursos em Azure, as métricas são armazenadas durante 93 dias. Existem algumas exceções:

**Métricas de SO convidados**
-   **Métricas clássicas de SO convidados.** Estes são contadores de desempenho recolhidos pela Extensão de Diagnóstico do [Windows (WAD)](./diagnostics-extension-overview.md) ou pela [Extensão de Diagnóstico Linux (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) e encaminhados para uma conta de armazenamento Azure. A retenção para estas métricas é de 14 dias.
-   **Métricas de SO de hóspedes enviadas para Azure Monitor Metrics**. Estes são contadores de desempenho recolhidos pela Extensão de Diagnóstico do [Windows (WAD)](diagnostics-extension-overview.md) e enviados para o [abacavador de dados do Monitor Azure](diagnostics-extension-overview.md#data-destinations), ou através do Agente [Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) em máquinas Linux. A retenção para estas métricas é de 93 dias.
-   **Métricas de SO de hóspedes recolhidas pelo agente Log Analytics.** Estes são contadores de desempenho recolhidos pelo agente Log Analytics e enviados para um espaço de trabalho Log Analytics. A retenção para estas métricas é de 31 dias, podendo ser estendida até 2 anos.

**Métricas baseadas em registos de insights de aplicação**. 
- Atrás da cena, [as métricas baseadas em registos traduzem-se](../app/pre-aggregated-metrics-log-metrics.md) em consultas de registo. A sua retenção coincide com a retenção de eventos em troncos subjacentes. Para os recursos de Insights de Aplicação, os registos são armazenados durante 90 dias.


> [!NOTE]
> Pode [enviar métricas de plataforma para recursos do Azure Monitor para um espaço de trabalho Log Analytics](./resource-logs.md#send-to-azure-storage) para tendências de longo prazo.





## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [plataforma de dados do Azure Monitor.](data-platform.md)
- Saiba mais [sobre os dados de registo no Azure Monitor](data-platform-logs.md).
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos em Azure.

