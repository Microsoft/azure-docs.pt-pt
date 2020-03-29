---
title: Analisar dados da série de tempo usando o Azure Data Explorer
description: Saiba como analisar os dados da série de tempo na nuvem usando o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765488"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Análise de séries temporais no Azure Data Explorer

O Azure Data Explorer (ADX) realiza a recolha em curso de dados de telemetria a partir de serviços na nuvem ou dispositivos IoT. Estes dados podem ser analisados para várias informações, tais como monitorização da saúde do serviço, processos de produção física e tendências de utilização. A análise é feita em séries de tempo de métricas selecionadas para encontrar um desvio no padrão em comparação com o seu padrão de base típico.
ADX contém suporte nativo para criação, manipulação e análise de várias séries de tempo. Neste tópico, saiba como o ADX é usado para criar e analisar **milhares de séries de tempo em segundos**, permitindo soluções de monitorização e fluxos de trabalho em tempo real.

## <a name="time-series-creation"></a>Criação de séries temporais

Nesta secção, criaremos um grande conjunto de séries de `make-series` horárioregulars de forma simples e intuitiva usando o operador, e preenchendo os valores em falta conforme necessário.
O primeiro passo na análise da série temporal é dividir e transformar a tabela original de telemetria num conjunto de séries temporais. A tabela geralmente contém uma coluna de carimbo de tempo, dimensões contextuais e métricas opcionais. As dimensões são usadas para dividir os dados. O objetivo é criar milhares de séries de tempo por partição em intervalos de tempo regulares.

A tabela *de* entrada demo_make_series1 contém registos de 600K de tráfego arbitrário do serviço web. Utilize o comando abaixo para recolher 10 registos:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA)**\]**

```kusto
demo_make_series1 | take 10 
```

A tabela resultante contém uma coluna de carimbo de tempo, três colunas de dimensões contextuais e nenhuma métrica:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Carimbo de tempo | BrowserVer | Rio Osver | País/Região |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Reino Unido |
|   | 2016-08-25 09:12:41.1120000 | Cromado 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Cromado 52.0 | Windows 7 | Reino Unido |
|   | 2016-08-25 09:12:46.5100000 | Cromado 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:12:46.5570000 | Cromado 52.0 | Windows 10 | República da Lituânia |
|   | 2016-08-25 09:12:47.0470000 | Cromado 52.0 | Windows 8.1 | Índia |
|   | 2016-08-25 09:12:51.3600000 | Cromado 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:12:51.6930000 | Cromado 52.0 | Windows 7 | Países Baixos |
|   | 2016-08-25 09:12:56.4240000 | Cromado 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:13:08.7230000 | Cromado 52.0 | Windows 10 | Índia |

Como não existem métricas, só podemos construir um conjunto de séries temporais que representam a própria contagem de tráfego, dividida seletiva por OS utilizando a seguinte consulta:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Utilize [`make-series`](/azure/kusto/query/make-seriesoperator) o operador para criar um conjunto de três séries temporais, onde:
    - `num=count()`: série de tempo de tráfego
    - `range(min_t, max_t, 1h)`: séries temporais são criadas em caixotes de 1 hora no intervalo de tempo (selos temporais mais antigos e recentes dos registos de tabelas)
    - `default=0`: especificar o método de enchimento para os caixotes em falta para criar séries de horário regulares. Alternativamente, [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction) [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction)utilização [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) , e [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) para alterações
    - `byOsVer`: partição por OS
- A estrutura de dados da série de tempo real é uma matriz numérica do valor agregado por cada recipiente de cada vez. Usamos `render timechart` para visualização.

Na mesa acima, temos três divisórias. Podemos criar uma série de tempo separada: Windows 10 (vermelho), 7 (azul) e 8.1 (verde) para cada versão DO, como visto no gráfico:

![Partição da série de tempo](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funções de análise de séries de tempo

Nesta secção, vamos executar funções típicas de processamento de séries.
Uma vez criada uma série de tempo, a ADX suporta uma lista crescente de funções para processá-las e analisá-las que podem ser encontradas na [documentação](/azure/kusto/query/machine-learning-and-tsa)da série de tempo . Descreveremos algumas funções representativas para o processamento e análise de séries temporais.

### <a name="filtering"></a>Filtragem

A filtragem é uma prática comum no processamento de sinais e útil para tarefas de processamento de séries temporais (por exemplo, alisar um sinal ruidoso, alterar a deteção).
- Existem duas funções genéricas de filtragem:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Aplicação do filtro FIR. Usado para um simples cálculo da média móvel e diferenciação da série de tempo para deteção de alterações.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Aplicação do filtro IIR. Usado para suavizar exponencial e soma acumulada.
- `Extend`a série de tempo definida adicionando uma nova série média móvel de caixas de tamanho 5 (nomeada *ma_num)* à consulta:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtragem da série de tempo](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Análise de regressão

A ADX suporta análises de regressão linear segmentadas para estimar a tendência da série de tempo.
- Utilize [series_fit_line()](/azure/kusto/query/series-fit-linefunction) para encaixar a melhor linha numa série temporal para deteção geral de tendências.
- Utilize [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) para detetar alterações de tendência, em relação à linha de base, que sejam úteis na monitorização de cenários.

Exemplo `series_fit_line()` de `series_fit_2lines()` e funções numa consulta de série seletiva:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=)**\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regressão da série temporal](media/time-series-analysis/time-series-regression.png)

