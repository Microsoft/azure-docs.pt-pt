---
title: Métricas em Azure Monitor | Microsoft Docs
description: Descreve métricas no Azure Monitor que são dados de monitorização leves capazes de suportar cenários em tempo real próximo.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/20/2021
ms.author: bwren
ms.openlocfilehash: 3c99002a4f8613ff40a116eeceded4b3bada1c15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936160"
---
# <a name="azure-monitor-metrics-overview"></a>Métricas do Monitor Azure
Azure Monitor Metrics é uma característica do Azure Monitor que recolhe dados numéricos de [recursos monitorizados](../monitor-reference.md) numa base de dados de séries temporizadas. As métricas são valores numéricos que são recolhidos a intervalos regulares e descrevem algum aspeto de um sistema num determinado momento. As métricas no Azure Monitor são leves e capazes de suportar cenários em tempo real, tornando-os particularmente úteis para alertar e detetar rapidamente problemas. Pode analisá-las interativamente com o explorador de métricas, ser notificada proativamente com um alerta quando um valor cruza um limiar, ou visualizá-los num livro ou num painel de instrumentos.


> [!NOTE]
> Azure Monitor Metrics é metade da plataforma de dados que suporta o Azure Monitor. O outro é [O Azure Monitor Logs](../logs/data-platform-logs.md) que recolhe e organiza dados de registo e desempenho e permite que seja analisado com uma linguagem de consulta rica. As métricas são mais leves do que os dados em Registos monitores do Azure e capazes de suportar cenários em tempo real tornando-os particularmente úteis para alertar e detetar rapidamente problemas. As métricas só podem armazenar dados numéricos numa determinada estrutura, enquanto os Logs podem armazenar uma variedade de diferentes tipos de dados cada um com a sua própria estrutura. Também pode realizar análises complexas em dados de Registos utilizando consultas de registo que não podem ser usadas para análise de dados de Métricas.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que se pode fazer com as Métricas do Monitor Azure?
A tabela que se segue lista as diferentes formas de utilização de Métricas no Monitor Azure.

