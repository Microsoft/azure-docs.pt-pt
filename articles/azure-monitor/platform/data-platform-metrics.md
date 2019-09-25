---
title: Métricas no Azure Monitor | Microsoft Docs
description: Descreve as métricas em Azure Monitor que são dados de monitoramento leves capazes de dar suporte a cenários quase em tempo real.
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
ms.openlocfilehash: e534754e46e6f2ad9b99b67d24d9f7da63a51a4f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258370"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados Azure Monitor baseia-se em dois tipos de dados fundamentais: Métricas e logs. Este artigo descreve as métricas. Consulte [os logs em Azure monitor](data-platform-logs.md) para obter uma descrição detalhada dos Logs e [Azure monitor plataforma de dados](data-platform.md) para uma comparação dos dois.

As métricas no Azure Monitor são leves e capazes de dar suporte a cenários quase em tempo real, tornando-os particularmente úteis para alertas e detecção rápida de problemas. Este artigo descreve como as métricas são estruturadas, o que você pode fazer com elas e identifica diferentes fontes de dados que armazenam dados em métricas.

## <a name="what-are-metrics"></a>O que são métricas?
As métricas são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. As métricas são coletadas em intervalos regulares e são úteis para alertas porque podem ser amostradas com frequência e um alerta pode ser acionado rapidamente com uma lógica relativamente simples.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que você pode fazer com Azure Monitor métricas?
A tabela a seguir lista as diferentes maneiras que você pode usar dados de métrica em Azure Monitor.

