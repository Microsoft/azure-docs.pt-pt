---
title: Capacidade de aprendizagem automática no Azure Data Explorer
description: Utilize o agrupamento de aprendizagem automática para análise de causas de raiz no Explorador de Dados Azure.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769936"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Capacidade de aprendizagem automática no Azure Data Explorer

O Azure Data Explorer, uma plataforma de análise de Big Data, é utilizado para monitorizar a saúde do serviço, QoS ou dispositivos avariados para comportamentos anómalos utilizando funções de [deteção e previsão](/azure/data-explorer/anomaly-detection) de anomalias incorporadas. Uma vez detetado um padrão anómalo, é realizada uma Análise da Causa da Raiz (RCA) para mitigar ou resolver a anomalia.

O processo de diagnóstico é complexo e longo e feito por especialistas em domínios. O processo inclui a recolha e a junção de dados adicionais de diferentes fontes para o mesmo período de tempo, procurando alterações na distribuição de valores em múltiplas dimensões, cartografando variáveis adicionais, e outras técnicas baseadas no conhecimento de domínio e intuição. Uma vez que estes cenários de diagnóstico são comuns no Azure Data Explorer, os plugins de aprendizagem automática estão disponíveis para facilitar a fase de diagnóstico e encurtar a duração da RCA.

O Azure Data Explorer tem três [`autocluster`](/azure/kusto/query/autoclusterplugin) [`basket`](/azure/kusto/query/basketplugin)plugins de Machine Learning: , e [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Todos os plugins implementam algoritmos de agrupamento. O `autocluster` `basket` agrupamento e os plugins `diffpatterns` um único recorde e os clusters de plugins as diferenças entre dois conjuntos de recordes.

## <a name="clustering-a-single-record-set"></a>Agrupamento de um único recorde

Um cenário comum inclui um conjunto de dados selecionado por um critério específico, como a janela do tempo que exibe comportamento anómalo, leituras de dispositivos de alta temperatura, comandos de longa duração e utilizadores de gastos de topo. Gostaríamos de encontrar padrões comuns (segmentos) nos dados. Os padrões são um subconjunto do conjunto de dados cujos registos partilham os mesmos valores em várias dimensões (colunas categóricas). A seguinte consulta constrói e mostra uma série de exceções de serviço ao longo de uma semana em caixotes de dez minutos:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Tabela de tempo de exceções de serviço](media/machine-learning-clustering/service-exceptions-timechart.png)

A contagem de exceções de serviço está relacionada com o tráfego de serviço seleção. Pode-se ver claramente o padrão diário, para os dias úteis de segunda a sexta-feira, com um aumento da exceção de serviço conta a meio do dia, e diminui nas contagens durante a noite. As baixas contagens são visíveis no fim de semana. Os picos de exceção podem ser detetados utilizando a deteção de [anomalias](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) da série de tempo no Azure Data Explorer.

O segundo pico nos dados ocorre na tarde de terça-feira. A seguinte consulta é usada para diagnosticar ainda mais este pico. Use a consulta para redesenhar o gráfico em torno do pico em resolução superior (oito horas em caixotes de um minuto) para verificar se é um pico afiado, e ver as suas fronteiras.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Foco no gráfico de tempo do pico](media/machine-learning-clustering/focus-spike-timechart.png)

Vemos um espigão estreito de dois minutos das 15:00 às 15:02. Na seguinte consulta, conte as exceções nesta janela de dois minutos:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Contagem |
|---------|
|972    |

Na seguinte consulta, a amostra 20 exceções de 972:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| Carimbo de Tempo Preciso            | Região | Unidade de Escala | Implantóide                     | Tracepoint | Anfitrião de Serviço                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Utilizar autocluster() para um único agrupamento de conjuntos de registos

Apesar de existirem menos de mil exceções, ainda é difícil encontrar segmentos comuns, uma vez que existem múltiplos valores em cada coluna. Pode utilizar [`autocluster()`](/azure/kusto/query/autoclusterplugin) o plugin para extrair instantaneamente uma pequena lista de segmentos comuns e encontrar os aglomerados interessantes dentro dos dois minutos do pico, como se pode ver na seguinte consulta:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| Segmentid | Contagem | Percentagem | Região | Unidade de Escala | Implantóide | Anfitrião de Serviço |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a2cb8ee20f16fc |  |

