---
title: Deteção e previsão de anomalias da série temporal - Azure Data Explorer
description: Saiba como analisar os dados da série de tempo para deteção e previsão de anomalias usando o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 4a7463d6149a921a4a29b43eaebb78a01543323a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985830"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Deteção e previsão de anomalias no Azure Data Explorer

O Azure Data Explorer realiza a recolha em curso de dados de telemetria a partir de serviços em nuvem ou dispositivos IoT. Estes dados são analisados para vários insights, tais como monitorização da saúde do serviço, processos de produção física, tendências de utilização e previsão de carga. A análise é feita em séries de tempo de métricas selecionadas para localizar um padrão de desvio da métrica em relação ao seu padrão normal típico de linha de base. O Azure Data Explorer contém suporte nativo para a criação, manipulação e análise de várias séries de tempo. Pode criar e analisar milhares de séries de tempo em segundos, permitindo soluções de monitorização em tempo real e fluxos de trabalho.

Este artigo detalha as capacidades de deteção e previsão de anomalias da série de tempo do Explorador de Dados do Azure. As funções da série de tempo aplicáveis baseiam-se num modelo robusto e conhecido de decomposição, onde cada série de tempo original é decomposta em componentes sazonais, trend e residuais. Anomalias são detetadas por outliers no componente residual, enquanto a previsão é feita extrapolando os componentes sazonais e de tendência. A implementação do Azure Data Explorer melhora significativamente o modelo básico de decomposição através da deteção automática de sazonalidade, análise sueste robusta e implementação vetorial para processar milhares de séries de tempo em segundos.

## <a name="prerequisites"></a>Pré-requisitos

Leia a análise da [série Time no Azure Data Explorer](/azure/data-explorer/time-series-analysis) para uma visão geral das capacidades da série temporal.

## <a name="time-series-decomposition-model"></a>Modelo de decomposição da série de tempo

A implementação nativa do Azure Data Explorer para a previsão da série de tempo e deteção de anomalias utiliza um modelo de decomposição bem conhecido. Este modelo é aplicado à série temporal de métricas que se espera que manifestem comportamento periódico e de tendência, tais como tráfego de serviço, batimentos cardíacos de componentes e medições periódicas ioT para prever valores métricos futuros e detetar valores anómalos. A suposição deste processo de regressão é que, para além do comportamento sazonal e de tendência anteriormente conhecido, a série de tempo é distribuída aleatoriamente. Em seguida, pode prever valores métricos futuros a partir dos componentes sazonais e de tendência, coletivamente nomeados como linha de base, e ignorar a parte residual. Também pode detetar valores anómalos com base em análises mais estranhas utilizando apenas a porção residual.
Para criar um modelo de decomposição, utilize a função [`series_decompose()`](/azure/kusto/query/series-decomposefunction). A função `series_decompose()` leva um conjunto de séries temporais e decompõe-se automaticamente de cada série de tempo aos seus componentes sazonais, trend, residuais e de base. 

Por exemplo, pode decompor o tráfego de um serviço web interno utilizando a seguinte consulta:

**\[** [**Clique para executar**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]** de consulta

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

![Decomposição da série de tempo](media/anomaly-detection/series-decompose-timechart.png)

* A série de tempo original é rotulada **num** (em vermelho). 
* O processo começa pela deteção automática da sazonalidade utilizando a função [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) e extrai o padrão **sazonal** (em roxo).
* O padrão sazonal é subtraído da série de tempo original e uma regressão linear é executada usando a função [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) para encontrar o componente de **tendência** (em azul claro).
* A função subtrai a tendência e o restante é o componente **residual** (em verde).
* Finalmente, a função adiciona os componentes sazonais e de tendência para gerar a linha de **base** (em azul).

## <a name="time-series-anomaly-detection"></a>Deteção de anomalias da série temporal

A função [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) encontra pontos anómalos num conjunto de séries temporais. Esta função chama `series_decompose()` para construir o modelo de decomposição e, em seguida, executa [`series_outliers()`](/azure/kusto/query/series-outliersfunction) no componente residual. `series_outliers()` calcula as pontuações de anomalia para cada ponto do componente residual utilizando o teste de vedação de Tukey. As pontuações de anomalia acima de 1,5 ou abaixo de -1,5 indicam um ligeiro aumento ou declínio de anomalia, respectivamente. As pontuações de anomalia acima de 3.0 ou abaixo de -3.0 indicam uma anomalia forte. 

A seguinte consulta permite detetar anomalias no tráfego interno do serviço web:

**\[** [**Clique para executar**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]** de consulta

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

![Deteção de anomalias da série temporal](media/anomaly-detection/series-anomaly-detection.png)

* A série de horários original (em vermelho). 
* O componente de base (sazonal + tendência) (em azul).
* Os pontos anómalos (em roxo) em cima da série de horários original. Os pontos anómalos desviam-se significativamente dos valores de base esperados.

## <a name="time-series-forecasting"></a>Previsão de série temporal

A função [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) prevê valores futuros de um conjunto de séries temporais. Esta função chama `series_decompose()` para construir o modelo de decomposição e, em seguida, para cada série de tempo, extrapolar o componente de base para o futuro.

A seguinte consulta permite-lhe prever o tráfego de serviçoweb da próxima semana:

**\[** [**Clique para executar**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]** de consulta

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

* Métrica original (em vermelho). Os valores futuros estão em falta e definidos para 0, por defeito.
* Extrapolar o componente de base (em azul) para prever os valores da próxima semana.

## <a name="scalability"></a>Escalabilidade

A sintaxe de linguagem de consulta do Azure Data Explorer permite uma única chamada para processar várias séries de tempo. A sua implementação otimizada única permite um desempenho rápido, que é fundamental para uma deteção e previsão eficazes de anomalias ao monitorizar milhares de contadores em cenários quase em tempo real.

A seguinte consulta mostra o processamento de três séries de tempo simultaneamente:

**\[** [**Clique para executar**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]** de consulta

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

![Escalabilidade da série temporal](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Resumo

Este documento detalha as funções nativas do Azure Data Explorer para deteção e previsão de anomalias da série temporal. Cada série de tempo original é decomposta em componentes sazonais, de tendência e residuais para detetar anomalias e/ou previsão. Estas funcionalidades podem ser utilizadas para cenários de monitorização em tempo real, tais como deteção de falhas, manutenção preditiva e previsão de procura e carga.

## <a name="next-steps"></a>Passos seguintes

Conheça as capacidades de [aprendizagem](/azure/data-explorer/machine-learning-clustering) automática no Azure Data Explorer.