|  |  |
|:---|:---|
| Analisar | Use o [Metrics Explorer](metrics-charts.md) para analisar as métricas coletadas em um gráfico e comparar as métricas de recursos diferentes. |
| Visualizar | Fixe um gráfico do Metrics Explorer em um [painel do Azure](../learn/tutorial-app-dashboards.md).<br>Crie uma [pasta de trabalho](../app/usage-workbooks.md) para combinar com vários conjuntos de dados em um relatório interativo. Exporte os resultados de uma consulta para [Grafana](grafana-plugin.md) para aproveitar seu painel e combinar com outras fontes de dados. |
| Alerta | Configurar uma [regra de alerta de métrica](alerts-metric.md) que envia uma notificação ou executa uma [ação automatizada](action-groups.md) quando o valor da métrica ultrapassa um limite. |
| Automatizar |  Use o [dimensionamento automático](autoscale-overview.md) para aumentar ou diminuir os recursos com base em um valor de métrica que ultrapassa um limite. |
| Exportar | [Direcione métricas para logs](resource-logs-collect-storage.md) para analisar dados em Azure monitor métricas junto com dados em logs de Azure monitor e para armazenar valores de métrica por mais de 93 dias.<br>Transmita métricas para um [Hub de eventos](stream-monitoring-data-event-hubs.md) para encaminhá-las a sistemas externos. |
| Recuperado | Acessar valores de métrica de uma linha de comando usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Acessar valores de métrica do aplicativo personalizado usando a [API REST](rest-api-walkthrough.md).<br>Acessar valores de métrica de uma linha de comando usando a [CLI](/cli/azure/monitor/metrics). |
| Arquivo | [Arquivo](..//learn/tutorial-archive-data.md) o histórico de desempenho ou o estado de funcionamento do seu recurso de conformidade, auditoria ou criação de relatórios offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Como os dados no Azure Monitor métricas são estruturados?
Os dados coletados por métricas de Azure Monitor são armazenados em um banco de dados de série temporal que é otimizado para analisar o data com carimbo de hora. Cada conjunto de valores de métrica é uma série temporal com as seguintes propriedades:

* A hora em que o valor foi coletado
* O recurso ao qual o valor está associado
* Um namespace que funciona como uma categoria para a métrica
* Um nome de métrica
* O próprio valor
* Algumas métricas podem ter várias dimensões, conforme descrito em [métricas multidimensionais](#multi-dimensional-metrics). Métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios dos dados de métrica é que ele geralmente tem informações limitadas para fornecer contexto para valores coletados. Azure Monitor resolve esse desafio com métricas multidimensionais. As dimensões de uma métrica são pares nome-valor que transportar dados adicionais para descrever o valor de métrica. Por exemplo, um _espaço em disco disponível_ de métrica poderia ter uma dimensão chamada _drive_ com os valores _C:_ , _D:_ , que permitiria exibir o espaço em disco disponível em todas as unidades ou em cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados para uma métrica hipotética chamada _débito de rede_. O primeiro conjunto de dados não tem dimensões. O segundo conjunto de um mostra os valores com duas dimensões, o _endereço IP_ e a _direção_:

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

## <a name="interacting-with-azure-monitor-metrics"></a>Interagindo com métricas de Azure Monitor
Use [Metrics Explorer](metrics-charts.md) para analisar interativamente os dados em seu banco de dado de métrica e o gráfico dos valores de várias métricas ao longo do tempo. Você pode fixar os gráficos em um painel para exibi-los com outras visualizações. Também pode obter métricas utilizando o [do Azure de monitorização de REST API](rest-api-walkthrough.md).

![Explorador de Métricas](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Fontes de métricas de Azure Monitor
Existem três fontes fundamentais de métricas recolhidas pelo Azure Monitor. Depois que essas métricas são coletadas no banco de dados de métrica Azure Monitor, elas podem ser avaliadas juntas, independentemente da origem.

**Métricas de plataforma** são criados por recursos do Azure e dar-lhe visibilidade para o respetivo estado de funcionamento e desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem qualquer configuração necessária. As métricas de plataforma são coletadas dos recursos do Azure em uma frequência de um minuto, a menos que especificado de outra forma na definição da métrica. 

As **métricas do SO convidado** são coletadas do sistema operacional convidado de uma máquina virtual. Habilite as métricas do SO convidado para máquinas virtuais do Windows com a [extensão de diagnóstico do Windows (wad)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais do Linux com o [agente Telegraf do InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

**Métricas da aplicação** são criados pelo Application Insights para as suas aplicações monitorizadas e ajudam a detetar problemas de desempenho e controle as tendências na forma como a aplicação está a ser utilizada. Isto inclui esses valores, conforme _tempo de resposta do servidor_ e _exceções do Browser_.

**Métricas personalizadas** são métricas que você define, além das métricas padrão que estão disponíveis automaticamente. Você pode [definir métricas personalizadas em seu aplicativo](../app/api-custom-events-metrics.md) monitorado por Application insights ou criar métricas personalizadas para um serviço do Azure usando a [API de métricas personalizadas](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de métricas
Para a maioria dos recursos no Azure, as métricas são armazenadas por 93 dias. Há algumas exceções:

**Métricas do SO convidado**
-   **Métricas do sistema operacional convidado clássico**. Esses são contadores de desempenho coletados pela [extensão de diagnóstico do Windows (wad)](../platform/diagnostics-extension-overview.md) ou pela [Lad (extensão de diagnóstico do Linux)](../../virtual-machines/extensions/diagnostics-linux.md) e roteados para uma conta de armazenamento do Azure. A retenção para essas métricas é de 14 dias.
-   **Métricas do SO convidado enviadas para Azure monitor métricas**. Esses são contadores de desempenho coletados pela extensão de diagnóstico do Windows (WAD) e enviam para o [coletor de Azure monitor](diagnostics-extension-overview.md#data-storage), ou por meio do [agente Telegraf do InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) em computadores Linux. A retenção para essas métricas é de 93 dias.
-   **Métricas do SO convidado coletadas pelo agente de log Analytics**. Esses são contadores de desempenho coletados pelo agente de Log Analytics e enviados a um espaço de trabalho do Log Analytics. A retenção para essas métricas é de 31 dias e pode ser estendida até 2 anos.

**Application insights métricas baseadas em log**. 
- Por trás da cena, as [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md) são transvertidas em consultas de log. Sua retenção corresponde à retenção de eventos em logs subjacentes. Para Application Insights recursos, os logs são armazenados por 90 dias.


> [!NOTE]
> Você pode [Enviar métricas de plataforma para Azure monitor recursos para um espaço de trabalho log Analytics](resource-logs-collect-storage.md) para tendência a longo prazo.





## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [plataforma de dados Azure monitor](data-platform.md).
- Saiba mais sobre [os dados de log em Azure monitor](data-platform-logs.md).
- Saiba mais sobre o [monitorização dos dados disponíveis](data-sources.md) diferentes recursos no Azure.