- Azul: série supor
- Verde: linha equipada
- Vermelho: duas linhas ajustadas

> [!NOTE]
> A função detetou com precisão o ponto de salto (mudança de nível).

### <a name="seasonality-detection"></a>Deteção de sazonalidade

Muitas métricas seguem padrões sazonais (periódicos). O tráfego de serviços na nuvem geralmente contém padrões diários e semanais que são mais altos em torno do meio do dia útil e mais baixo à noite e durante o fim de semana. Os sensores ioT medem em intervalos periódicos. Medições físicas como temperatura, pressão ou humidade também podem mostrar comportamento sazonal.

O exemplo seguinte aplica a deteção de sazonalidade num tráfego de um mês de um serviço web (caixotes de 2 horas):

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA)**\]**

```kusto
demo_series3
| render timechart 
```

![Sazonalidade da série de tempo](media/time-series-analysis/time-series-seasonality.png)

- Utilize [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) para detetar automaticamente os períodos da série de tempo. 
- Utilize [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) se soubermos que uma métrica deve ter um período distinto específico e queremos verificar se existem.

> [!NOTE]
> É uma anomalia se períodos distintos específicos não existirem

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA)**\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | períodos | pontuações | dias |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

A função deteta sazonalidade diária e semanal. As pontuações diárias são menos do que o semanal porque os dias de fim de semana são diferentes dos dias úteis.

### <a name="element-wise-functions"></a>Funções elementais

Operações aritméticas e lógicas podem ser feitas numa série temporal. Usando [series_subtract()](/azure/kusto/query/series-subtractfunction) podemos calcular uma série de tempo residual, isto é, a diferença entre a métrica bruta original e uma suavidaa, e procurar anomalias no sinal residual:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Operações de séries temporais](media/time-series-analysis/time-series-operations.png)

- Azul: série supor
- Vermelho: série de tempo suavizada
- Verde: série supor

## <a name="time-series-workflow-at-scale"></a>Fluxo de trabalho da série de tempo em escala

O exemplo abaixo mostra como estas funções podem funcionar à escala em milhares de séries de tempo em segundos para deteção de anomalias. Para ver alguns registos de telemetria de uma métrica de leitura de um serviço DB ao longo de quatro dias executar a seguinte consulta:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==)**\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | CARIMBO TEMPORAL | Rio Loc | anonOp | DB | Leitura de Dados |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

E estatísticas simples:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA)**\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

A construção de uma série de tempo em caixotes de 1 hora da métrica de leitura (total de quatro dias * 24 horas = 96 pontos), resulta numa flutuação normal do padrão:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Série de tempo à escala](media/time-series-analysis/time-series-at-scale.png)

O comportamento acima é enganador, uma vez que a série de tempo normal única é agregada de milhares de casos diferentes que podem ter padrões anormais. Por isso, criamos uma série de horários por exemplo. Uma instância é definida por Loc (localização), anonOp (operação) e DB (máquina específica).

Quantas séries de tempo podemos criar?

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=)**\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Contagem |
|   | 18339 |

Vamos criar um conjunto de 18339 séries de tempo da métrica da contagem de leitura. Adicionamos `by` a cláusula à declaração de série seletiva, aplicamos regressão linear, e selecionamos as duas primeiras séries de tempo que tiveram a tendência de crescente mais significativa:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Série de tempo top dois](media/time-series-analysis/time-series-top-2.png)

Mostrar as instâncias:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Rio Loc | Op | DB | inclinação |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

Em menos de dois minutos, a ADX analisou cerca de 20.000 séries temporais e detetou duas séries de tempo anormal em que a contagem de leitura supor subitamente caiu.

Estas capacidades avançadas combinadas com o desempenho rápido aDX fornecem uma solução única e poderosa para a análise de séries de tempo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a deteção e previsão](/azure/data-explorer/anomaly-detection) de anomalias da série Time no Azure Data Explorer.
* Conheça as capacidades de [aprendizagem](/azure/data-explorer/machine-learning-clustering) automática no Azure Data Explorer.