|  | Description |
|:---|:---|
| **Analisar** | Use [o explorador de métricas](metrics-charts.md) para analisar métricas recolhidas num gráfico e comparar métricas de diferentes recursos. |
| **Alerta** | Configure uma [regra de alerta métrico](../alerts/alerts-metric.md) que envia uma notificação ou toma [medidas automatizadas](../alerts/action-groups.md) quando o valor métrico cruza um limiar. |
| **Visualizar** | Pin um gráfico de explorador de métricas para um [painel Azure](../app/tutorial-app-dashboards.md).<br>Crie um [livro para](../visualize/workbooks-overview.md) combinar com vários conjuntos de dados num relatório interativo. Exporte os resultados de uma consulta à [Grafana](../visualize/grafana-plugin.md) para alavancar o seu dashboarding e combinar com outras fontes de dados. |
| **Automatizar** |  Utilize [a Autoescala](../autoscale/autoscale-overview.md) para aumentar ou diminuir os recursos com base num valor métrico que cruza um limiar. |
| **Recuperar** | Aceder a valores métricos de uma linha de comando utilizando  [cmdlets PowerShell](/powershell/module/az.monitor)<br>Aceder a valores métricos de aplicação personalizada utilizando [API REST.](./rest-api-walkthrough.md)<br>Aceder a valores métricos de uma linha de comando utilizando  [OCLI](/cli/azure/monitor/metrics). |
| **Exportar** | [Rota métricas para Logs](./resource-logs.md#send-to-azure-storage) para analisar dados em Azure Monitor Metrics juntamente com dados em Registos monitores Azure e para armazenar valores métricos por mais de 93 dias.<br>Stream Metrics para um [Centro de Eventos](./stream-monitoring-data-event-hubs.md) para encaminhá-los para sistemas externos. |
| **Arquivo** | [Archive](./platform-logs-overview.md) o desempenho ou histórico de saúde do seu recurso para fins de conformidade, auditoria ou reporte offline. |

![Métricas panorâmicas](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Recolha de dados
Existem três fontes fundamentais de métricas recolhidas pelo Azure Monitor. Uma vez recolhidas estas métricas na base de dados métrica do Azure Monitor, podem ser avaliadas em conjunto, independentemente da sua origem.

**Recursos azuis.** As métricas da plataforma são criadas pelos recursos Azure e dão-lhe visibilidade para a sua saúde e desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](./metrics-supported.md) sem qualquer configuração necessária. As métricas da plataforma são recolhidas a partir de recursos Azure numa frequência de um minuto, salvo especificação em contrário na definição da métrica. 

**Aplicações**. As métricas são criadas pela Application Insights para as suas aplicações monitorizadas e ajudam-no a detetar problemas de desempenho e a acompanhar as tendências de utilização da sua aplicação. Isto inclui valores como _o tempo de resposta do Servidor_ e _exceções ao Navegador._

**Agentes de máquinas virtuais.** As métricas são recolhidas do sistema operativo convidado de uma máquina virtual. Ative as métricas de SO dos hóspedes para máquinas virtuais do Windows com [extensão de diagnóstico do Windows (WAD)](../agents/diagnostics-extension-overview.md) e para máquinas virtuais Linux com [Agente Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

**Métricas personalizadas.** Pode definir métricas para além das métricas padrão que estão automaticamente disponíveis. Pode [definir métricas personalizadas na sua aplicação](../app/api-custom-events-metrics.md) que são monitorizadas pela Application Insights ou criar métricas personalizadas para um serviço Azure utilizando as [métricas personalizadas API](./metrics-store-custom-rest-api.md).

- Ver [O que é monitorizado pelo Azure Monitor?](../monitor-reference.md)

## <a name="metrics-explorer"></a>Explorador de Métricas
Utilize [o Metrics Explorer](metrics-charts.md) para analisar interativamente os dados na sua base de dados métricas e mapear os valores de várias métricas ao longo do tempo. Pode fixar as tabelas num painel de instrumentos para os visualizar com outras visualizações. Também pode obter métricas utilizando a [API de monitorização Azure.](./rest-api-walkthrough.md)

![Explorador de Métricas](media/data-platform-metrics/metrics-explorer.png)

- Veja [como começar com o explorador de métricas Azure Monitor](./metrics-getting-started.md) para começar a usar o explorador de métricas.

## <a name="data-structure"></a>Estrutura de dados
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

| CarimboDeDataEHora     | Valor métrico |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1.331,8 Kbps |
| 8/9/2017 8:15 | 1.141,4 Kbps |
| 8/9/2017 8:16 | 1.110,2 Kbps |

Esta métrica não dimensional só pode responder a uma pergunta básica como "qual era a minha produção de rede num dado momento?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Produção de rede + duas dimensões ("IP" e "Direção")

| CarimboDeDataEHora     | Dimensão "IP"   | Dimensão "Direção" | Valor métrico|
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

## <a name="retention-of-metrics"></a>Retenção de Métricas
Para a maioria dos recursos em Azure, as métricas são armazenadas durante 93 dias. Existem algumas exceções:

**Métricas de SO convidados**
-   **Métricas clássicas de SO convidados.** Estes são contadores de desempenho recolhidos pela Extensão de Diagnóstico do [Windows (WAD)](../agents/diagnostics-extension-overview.md) ou pela [Extensão de Diagnóstico Linux (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) e encaminhados para uma conta de armazenamento Azure. A retenção para estas métricas é garantida de pelo menos 14 dias, embora não seja escrita nenhuma data de validade real na conta de armazenamento. Por razões de desempenho, o portal limita a quantidade de dados que exibe com base no volume. Portanto, o número real de dias recuperados pelo portal pode ser superior a 14 dias se o volume de dados que estão a ser escritos não for muito grande.  
-   **Métricas de SO de hóspedes enviadas para Azure Monitor Metrics**. Estes são contadores de desempenho recolhidos pela Extensão de Diagnóstico do [Windows (WAD)](../agents/diagnostics-extension-overview.md) e enviados para o [abacavador de dados do Monitor Azure](../agents/diagnostics-extension-overview.md#data-destinations), ou através do Agente [Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) em máquinas Linux. A retenção para estas métricas é de 93 dias.
-   **Métricas de SO de hóspedes recolhidas pelo agente Log Analytics.** Estes são contadores de desempenho recolhidos pelo agente Log Analytics e enviados para um espaço de trabalho Log Analytics. A retenção para estas métricas é de 31 dias, podendo ser estendida até 2 anos.

**Métricas baseadas em registos de insights de aplicação**. 
- Atrás da cena, [as métricas baseadas em registos traduzem-se](../app/pre-aggregated-metrics-log-metrics.md) em consultas de registo. A retenção corresponde à retenção dos eventos nos registos subjacentes. Para os recursos de Insights de Aplicação, os registos são armazenados durante 90 dias.


> [!NOTE]
> Pode [enviar métricas de plataforma para recursos do Azure Monitor para um espaço de trabalho Log Analytics](./resource-logs.md#send-to-azure-storage) para tendências de longo prazo.





## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [plataforma de dados do Azure Monitor.](../data-platform.md)
- Saiba mais [sobre os dados de registo no Azure Monitor](../logs/data-platform-logs.md).
- Conheça os [dados de monitorização disponíveis](../agents/data-sources.md) para diferentes recursos em Azure.