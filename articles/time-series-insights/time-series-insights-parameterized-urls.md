---
title: Compartilhar exibições personalizadas com URLs parametrizadas-Azure Time Series Insights | Microsoft Docs
description: Saiba como criar URLs parametrizadas para compartilhar facilmente exibições personalizadas do Explorer no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024369"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partilhar vistas personalizadas com um URL parametrizado

Para compartilhar uma exibição personalizada no Time Series Insights Explorer, você pode criar programaticamente uma URL com parâmetros da exibição personalizada.

O Time Series Insights Explorer dá suporte a parâmetros de consulta de URL para especificar exibições na experiência diretamente da URL. Por exemplo, ao utilizar apenas o URL, pode especificar um ambiente de destino, um predicado de pesquisa e um intervalo de tempo pretendido. Quando um usuário seleciona a URL personalizada, a interface fornece um link diretamente para esse ativo no portal de Time Series Insights. Aplicam-se as políticas de acesso a dados.

> [!TIP]
> * Veja a [demonstração de time Series insights](https://insights.timeseries.azure.com/samples)gratuita.
> * Leia a documentação do [Time Series insights Explorer](./time-series-insights-explorer.md) que acompanha.

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica o ID do ambiente de destino. É um componente do FQDN de acesso a dados, e você pode encontrá-lo no canto superior direito da visão geral do ambiente na portal do Azure. É tudo o que vem antes de `env.timeseries.azure.com`.

Um exemplo de parâmetro de ID de ambiente é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tempo

Pode especificar valores de hora absolutos ou relativos com URLs parametrizados.

### <a name="absolute-time-values"></a>Valores de hora absolutos

Para valores de hora absolutos, utilize os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início do intervalo de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de fim do intervalo de pesquisa.

> [!TIP]
> Para traduzir datas facilmente em milissegundos de JavaScript, experimente a [época & o conversor de carimbo de data/hora UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valores de hora relativos

Para um valor de tempo relativo, use `relativeMillis=<value>`, onde o *valor* está em milissegundos de JavaScript do carimbo de data/hora mais recente recebido da API.

Por exemplo, `&relativeMillis=3600000` apresenta os 60 segundos de dados mais recentes.

Os valores aceitos correspondem ao menu de **tempo rápido** do time Series insights Explorer e incluem:

* `1800000` (últimos 30 minutos)
* `3600000` (últimas 60 min)
* `10800000` (últimas 3 horas)
* `21600000` (últimas 6 horas)
* `43200000` (últimas 12 horas)
* `86400000` (últimas 24 horas)
* `604800000` (últimos 7 dias)
* `2592000000` (últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O parâmetro `timeSeriesDefinitions=<collection of term objects>` especifica os termos de predicado que aparecerão em uma exibição de Time Series Insights:

| Parâmetro | Item de URL | Descrição |
| --- | --- | --- |
| **name** | `\<string>` | É o nome do *termo*. |
| **splitBy** | `\<string>` | É o nome da coluna pela qual *dividir*. |
| **measureName** | `\<string>` | É o nome da coluna da *medida*. |
| **predicado** | `\<string>` | É a cláusula *where* para a filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica o uso da soma para sua medida. |

> [!NOTE]
> Se `Events` for a medida de **usos** selecionada, Count será selecionado por padrão.  
> Se `Events` não for selecionado, a média será selecionada por padrão. |

* O par `multiChartStack=<true/false>` chave-valor habilita o empilhamento no gráfico.
* O par `multiChartSameScale=<true/false>` chave-valor habilita a mesma escala do eixo Y entre os termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite que você ajuste o controle deslizante de intervalo para fornecer uma exibição mais granular ou mais suave do gráfico.  
* O parâmetro `timezoneOffset=<integer>` permite que você defina o fuso horário do gráfico a ser exibido como um deslocamento para UTC.

| Par (es) | Descrição |
| --- | --- |
| `multiChartStack=false` | o `true` é habilitado por padrão, assim, passe `false` para Stack. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento tem de estar ativado para utilizar a mesma escala de eixo Y entre termos.  `false` por padrão, a passagem de `true` habilita essa funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Units = `days`, `hours`, `minutes`, `seconds`, `milliseconds`.  Capitalize sempre a unidade. </br> Defina o número de unidades passando o inteiro desejado para **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | O número inteiro é sempre em milissegundos. |

> [!NOTE]
> os valores de **timeBucketUnit** podem ser suavizados para até 7 dias.
> os valores de **timezoneOffset** não são UTC nem horário local.

### <a name="examples"></a>Exemplos

Para adicionar definições de série temporal a um ambiente de Time Series Insights como um parâmetro de URL, acrescente:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Use as definições de série temporal de exemplo para:

* A ID do ambiente
* Os últimos 60 minutos de dados
* Os termos (**F1PressureID**, **F2TempStation**e **F3VibrationPL**) que compõem os parâmetros opcionais

Você pode construir a seguinte URL com parâmetros para uma exibição:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Time Series Insights parametrou URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Consulte o Gerenciador ao vivo [usando o exemplo de URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) acima.

A URL acima descreve e exibe a exibição parametrizada do Time Series Insights Explorer. 

* Os predicados com parâmetros.

  [![Time Series Insights parametrou predicados.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A exibição de gráfico completo compartilhada.

  [![A vista de gráfico completo compartilhado.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consultar dados usando C# ](time-series-insights-query-data-csharp.md)o.

* Saiba mais sobre o [Time Series insights Explorer](./time-series-insights-explorer.md).
