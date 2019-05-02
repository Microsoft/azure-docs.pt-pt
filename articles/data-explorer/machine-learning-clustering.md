---
title: Aprendizagem automática capacidade no Explorador de dados do Azure
description: Utilize o machine learning clustering para a análise da causa raiz no Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 2358cb2ea411a0077f34798183da30bd32ae067b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925132"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Aprendizagem automática capacidade no Explorador de dados do Azure

Explorador de dados do Azure, uma plataforma de análise de macrodados, é utilizado para monitorizar o estado de funcionamento do serviço, QoS ou dispositivos com funcionamento incorreto para o comportamento anómalo usando incorporado [deteção de anomalias e previsão](/azure/data-explorer/anomaly-detection) funções. Depois de um padrão de anómalo detectado, é executada uma análise de causa raiz (RCA) para minimizar ou resolver da anomalia.

O processo de diagnóstico é complexa e demorada e concluído por especialistas de domínio. O processo inclui a obter e associar dados adicionais de origens diferentes para o mesmo intervalo de tempo, à procura de alterações na distribuição de valores em várias dimensões, criação de gráficos adicionais variáveis, e outras técnicas com base nos dados de conhecimento do domínio e intuição. Uma vez que estes cenários de diagnóstico são comuns no Explorador de dados do Azure, o plug-ins do machine learning estão disponíveis para facilitar a fase de diagnóstico e reduzir a duração do RCA.

O Explorador de dados do Azure tem três plug-ins de Machine Learning: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), e [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Todos os plug-ins de implementam algoritmos de clustering. O `autocluster` e `basket` plug-ins de um único conjunto de registos do cluster e o `diffpatterns` Plug-in clusters as diferenças entre os dois conjuntos de registos.

## <a name="clustering-a-single-record-set"></a>Clustering de um conjunto único registo

Um cenário comum inclui um conjunto de dados selecionado por critérios específicos, tais como a janela de tempo que apresenta um comportamento anómalo, leituras de dispositivo de temperatura máxima, comandos de longa duração e superior, os utilizadores de gastos. Gostaríamos de uma forma simples e rápida para encontrar padrões comuns (segmentos) nos dados. Os padrões são um subconjunto do conjunto de dados cujos registos partilham os mesmos valores ao longo de várias dimensões (colunas categóricas). A seguinte consulta baseia-se e mostra uma série de tempo de exceções de serviço durante uma semana em contentores de dez minutos:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Pego de exceções de serviço](media/machine-learning-clustering/service-exceptions-timechart.png)

A contagem de exceção do serviço está relacionada com o geral tráfego de serviço. Pode ver claramente o padrão de diário, por dias úteis de segunda a sexta, com um aumento no serviço de exceção contagens de meio dia e cai em contagens de durante a noite. Contagens de fixa baixas são visíveis no final de semana. Os picos de exceção podem ser detectados através de [deteção de anomalias de série de tempo](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) no Explorador de dados do Azure.

O segundo pico nos dados ocorre em tarde Terça-feira. A seguinte consulta é utilizada para diagnosticar melhor esse pico. Utilize a consulta para redesenhar o gráfico em todo o pico na resolução superior (oito horas em contentores de um minuto) para verificar se é um pico sharp e ver seus limites.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Se concentrar em Pego de pico](media/machine-learning-clustering/focus-spike-timechart.png)

Vemos um pico de dois minutos estreito de 15:00 até 15:02. Na seguinte consulta, conte as exceções nesta janela de dois minutos:

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

Na seguinte consulta de exemplo 20 exceções fora 972:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Região | ScaleUnit | DeploymentId                     | Ponto de rastreio | ServiceHost                          |
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

### <a name="use-autocluster-for-single-record-set-clustering"></a>Utilize `autocluster()` para registo único conjunto de clustering

Apesar de existirem menos de mil exceções, é ainda difícil encontrar segmentos comuns, como há vários valores em cada coluna. Pode usar [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) Plug-in para extrair uma lista pequena de segmentos comuns e encontrar o interessante instantaneamente clusters dentro de dois minutos do pico, como visto na seguinte consulta:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Contagem | Percentagem | Região | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Pode ver acima nos resultados que o segmento mais dominante contém 65.74% dos registos de exceção total e compartilha as dimensões de quatro. O segmento seguinte é muito menos comum, contém apenas 9.67% dos registos e compartilha três dimensões. Os outros segmentos são até mesmo menos comuns. 

