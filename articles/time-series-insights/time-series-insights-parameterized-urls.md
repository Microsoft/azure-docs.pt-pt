---
title: Partilhe vistas personalizadas com URLs parametrizados - Azure Time Series Insights / Microsoft Docs
description: Aprenda a criar URLs parametrizados para partilhar facilmente vistas personalizadas do explorador em Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 10616c8003d9bbbe42cb70bd1bac4193044907c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417000"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partilhar vistas personalizadas com um URL parametrizado

Para partilhar uma visão personalizada no Time Series Insights Explorer, pode criar programáticamente um URL parametrizado da vista personalizada.

O Time Series Insights Explorer suporta parâmetros de consulta de URL para especificar pontos de vista na experiência diretamente do URL. Por exemplo, ao utilizar apenas o URL, pode especificar um ambiente de destino, um predicado de pesquisa e um intervalo de tempo pretendido. Quando um utilizador seleciona o URL personalizado, a interface fornece uma ligação diretamente a esse ativo no portal Time Series Insights. Aplicam-se as políticas de acesso a dados.

> [!TIP]
> * Ver a [demonstração](https://insights.timeseries.azure.com/samples)gratuita do Time Series Insights .
> * Leia a documentação do [Time Series Insights Explorer.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica o ID do ambiente de destino. É um componente do acesso de dados FQDN, e você pode encontrá-lo no canto superior direito do ambiente visão geral no portal Azure. É tudo o que `env.timeseries.azure.com` precede.

Um exemplo de parâmetro de ID de ambiente é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Pode especificar valores de hora absolutos ou relativos com URLs parametrizados.

### <a name="absolute-time-values"></a>Valores de hora absolutos

Para valores de hora absolutos, utilize os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início do intervalo de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de fim do intervalo de pesquisa.

> [!TIP]
> Para traduzir facilmente as datas em milissegundos JavaScript, experimente o [Conversor de Tempos de Época & Da Época](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de hora relativos

Para um valor de tempo relativo, use `relativeMillis=<value>` , onde o *valor* está em JavaScript milissegundos da mais recente marca de tempo recebida da API.

Por exemplo, `&relativeMillis=3600000` apresenta os 60 segundos de dados mais recentes.

Os valores aceites correspondem ao menu **de tempo rápido** do explorador de séries de tempo, e incluem:

* `1800000`(Últimos 30 minutos)
* `3600000`(Últimos 60 minutos)
* `10800000`(Últimas 3 horas)
* `21600000`(Últimas 6 horas)
* `43200000`(Últimas 12 horas)
* `86400000`(Últimas 24 horas)
* `604800000`(Últimos 7 dias)
* `2592000000`(Últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica termos predicados que aparecerão numa vista de Insights da Série de Tempo:

| Parâmetro | Artigo URL | Description |
| --- | --- | --- |
| **nome** | `\<string>` | É o nome do *termo*. |
| **splitBy** | `\<string>` | É o nome da coluna pela qual *dividir*. |
| **medidaName** | `\<string>` | É o nome da coluna da *medida*. |
| **predicado** | `\<string>` | É a cláusula *where* para a filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica a utilização da soma para a sua medida. |

> [!NOTE]
> Se `Events` for a medida **utilização seleccionadaSSum,** a contagem é selecionada por predefinição.  
> Se `Events` não for selecionada, a média é selecionada por predefinição. |

* O `multiChartStack=<true/false>` par de valores-chave permite o empilhamento na tabela.
* O `multiChartSameScale=<true/false>` par de valores-chave permite a mesma escala de eixo Y em termos dentro de um parâmetro opcional.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`Permite-lhe ajustar o deslizador de intervalos para proporcionar uma visão mais granular ou mais suave e agregada do gráfico.  
* O `timezoneOffset=<integer>` parâmetro permite-lhe definir o timezone para que o gráfico seja visto como uma compensação para UTC.

| Pares(s) | Description |
| --- | --- |
| `multiChartStack=false` | `true`é ativado por padrão assim passar `false` para empilhar. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento tem de estar ativado para utilizar a mesma escala de eixo Y entre termos.  É `false` por padrão, por isso a passagem `true` permite esta funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = `days` , , , , . `hours` `minutes` `seconds` `milliseconds` .  Capitalize sempre a unidade. </br> Defina o número de unidades passando o inteiro desejado para **o timeBucketSize**.  |
| `timezoneOffset=-<integer>` | O número inteiro é sempre em milissegundos. |

> [!NOTE]
> os valores **timeBucketUnit** podem ser suavizado até 7 dias.
> os valores **de timezoneOffset** não são utc nem hora local.

### <a name="examples"></a>Exemplos

Para adicionar definições de séries de tempo a um ambiente de Insights de Séries de Tempo como parâmetro URL, apêndice:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Utilize as definições de séries hortemporais de exemplo para:

* A iD do ambiente
* Os últimos 60 minutos de dados
* Os termos **(F1PressureID,** **F2TempStation**e **F3VibrationPL**) que compõem os parâmetros opcionais

Pode construir o seguinte URL parametrizado para uma vista:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Url parametrizado do explorador de séries de tempo](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Consulte o Explorer ao vivo [usando o](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) exemplo URL acima.

O URL acima descreve e exibe a vista de exploradores de Séries de Tempo parametrizadas. 

* Os predicados parametrizados.

  [![Séries de tempo Insights exploradores predicados parametrizados.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A vista de gráfico completo compartilhado.

  [![A vista de gráfico completo compartilhado.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consultar os dados utilizando C#](time-series-insights-query-data-csharp.md).

* Saiba mais sobre o [Time Series Insights Explorer](./time-series-insights-explorer.md).
