---
title: Analisar dados de séries de tempo com o Explorador de dados do Azure
description: Saiba como analisar dados de séries de tempo na cloud com o Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: cb5e41feb9be978592a1c1708ade0188ba20d504
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873153"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Análise de série de tempo no Explorador de dados do Azure

Explorador de dados do Azure (ADX) efetuar contínuo de recolha de dados de telemetria a partir de serviços cloud ou de dispositivos IoT. Estes dados podem ser analisados para as várias informações como estado de funcionamento de serviço monitorização, processos de produção físico e tendências de utilização. Análise ser efetuada em série de tempo das métricas selecionadas para localizar um desvio padrão em comparação comparada o respetivo padrão de linha de base comum.
ADX contém o suporte nativo para criação, a manipulação e a análise de várias séries de tempo. Neste tópico, saiba como o ADX é usado para criar e analisar **milhares de séries de tempo em segundos**, permitindo que quase soluções de monitorização em tempo real e fluxos de trabalho.

## <a name="time-series-creation"></a>Criação de série de tempo

Nesta secção, vamos criar um grande conjunto de série de tempo regulares simples e intuitiva utilizando o `make-series` operador e valores em falta do fill-in conforme necessário.
A primeira etapa na análise de série de tempo é de particionar e transformar a tabela original de telemetria a um conjunto de séries de tempo. A tabela contém, normalmente, uma coluna timestamp, dimensões contextuais e métricas opcionais. As dimensões são utilizadas para particionar os dados. O objetivo é criar milhares de séries de tempo por partição em intervalos de tempo regulares.

A tabela de entrada *demo_make_series1* contém 600 mil registos de tráfego de serviço da web arbitrários. Utilize o comando abaixo para 10 registos de exemplo:

```kusto
demo_make_series1 | take 10 
```

A tabela resultante contém uma coluna timestamp, três colunas de dimensões contextual e não existem métricas:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Carimbo de data/hora | BrowserVer | OsVer | País |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Reino Unido |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Reino Unido |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | República de Lituânia |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Índia |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Países Baixos |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Reino Unido |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Índia |

Uma vez que existem não existem métricas, só é possível criar um conjunto de séries de tempo que representa a contagem de tráfego em si, particionada por sistema operacional usando a seguinte consulta:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Utilize o [ `make-series` ](/azure/kusto/query/make-seriesoperator) operador para criar um conjunto de três séries de tempo, em que:
    - `num=count()`: série de tráfego de tempo
    - `range(min_t, max_t, 1h)`: série de tempo é criado em contentores de 1 hora no intervalo de tempo (mais antigos e mais recentes carimbos de registos de tabela)
    - `default=0`: Especificar método de preenchimento de discretizações em falta criar a série de tempo regulares. Em alternativa utilize [ `series_fill_const()` ](/azure/kusto/query/series-fill-constfunction), [ `series_fill_forward()` ](/azure/kusto/query/series-fill-forwardfunction), [ `series_fill_backward()` ](/azure/kusto/query/series-fill-backwardfunction) e [ `series_fill_linear()` ](/azure/kusto/query/series-fill-linearfunction) para que as alterações
    - `byOsVer`: a partição pelo sistema operacional
- A estrutura de dados de séries de tempo real é uma matriz numérico do valor agregado por cada posição de tempo. Usamos `render timechart` para visualização.

Na tabela acima, temos três partições. Podemos criar uma série de tempo separadas: Com o Windows 10 (vermelho), 7 (azul) e 8.1 (verde) para cada versão de SO, como visto no gráfico:

![Partição de série de tempo](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funções de análise de série de tempo

Nesta secção, vai realizar funções de processamento de série típica.
Depois de criar um conjunto de séries de tempo, ADX oferece suporte a uma lista crescente de funções para processar e analisá-los, que pode ser encontrado na [documentação da série de tempo](/azure/kusto/query/machine-learning-and-tsa). Iremos descrever algumas funções representativas para processamento e análise de séries de tempo.

### <a name="filtering"></a>Filtering

A filtragem é uma prática comum no sinal de processamento e útil para tarefas de processamento de séries de tempo (por exemplo, suavizar um sinal do ruído, alterar a deteção).
- Existem duas funções de filtragem genéricas:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Aplicar o filtro de PREENCHA. Utilizado para o cálculo simple de mover a média e de diferenciação da série de tempo para a deteção de alteração.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Aplicar o filtro IIR. Utilizado para nivelamento exponencial e soma cumulativa.
- `Extend` série de tempo definida adicionando uma nova série de médias móveis de tamanho de 5 discretizações (com o nome *ma_num*) para a consulta:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtragem de séries de tempo](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Análise de regressão

Suporta ADX segmentadas análise de regressão linear para estimar a tendência da série de tempo.
- Uso [series_fit_line()](/azure/kusto/query/series-fit-linefunction) de acordo com a linha de uma série de tempo para a deteção de tendência geral melhor.
- Uso [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) para detectar alterações tendência, relativo à linha de base, que são úteis em cenários de monitorização.

Exemplo de `series_fit_line()` e `series_fit_2lines()` funções numa consulta de série de tempo:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regressão de série de tempo](media/time-series-analysis/time-series-regression.png)