Pode ver pelos resultados acima que o segmento mais dominante contém 65,74% do total de registos de exceção e partilha quatro dimensões. O próximo segmento é muito menos comum, contém apenas 9,67% dos registos e partilha três dimensões. Os outros segmentos são ainda menos comuns. 

O autocluster usa um algoritmo proprietário para minerar múltiplas dimensões e extrair segmentos interessantes. "Interessante" significa que cada segmento tem uma cobertura significativa tanto do conjunto de registos como das funcionalidades definidas. Os segmentos também são divergidos, o que significa que cada um é significativamente diferente dos outros. Um ou mais destes segmentos podem ser relevantes para o processo da RCA. Para minimizar a revisão e avaliação do segmento, o cluster automático extrai apenas uma pequena lista de segmentos.

### <a name="use-basket-for-single-record-set-clustering"></a>Utilizar o cesto para um único agrupamento de conjuntos de registos

Também pode utilizar [`basket()`](/azure/kusto/query/basketplugin) o plugin como visto na seguinte consulta:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| Segmentid | Contagem | Percentagem | Região | Unidade de Escala | Implantóide | Tracepoint | Anfitrião de Serviço |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a2cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

O cabaz implementa o algoritmo Apriori para a mineração definida em item e extrai todos os segmentos cuja cobertura do conjunto de registos está acima de um limiar (padrão 5%). Pode ver que mais segmentos foram extraídos com segmentos semelhantes (por exemplo, segmentos 0,1 ou 2,3).

Ambos os plugins são poderosos e fáceis de usar, mas a sua limitação significativa é que agrupam um único recorde estabelecido de forma não supervisionada (sem rótulos). Não é, portanto, claro se os padrões extraídos caracterizam o conjunto de registos selecionado (os registos anómalos) ou o recorde global.

## <a name="clustering-the-difference-between-two-records-sets"></a>Agrupamento da diferença entre dois conjuntos de recordes

O [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) plugin supera `autocluster` a `basket`limitação de e . `Diffpatterns`leva dois recordes e extrai os segmentos principais que são diferentes entre eles. Um conjunto geralmente contém o conjunto de registos `autocluster` anómalo que está a ser investigado (um analisado por e). `basket` O outro conjunto contém o conjunto de registos de referência (linha de base). 

Na consulta abaixo, usamos `diffpatterns` para encontrar aglomerados interessantes dentro dos dois minutos do pico, que são diferentes dos aglomerados dentro da linha de base. Definimos a janela de base como os oito minutos antes das 15:00 (quando o pico começou). Precisamos também de alargar por uma coluna binária (AB) especificando se um registo específico pertence à linha de base ou ao conjunto anómalo. `Diffpatterns`implementa um algoritmo de aprendizagem supervisionado, onde as duas etiquetas de classe foram geradas pelo anómalo contra a bandeira de base (AB).

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| Segmentid | CountA | Conde B | Percenta | PercentB | Percentdiffab | Região | Unidade de Escala | Implantóide | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a2cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

O segmento mais dominante é o mesmo `autocluster`segmento que foi extraído por , a sua cobertura na janela anómala de dois minutos também é de 65,74%. Mas a sua cobertura na janela de oito minutos é de apenas 1,7%. A diferença é de 64,04%. Esta diferença parece estar relacionada com o pico anómalo. Pode verificar esta suposição dividindo o gráfico original nos registos pertencentes a este segmento problemático em relação aos outros segmentos, como se pode ver na consulta abaixo:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Validar o temtempo do segmento 'diffpattern'](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Este gráfico permite-nos ver que o pico na tarde de terça-feira `diffpatterns` se deveu a exceções deste segmento específico, descoberto susponsado através do plugin.

## <a name="summary"></a>Resumo

Os plugins de Machine Learning do Explorador de Dados Azure são úteis para muitos cenários. O `autocluster` `basket` algoritmo de aprendizagem sem supervisão e são fáceis de usar. `Diffpatterns`implementa algoritmo de aprendizagem supervisionado e, embora mais complexo, é mais poderoso na extração de segmentos de diferenciação para a RCA.

Estes plugins são utilizados interativamente em cenários ad-hoc e em serviços automáticos de monitorização em tempo real. No Azure Data Explorer, a deteção de anomalias da série time é seguida por um processo de diagnóstico altamente otimizado para cumprir os padrões de desempenho necessários.
