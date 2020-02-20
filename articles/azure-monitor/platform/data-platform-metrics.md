---
title: Métricas no Monitor Azure / Microsoft Docs
description: Descreve métricas no Monitor Azure que são dados de monitorização leves capazes de suportar cenários próximos em tempo real.
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
ms.openlocfilehash: cd30803735c5453c286788b8669a3d2f02c418a5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468053"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados Azure Monitor baseia-se em dois tipos fundamentais de dados: Métricas e Registos. Este artigo descreve métricas. Consulte os [Registos no Monitor Azure](data-platform-logs.md) para obter uma descrição detalhada dos registos e da plataforma de [dados Do Monitor Azure](data-platform.md) para uma comparação entre os dois.

As métricas do Monitor Azure são leves e capazes de suportar cenários próximos em tempo real, tornando-os particularmente úteis para alertar e detetar rapidamente problemas. Este artigo descreve como as métricas são estruturadas, o que pode fazer com elas, e identifica diferentes fontes de dados que armazenam dados em métricas.

## <a name="what-are-metrics"></a>O que são métricas?
As métricas são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. As métricas são recolhidas em intervalos regulares e são úteis para alertar porque podem ser amostradas com frequência, e um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que se pode fazer com as Métricas do Monitor Azure?
A tabela seguinte enumera as diferentes formas de utilizar dados métricos no Monitor Azure.

