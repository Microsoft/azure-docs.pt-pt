---
title: Partilhe vistas personalizadas com URLs parametrizados - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a criar URLs parametrizados para compartilhar facilmente vistas personalizadas do explorador em Azure Time Series Insights.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417000"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partilhar vistas personalizadas com um URL parametrizado

Para partilhar uma vista personalizada no Time Series Insights Explorer, pode criar programáticamente um URL parametrizado da vista personalizada.

O Time Series Insights Explorer suporta parâmetros de consulta de URL para especificar pontos de vista na experiência diretamente a partir do URL. Por exemplo, ao utilizar apenas o URL, pode especificar um ambiente de destino, um predicado de pesquisa e um intervalo de tempo pretendido. Quando um utilizador seleciona o URL personalizado, a interface fornece uma ligação diretamente a esse ativo no portal Time Series Insights. Aplicam-se as políticas de acesso a dados.

> [!TIP]
> * Veja a demonstração gratuita da [Time Series Insights.](https://insights.timeseries.azure.com/samples)
> * Leia a documentação do [Time Series Insights Explorer.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica o ID do ambiente de destino. É um componente do acesso a dados FQDN, e você pode encontrá-lo no canto superior direito do ambiente visão geral no portal Azure. É tudo o que `env.timeseries.azure.com`precede.

Um exemplo de parâmetro de ID de ambiente é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Pode especificar valores de hora absolutos ou relativos com URLs parametrizados.

### <a name="absolute-time-values"></a>Valores de hora absolutos

Para valores de hora absolutos, utilize os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início do intervalo de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de fim do intervalo de pesquisa.

> [!TIP]
> Para traduzir facilmente as datas em milissegundos javaScript, experimente a [época & Conversor](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)de marca unix .

### <a name="relative-time-values"></a>Valores de hora relativos

Para um valor de `relativeMillis=<value>`tempo relativo, utilize , onde o *valor* está em JavaScript milissegundos do mais recente carimbo de tempo recebido da API.

Por exemplo, `&relativeMillis=3600000` apresenta os 60 segundos de dados mais recentes.

Os valores aceites correspondem ao menu **de tempo rápido** do explorador time series Insights, e incluem:

* `1800000`(Últimos 30 minutos)
* `3600000`(Últimos 60 minutos)
* `10800000`(Últimas 3 Horas)
* `21600000`(Últimas 6 Horas)
* `43200000`(Últimas 12 Horas)
* `86400000`(Últimas 24 Horas)
* `604800000`(Últimos 7 Dias)
* `2592000000`(Últimas 30 Horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica termos predicados que aparecerão numa visão da Time Series Insights:

| Parâmetro | URL Item | Descrição |
| --- | --- | --- |
| **nome** | `\<string>` | É o nome do *termo*. |
| **splitBy** | `\<string>` | É o nome da coluna pela qual *dividir*. |
| **medidaNome** | `\<string>` | É o nome da coluna da *medida*. |
| **predicado** | `\<string>` | É a cláusula *where* para a filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica a utilização da soma para a sua medida. |

> [!NOTE]
> Se `Events` for a medida de **utilização seleccionadaSum,** a contagem é selecionada por defeito.  
> Se `Events` não for selecionado, a média é selecionada por defeito. |

* O `multiChartStack=<true/false>` par de valor-chave permite o empilhamento na tabela.
* O `multiChartSameScale=<true/false>` par de valor-chave permite a mesma escala de eixo Y através de termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite ajustar o slider de intervalo para proporcionar uma visão mais granular ou mais suave e agregada do gráfico.  
* O `timezoneOffset=<integer>` parâmetro permite-lhe definir o fuso horário para que o gráfico seja visto como uma compensação à UTC.

| Par(s) | Descrição |
| --- | --- |
| `multiChartStack=false` | `true`é ativado por padrão `false` para passar a empilhar. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento tem de estar ativado para utilizar a mesma escala de eixo Y entre termos.  É `false` por defeito, por `true` isso passar permite esta funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades `days` `hours`= `minutes` `seconds`, `milliseconds`, , .  Capitalize sempre a unidade. </br> Defina o número de unidades passando o inteiro desejado para **o timeBucketSize**.  |
| `timezoneOffset=-<integer>` | O número inteiro é sempre em milissegundos. |

> [!NOTE]
> tempo Os valores **bucketUnit** podem ser suavizados até 7 dias.
> os valores **timezoneOffset** não são UTC nem hora local.

### <a name="examples"></a>Exemplos

Para adicionar definições de séries de tempo a um ambiente time series insights como um parâmetro URL, apêndice:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Utilize as definições da série de tempo exemplo para:

* O ID ambiental
* Os últimos 60 minutos de dados
* Os termos **(F1PressureID,** **F2TempStation**e **F3VibrationPL**) que compreendem os parâmetros opcionais

Pode construir o seguinte URL parametrizado para uma vista:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![URL parametrizado explorador da Série De Tempo Insights](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Consulte o Explorer ao vivo utilizando o exemplo [de URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) acima.

O URL acima descreve e exibe a visão parametrizada do explorador insights da Série de Tempo. 

* Os predicados parametrizados.

  [![Predicados parametidos do explorador da Série Time Insights.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A vista completa partilhada.

  [![A vista completa partilhada.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consultar dados usando C#](time-series-insights-query-data-csharp.md).

* Conheça o [Time Series Insights Explorer](./time-series-insights-explorer.md).