- Azul: série de tempo original
- Verde: linha de equipados
- Vermelho: duas linhas de ajustada

> [!NOTE]
> A função detetada com precisão o ponto de ligação (alterar nível).

### <a name="seasonality-detection"></a>Deteção de sazonalidade

Diversas métricas siga sazonais padrões (periódicos). Tráfego de utilizador dos serviços cloud geralmente contém diárias e semanais padrões que são mais altos em todo o meio do dia e a mais baixa durante a noite e no fim de semana. Medida de sensores de IoT em intervalos periódicos. Medidas físicas como temperatura, pressão ou humidade também podem mostrar o comportamento sazonal.

O exemplo seguinte aplica-se a deteção de sazonalidade no tráfego de um mês de um serviço web (discretizações de 2 horas):

```kusto
demo_series3
| render timechart 
```

![Sazonalidade de série de tempo](media/time-series-analysis/time-series-seasonality.png)

- Uso [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) para detetar automaticamente os períodos de na série de tempo. 
- Uso [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) se Sabemos que uma métrica deve ter period(s) distintos específico e que queremos verificar se existem.

> [!NOTE]
> É uma anomalia se não existirem períodos distintos específicos

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | períodos | Pontuações | dias |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

A função Deteta sazonalidade diária e semanal. Diária pontua menor do que o semanal, porque a fim de semana é diferentes dos dias da semana.

### <a name="element-wise-functions"></a>Funções element-Wise

Operações de aritméticas e lógicas podem ser feitas numa série de tempo. Usando [series_subtract()](/azure/kusto/query/series-subtractfunction) podemos calcular uma série de tempo residuais, que é, a diferença entre a métrica não processada original e uma suavizados e procurar anomalias no sinal de residual:

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

![Operações de série de tempo](media/time-series-analysis/time-series-operations.png)

- Azul: série de tempo original
- Vermelho: série de tempo de suavizados
- Verde: série de tempo residuais

## <a name="time-series-workflow-at-scale"></a>Fluxo de trabalho de série de tempo em escala

O exemplo abaixo mostra como essas funções podem ser executadas em escala em milhares de séries de tempo em segundos, de deteção de anomalias. Para ver alguns registos de telemetria de amostra de métrica de contagem de leitura de um serviço de DB através de quatro dias, execute a seguinte consulta:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | CARIMBO DE DATA/HORA | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | 9 de Loc | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | 9 de Loc | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | 9 de Loc | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | 9 de Loc | 371921734563783410 | 255 | 0 |

E estatísticas simples:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Criação de uma série de tempo em contentores de 1 hora da métrica de leitura (total de quatro dias * 24 horas = 96 pontos), resulta em flutuação padrão normal:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Série de tempo em escala](media/time-series-analysis/time-series-at-scale.png)

O comportamento acima é enganoso, uma vez que a série de tempo normal único é agregada a partir de milhares de instâncias diferentes que podem ter padrões anormais. Portanto, criamos uma série de tempo por instância. Uma instância é definida por Loc (localização), anonOp (operação) e DB (máquina específica).

O número de série de tempo podemos criar?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Contagem |
|   | 18339 |

Agora, vamos criar um conjunto de séries de tempo 18339 da métrica de contagem de leitura. Adicionamos a `by` cláusula para a instrução de série de marca, aplicam-se de regressão linear e selecione a parte superior a tendência de duas séries de tempo que tinha a diminuir mais significativos:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Série de tempo principais dois](media/time-series-analysis/time-series-top-2.png)

As instâncias de apresentação:

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
|   | Loc | OP | DB | inclinação |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

Em menos de dois minutos, ADX analisados próximas 20 000 séries de tempo e detetou duas séries de tempo anormal em que a contagem de leitura, de repente, removido.

Estas capacidades avançadas, combinadas com um desempenho rápido ADX fornecem uma solução única e eficiente para análise de série de tempo.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [deteção de anomalias de série e a previsão do tempo](/azure/data-explorer/anomaly-detection) no Explorador de dados do Azure.
* Saiba mais sobre [de Machine learning capacidades](/azure/data-explorer/machine-learning-clustering) no Explorador de dados do Azure.