Autocluster utiliza um algoritmo proprietário para várias dimensões de extração e extrair segmentos interessantes. "Interessante" significa que cada segmento tem cobertura de significativa do conjunto de registos e o conjunto de funcionalidades. Os segmentos são também divergiram, isso significa que cada um deles é significativamente diferente dos outros. Um ou mais desses segmentos podem ser relevantes para o processo RCA. Para minimizar a revisão de segmento e a avaliação, autocluster extrai apenas uma lista de segmento de pequenas.

### <a name="use-basket-for-single-record-set-clustering"></a>Utilize `basket()` para registo único conjunto de clustering

Também pode utilizar o [ `basket()` ](/azure/kusto/query/basketplugin) Plug-in como visto na seguinte consulta:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Contagem | Percentagem | Região | ScaleUnit | DeploymentId | Ponto de rastreio | ServiceHost |
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
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Cesta implementa o algoritmo Apriori para item definido extração e extrai todos os segmentos cuja cobertura do conjunto de registos for superior a um limiar (predefinição 5%). Pode ver o que mais segmentos foram extraídos com rostos semelhantes (por exemplo, segmentos de 0,1 ou 2,3).

Ambos os plug-ins são poderosas e fáceis de usar, mas seus limitação significativa é que eles um único conjunto de forma não supervisionada (com sem etiquetas) de registos do cluster. Portanto, é claro se os padrões extraídos caracterizam o conjunto de registos selecionado (os registos anómalos) ou o conjunto de registos global.

## <a name="clustering-the-difference-between-two-records-sets"></a>Clustering a diferença entre conjuntos de dois registos

O [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) Plug-in supera a limitação de `autocluster` e `basket`. `Diffpatterns` demora dois conjuntos de registos e extrai os segmentos principais que são diferentes entre eles. Um conjunto geralmente contém o conjunto está a ser investigado de registos anómalos (um analisados pelo `autocluster` e `basket`). O outro conjunto contém o conjunto de registos de referência (linha de base). 

A consulta abaixo, utilizamos `diffpatterns` para localizar clusters interessantes em dois minutos o pico, que são diferentes de clusters numa linha de base. Vamos definir a janela de linha de base como os oito minutos antes de 15:00 (quando iniciado o pico). Também precisamos estender por uma coluna de binária (AB) especificar se um registo específico pertence à linha de base ou para o conjunto anómalo. `Diffpatterns` implementa um algoritmo de aprendizagem supervisionada, onde as etiquetas de dois classe geradas pelo anómalos em comparação com o sinalizador de linha de base (AB).

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

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Região | ScaleUnit | DeploymentId | Ponto de rastreio |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

O segmento mais dominante é o mesmo segmento que foram extraído por `autocluster`, sua cobertura na janela anómala de dois minutos também é 65.74%. Mas sua cobertura na janela de linha de base de oito minutos é apenas 1.7%. A diferença é 64.04%. Essa diferença parece estar relacionado com o pico anómalo. Pode verificar essa suposição, dividindo o gráfico original em registos que pertencem a esse segmento problemático em comparação com os outros segmentos, como visto na consulta abaixo:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![A validar 'diffpattern' Pego de segmento](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Este gráfico permite ver o que o pico na Terça-feira à tarde foi devido a exceções a partir deste segmento específico, detetados com o `diffpatterns` Plug-in.

## <a name="summary"></a>Resumo

Os plug-ins do Explorador de dados de Azure Machine Learning são úteis para muitos cenários. O `autocluster` e `basket` implementar o algoritmo de aprendizado não supervisionado e são fáceis de usar. `Diffpatterns` implementa supervisionado algoritmo de aprendizagem e embora mais complexas, é mais poderoso no extrair segmentos de diferenciação para RCA.

Esses plug-ins são utilizados interativamente em cenários de ad-hoc e automático perto de serviços de monitorização em tempo real. No Explorador de dados do Azure, deteção de anomalias de série de tempo é seguida um processo de diagnóstico que está altamente otimizado para atender a padrões de desempenho necessário.