|  |  |
|:---|:---|
| Analisar | Use o explorador de [métricas](metrics-charts.md) para analisar métricas recolhidas num gráfico e comparar métricas de diferentes recursos. |
| Visualizar | Pin um gráfico do explorador de métricas para um [painel de instrumentos Azure.](../learn/tutorial-app-dashboards.md)<br>Crie um [livro](../app/usage-workbooks.md) para combinar com vários conjuntos de dados num relatório interativo. Exporte os resultados de uma consulta à [Grafana](grafana-plugin.md) para alavancar o seu dashboard e combinar com outras fontes de dados. |
| Alerta | Configure uma regra de [alerta métrico](alerts-metric.md) que envie uma notificação ou tome [medidas automatizadas](action-groups.md) quando o valor métrico cruza um limiar. |
| Automatizar |  Utilize a [escala automática](autoscale-overview.md) para aumentar ou diminuir os recursos com base num valor métrico que cruze um limiar. |
| Exportar | [Route Metrics to Logs](resource-logs-collect-storage.md) para analisar dados em Métricas de Monitor Azure juntamente com dados em Registos de Monitor estoque e para armazenar valores métricos por mais de 93 dias.<br>Stream Metrics para um Hub de [Eventos](stream-monitoring-data-event-hubs.md) para encaminhá-los para sistemas externos. |
| Recuperar | Valores métricos de acesso a partir de uma linha de comando usando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Aceder a valores métricos a partir de aplicação personalizada utilizando [a REST API](rest-api-walkthrough.md).<br>Valores métricos de acesso a partir de uma linha de comando utilizando [CLI](/cli/azure/monitor/metrics). |
| Arquivo | [Arquivar](..//learn/tutorial-archive-data.md) o desempenho ou histórico de saúde do seu recurso para efeitos de conformidade, auditoria ou reporte offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Como é estruturado os dados em Métricas de Monitor Azure?
Os dados recolhidos pela Azure Monitor Metrics são armazenados numa base de dados da série temporal que é otimizada para analisar dados carimbados no tempo. Cada conjunto de valores métricos é uma série temporal com as seguintes propriedades:

* O tempo que o valor foi recolhido
* O recurso a que o valor está associado
* Um espaço de nome que age como uma categoria para a métrica
* Um nome métrico
* O valor em si
* Algumas métricas podem ter múltiplas dimensões, conforme descrito em [métricas multidimensionais.](#multi-dimensional-metrics) Métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios para os dados métricos é que muitas vezes tem informação limitada para fornecer contexto para valores recolhidos. O Monitor Azure aborda este desafio com métricas multidimensionais. As dimensões de uma métrica são pares nome-valor que transportar dados adicionais para descrever o valor de métrica. Por exemplo, um espaço de _disco disponível_ métrico poderia ter uma dimensão chamada _Drive_ com valores _C:_ , _D:_ , que permitiria visualizar o espaço do disco disponível em todas as unidades ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados para uma métrica hipotética chamada _"Network Throughput"._ O primeiro conjunto de dados não tem dimensões. O segundo conjunto de dados mostra os valores com duas dimensões, _endereço IP_ e _direção:_

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

## <a name="interacting-with-azure-monitor-metrics"></a>Interagindo com métricas de monitor azure
Use [o Metrics Explorer](metrics-charts.md) para analisar interativamente os dados na sua base de dados métrica e traçar os valores de várias métricas ao longo do tempo. Pode fixar as tabelas num painel de instrumentos para vê-las com outras visualizações. Também pode recuperar métricas utilizando a API de [monitorização Azure](rest-api-walkthrough.md).

![Explorador de Métricas](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Fontes de Métricas de Monitor Azure
Existem três fontes fundamentais de métricas recolhidas pelo Azure Monitor. Uma vez recolhidas estas métricas na base de dados métrica do Monitor Azure, podem ser avaliadas em conjunto independentemente da sua origem.

**As métricas** da plataforma são criadas pelos recursos do Azure e dão-lhe visibilidade para a sua saúde e desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem qualquer configuração necessária. As métricas da plataforma são recolhidas a partir de recursos Azure com uma frequência de um minuto, salvo especificação em contrário na definição da métrica. 

**As métricas de SO** dos hóspedes são recolhidas do sistema operativo convidado de uma máquina virtual. Ative as métricas de OS dos hóspedes para máquinas virtuais windows com extensão de [diagnóstico do Windows (WAD)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais Linux com [O Agente Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

**As métricas** de aplicação são criadas pela Application Insights para as suas aplicações monitorizadas e ajudam-no a detetar problemas de desempenho e a acompanhar as tendências na forma como a sua aplicação está a ser utilizada. Isto inclui valores como o tempo de _resposta do Servidor_ e exceções ao _Navegador._

**As métricas personalizadas** são métricas que define para além das métricas padrão que estão automaticamente disponíveis. Pode [definir métricas personalizadas na sua aplicação](../app/api-custom-events-metrics.md) que são monitorizadas por Application Insights ou criar métricas personalizadas para um serviço Azure utilizando as [métricas personalizadas API](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de Métricas
Para a maioria dos recursos em Azure, as métricas são armazenadas durante 93 dias. Há algumas exceções:

**Métricas de Os Convidados**
-   **Métricas clássicas de soei.** Estes são contadores de desempenho recolhidos pela Extensão de [Diagnóstico do Windows (WAD)](../platform/diagnostics-extension-overview.md) ou pela Extensão de [Diagnóstico Linux (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) e encaminhados para uma conta de armazenamento Azure. A retenção para estas métricas é de 14 dias.
-   **Métricas de SO convidadaenviadas para As Métricas do Monitor Azure.** Estes são contadores de desempenho recolhidos pela Extensão de [Diagnóstico do Windows (WAD)](diagnostics-extension-overview.md) e enviados para o lavatório de [dados do Monitor Azure,](diagnostics-extension-overview.md#data-destinations)ou através do [Agente Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) nas máquinas Linux. A retenção para estas métricas é de 93 dias.
-   **Métricas de OS do hóspede recolhidas pelo agente Log Analytics**. Estes são contadores de desempenho recolhidos pelo agente Log Analytics e enviados para um espaço de trabalho de Log Analytics. A retenção para estas métricas é de 31 dias, podendo ser prolongada até 2 anos.

**Aplicação Insights métricas baseadas em log.** 
- Por detrás da cena, [as métricas baseadas em registo traduzem-se](../app/pre-aggregated-metrics-log-metrics.md) em consultas de registo. A sua retenção corresponde à retenção de eventos em registos subjacentes. Para recursos de Informação de Aplicação, os registos são armazenados durante 90 dias.


> [!NOTE]
> Pode [enviar métricas de plataforma para os recursos do Azure Monitor para um espaço](resource-logs-collect-storage.md) de trabalho de Log Analytics para tendências de longo prazo.





## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a plataforma de [dados Azure Monitor.](data-platform.md)
- Saiba mais sobre os dados de [log no Monitor Azure.](data-platform-logs.md)
- Conheça os [dados de monitorização disponíveis](data-sources.md) para diferentes recursos no Azure.
