---
title: Detecção e previsão de anomalias de séries temporais no Azure Data Explorer
description: Saiba como analisar dados de série temporal para detecção de anomalias e previsão usando o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e06569a3a6948836201b333501bf2de0416d4ca
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766043"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Detecção de anomalias e previsão no Azure Data Explorer

O Azure Data Explorer executa a coleta contínua de dados de telemetria de serviços de nuvem ou dispositivos IoT. Esses dados são analisados para várias informações, como monitoramento de integridade do serviço, processos de produção física, tendências de uso e previsão de carga. A análise é feita na série temporal de métricas selecionadas para localizar um padrão de desvio da métrica em relação ao padrão típico de linha de base normal. O Azure Data Explorer contém suporte nativo para criação, manipulação e análise de várias séries temporais. Ele pode criar e analisar milhares de séries temporais em segundos, permitindo soluções de monitoramento e fluxos de trabalho quase em tempo real.

Este artigo detalha os recursos de previsão e de detecção de anomalias da série temporal do Azure Data Explorer. As funções de série temporal aplicáveis se baseiam em um modelo de decomposição conhecido robusto, em que cada série temporal original é decomposta em componentes sazonais, de tendência e residuais. As anomalias são detectadas por exceções no componente residual, enquanto a previsão é feita por meio da extrapolação dos componentes sazonais e de tendência. A implementação de Data Explorer do Azure aprimora significativamente o modelo de decomposição básico por detecção automática de sazonalidade, análise de exceção robusta e implementação em vetor para processar milhares de séries temporais em segundos.

## <a name="prerequisites"></a>Pré-requisitos

Leia [análise de série temporal no Azure data Explorer](/azure/data-explorer/time-series-analysis) para obter uma visão geral dos recursos de série temporal.

## <a name="time-series-decomposition-model"></a>Modelo de decomposição de série temporal

A implementação nativa do Azure Data Explorer para a previsão de série temporal e a detecção de anomalias usa um modelo de decomposição conhecido. Esse modelo é aplicado à série temporal de métricas esperadas para manifestar o comportamento periódico e de tendência, como tráfego de serviço, pulsações de componente e medições periódicas de IoT para prever valores de métrica futuros e detectar anomalias. A suposição desse processo de regressão é que, além do comportamento sazonal e de tendência conhecido anteriormente, a série temporal é distribuída aleatoriamente. Em seguida, você pode prever valores de métricas futuros dos componentes sazonais e de tendência, de linha de base nomeada coletivamente, e ignorar a parte residual. Você também pode detectar valores anormais com base na análise de exceção usando apenas a parte residual.
Para criar um modelo de decomposição, use a função [`series_decompose()`](/azure/kusto/query/series-decomposefunction). A função `series_decompose()` usa um conjunto de séries temporais e automaticamente decompõe cada série temporal para seus componentes sazonal, de tendência, residuais e de linha de base. 

Por exemplo, você pode decompor o tráfego de um serviço Web interno usando a seguinte consulta:

**\[** [**clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

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

* A série temporal original é rotulada **num** (vermelho). 
* O processo inicia pela detecção automática do sazonalidade usando a função [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) e extrai o padrão **sazonal** (em roxo).
* O padrão sazonal é subtraído da série temporal original e uma regressão linear é executada usando a função [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) para localizar o componente de **tendência** (em azul claro).
* A função subtrai a tendência e o restante é o componente **residual** (em verde).
* Por fim, a função adiciona os componentes sazonais e Trend para gerar a **linha de base** (em azul).

## <a name="time-series-anomaly-detection"></a>Detecção de anomalias de série temporal

A função [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) localiza pontos anormais em um conjunto de séries temporais. Essa função chama `series_decompose()` para criar o modelo de decomposição e, em seguida, executa [`series_outliers()`](/azure/kusto/query/series-outliersfunction) no componente residual. `series_outliers()` calcula as pontuações de anomalias para cada ponto do componente residual usando o teste de isolamento do Tukey. As pontuações de anomalias acima de 1,5 ou abaixo-1,5 indicam um leve aumento de anomalia ou declínio, respectivamente. As pontuações de anomalias acima de 3,0 ou abaixo-3,0 indicam uma forte anomalia. 

A consulta a seguir permite que você detecte anomalias no tráfego interno do serviço Web:

**\[** [**clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

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

![Detecção de anomalias de série temporal](media/anomaly-detection/series-anomaly-detection.png)

* A série temporal original (em vermelho). 
* O componente de linha de base (sazonal + Trend) (em azul).
* Os pontos anormais (em roxo) na parte superior da série temporal original. Os pontos anormais se desviam significativamente dos valores de linha de base esperados.

## <a name="time-series-forecasting"></a>Previsão de série temporal

A função [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) prevê os valores futuros de um conjunto de séries temporais. Essa função chama `series_decompose()` para criar o modelo de decomposição e, em seguida, para cada série temporal, extrapola o componente de linha de base no futuro.

A consulta a seguir permite prever o tráfego do serviço Web da próxima semana:

**\[** [**clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Previsão de série temporal](media/anomaly-detection/series-forecasting.png)

* Métrica original (em vermelho). Valores futuros estão ausentes e definidos como 0, por padrão.
* Extrapolar o componente de linha de base (em azul) para prever valores da próxima semana.

## <a name="scalability"></a>Escalabilidade

A sintaxe da linguagem de consulta do Azure Data Explorer permite que uma única chamada para processar várias séries temporais. Sua implementação otimizada exclusiva permite um desempenho rápido, que é essencial para a detecção e previsão de anomalias em vigor ao monitorar milhares de contadores em cenários quase em tempo real.

A consulta a seguir mostra o processamento de três séries temporais simultaneamente:

**\[** [**clique para executar a consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

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

![Escalabilidade de série temporal](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Resumo

Este documento detalha as funções nativas do Azure Data Explorer para detecção e previsão de anomalias de séries temporais. Cada série temporal original é decomposta em componentes sazonais, de tendência e residuais para detectar anomalias e/ou previsões. Essas funcionalidades podem ser usadas para cenários de monitoramento quase em tempo real, como detecção de falhas, manutenção preditiva e previsão de carga e demanda.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [recursos de aprendizado de máquina](/azure/data-explorer/machine-learning-clustering) no Azure data Explorer.
