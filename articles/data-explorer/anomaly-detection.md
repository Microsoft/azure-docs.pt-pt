---
title: Deteção de anomalias de série de tempo e previsão no Explorador de dados do Azure
description: Saiba como analisar dados de séries de tempo para a deteção de anomalias e previsão a utilizar o Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: f40350129a12c7865051bcae80b74b6f9c069179
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233537"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Deteção de anomalias e previsão no Explorador de dados do Azure

O Explorador de dados do Azure efetua contínuo de recolha de dados de telemetria do serviços em nuvem ou de dispositivos IoT. Estes dados são analisados para as várias informações, como a monitorização de estado de funcionamento do serviço, os processos de produção físico, tendências de utilização e previsão de carga. A análise ser efetuada em série de tempo das métricas selecionadas para localizar um padrão de desvio da métrica em relação ao respetivo padrão de linha de base normal típico. O Explorador de dados do Azure contém o suporte nativo para criação, a manipulação e a análise de várias séries de tempo. Ele pode criar e analisar milhares de séries de tempo em segundos, permitindo que quase soluções e fluxos de trabalho de monitorização em tempo real.

Este artigo fornece detalhes sobre os Explorador de dados do Azure tempo série anomalias deteção e a previsão de recursos. As funções de série de tempo aplicável se baseiam num modelo de decomposição bem conhecidos robusto, em que cada série de tempo original for decomposta em sazonal, tendência e componentes residuais. Anomalias são detetadas através da valores atípicos no componente residual, enquanto a previsão é feita ao extrapolando o sazonais e componentes de tendência. A implementação do Explorador de dados do Azure melhora significativamente o modelo de decomposição básica por deteção de sazonalidade automática, análise de outlier robusta e rato vetorizada implementação para processar milhares de série de tempo em segundos.

## <a name="prerequisites"></a>Pré-requisitos

Leia [tempo de análise de série no Explorador de dados do Azure](/azure/data-explorer/time-series-analysis) para uma descrição geral das capacidades de série de tempo.

## <a name="time-series-decomposition-model"></a>Modelo de decomposição de série de tempo

Implementação nativa do Explorador de dados do Azure para a predição de séries de tempo e deteção de anomalias utiliza um modelo de decomposição bem conhecido. Esse modelo é aplicado a série de tempo de métricas esperadas se manifeste periódicas e comportamento de tendência, como o tráfego de serviço, heartbeats de componente e medidas periódicas de IoT para prever futuros valores de métrica e detetar aqueles anómalas. A suposição de que este processo de regressão é que o que não seja conhecidos anteriormente sazonais e o comportamento de tendência, o tempo de série é distribuída aleatoriamente. Em seguida, pode prever futuros valores de métrica do sazonais e componentes de tendência, coletivamente com o nome da linha de base e ignorar a parte residual. Também pode detectar anómalos valores com base na análise de exceção utilizando apenas a parte residual.
Para criar um modelo de decomposição, use a função [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). O `series_decompose()` função aceita um conjunto de séries de tempo e decomposes automaticamente cada série de tempo para seu sazonais, tendência, residuais e componentes de linha de base. 

Por exemplo, pode decompor o tráfego de um serviço web interno usando a seguinte consulta:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Decomposição de série temporal](media/anomaly-detection/series-decompose-timechart.png)

* Série de tempo original tem o nome **núm** (a vermelho). 
* O processo começa com deteção automática da sazonalidade através da função [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) e extrai o **sazonais** padrão (em Roxo).
* O padrão sazonal é subtraído da série de tempo original e uma regressão linear é executado usando a função [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) para encontrar o **tendência** componente (em azul claro).
* A função subtrai esses a tendência e o restante é o **residuais** componente (em verde).
* Por fim, a função adiciona o sazonais de tendências e componentes para gerar o **linha de base** (a azul).

## <a name="time-series-anomaly-detection"></a>Deteção de anomalias de série de tempo

A função [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) localiza pontos anómalos num conjunto de séries de tempo. Essa função chama `series_decompose()` para criar o modelo de decomposição e, em seguida, executa [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) no componente de residual. `series_outliers()` calcula as classificações de anomalias para cada ponto do componente residual usando o teste de cerca do Tukey. As pontuações de anomalias acima 1.5 ou abaixo-1.5 indicam um aumento de anomalias protestos ou recusar, respetivamente. As pontuações de anomalias acima 3.0 ou abaixo-3.0 indicam uma forte anomalias. 

A consulta seguinte permite-lhe detetar anomalias no tráfego de serviço da web internos:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Deteção de anomalias de série de tempo](media/anomaly-detection/series-anomaly-detection.png)

* A série de tempo original (a vermelho). 
* A linha de base (sazonais + tendência) componente (a azul).
* Os pontos de anómalos (em Roxo) na parte superior da série de tempo original. Os pontos de anómalos significativamente desviar os valores de linha de base esperada.

## <a name="time-series-forecasting"></a>Previsão de série temporal

A função [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) prevê futuras valores de um conjunto de séries de tempo. Essa função chama `series_decompose()` criar a modelo e, em seguida, para cada série de tempo, a decomposição extrapolates o componente de linha de base para o futuro.

A seguinte consulta, pode prever o tráfego de serviço da web de próxima semana:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Previsão de série temporal](media/anomaly-detection/series-forecasting.png)

* Métrica original (a vermelho). Valores futuras estão em falta e definido como 0, por padrão.
* Utilize para tirar conclusões o componente de linha de base (a azul) para prever valores próxima semana.

## <a name="scalability"></a>Escalabilidade

Sintaxe de linguagem de consulta do Azure Data Explorer permite que uma única chamada para processar várias séries de tempo. Permite que sua implementação otimizada exclusiva para um desempenho rápido, que é fundamental para a deteção de anomalias em vigor e previsão ao monitorizar milhares de contadores em cenários em tempo real em tempo quase.

A consulta seguinte mostra o processamento de três séries de tempo em simultâneo:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Escalabilidade de série de tempo](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Resumo

Este documento fornece detalhes sobre as funções nativas do Explorador de dados do Azure para deteção de anomalias de série de tempo e previsão. Cada série de tempo original for decomposta em componentes de tendência e residuais sazonais, para detetar anomalias e/ou de previsão. Estas funcionalidades podem ser utilizadas para cenários de monitorização em tempo real, como deteção de falhas, manutenção preditiva e a pedido em tempo quase e previsão de carga.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [de Machine learning capacidades](/azure/data-explorer/machine-learning-clustering) no Explorador de dados do Azure.
