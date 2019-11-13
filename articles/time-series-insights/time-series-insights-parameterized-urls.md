---
title: Compartilhar exibições personalizadas com URLs parametrizadas-Azure Time Series Insights | Microsoft Docs
description: Saiba como desenvolver URLs com parâmetros no Azure Time Series Insights para compartilhar facilmente exibições personalizadas.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: 145af35f8c36d7f4659c3937209cb0d4d5b221a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006380"
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

## <a name="time"></a>Hora

Pode especificar valores de hora absolutos ou relativos com URLs parametrizados.

### <a name="absolute-time-values"></a>Valores de hora absolutos

Para valores de hora absolutos, utilize os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início do intervalo de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de fim do intervalo de pesquisa.

Para identificar os milissegundos de JavaScript de uma data, veja [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Conversor de Carimbo de Data/Hora Epoch e Unix).

### <a name="relative-time-values"></a>Valores de hora relativos

Para valores de hora relativos, utilize `relativeMillis=<value>`, em que o *valor* está em milissegundos de JavaScript da data mais recente no back-end.

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

O parâmetro `timeSeriesDefinitions=<collection of term objects>` especifica os termos de uma exibição Time Series Insights:

| Parâmetro | Item de URL | Descrição |
| --- | --- | --- |
| **name** | `\<string>` | É o nome do *termo*. |
| **splitBy** | `\<string>` | É o nome da coluna pela qual *dividir*. |
| **measureName** | `\<string>` | É o nome da coluna da *medida*. |
| **predicado** | `\<string>` | É a cláusula *where* para a filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica o uso da soma para sua medida. </br>  Observe que, se `Events` for a medida selecionada, Count será selecionado por padrão.  </br>  Se `Events` não for selecionado, a média será selecionada por padrão. |

* O par `multiChartStack=<true/false>` chave-valor habilita o empilhamento no gráfico.
* O par `multiChartSameScale=<true/false>` chave-valor habilita a mesma escala do eixo Y entre os termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite que você ajuste o controle deslizante de intervalo para fornecer uma exibição mais granular ou mais suave do gráfico.  
* O parâmetro `timezoneOffset=<integer>` permite que você defina o fuso horário do gráfico a ser exibido como um deslocamento para UTC.

| Par (es) | Descrição |
| --- | --- |
| `multiChartStack=false` | o `true` é habilitado por padrão, assim, passe `false` para Stack. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento tem de estar ativado para utilizar a mesma escala de eixo Y entre termos.  É `false` por padrão, então a passagem de ' true ' habilita essa funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = dias, horas, minutos, segundos, milissegundos.  Capitalize sempre a unidade. </br> Defina o número de unidades transferindo o número inteiro pretendido para timeBucketSize.  Tenha em atenção que tem até 7 dias.  |
| `timezoneOffset=-<integer>` | O número inteiro é sempre em milissegundos. </br> Observe que essa funcionalidade é um pouco diferente do que habilitamos no Time Series Insights Explorer, no qual podemos permitir que você escolha local (horário do navegador) ou UTC. |

### <a name="examples"></a>Exemplos

Para adicionar definições de série temporal a um ambiente de Time Series Insights como um parâmetro de URL, acrescente:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Use as definições de série temporal de exemplo para:

* A ID do ambiente
* Os últimos 60 minutos de dados
* Os termos (F1PressureID, F2TempStation e F3VibrationPL) que compõem os parâmetros opcionais

Você pode construir a seguinte URL com parâmetros para uma exibição:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Consulte o Gerenciador ao vivo [usando a URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

A URL acima descreve e compila a exibição do Time Series Insights Explorer:

[![termos do Time Series Insights Explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

A exibição completa (incluindo o gráfico):

[exibição de gráfico de ![](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consultar dados usando C# ](time-series-insights-query-data-csharp.md)o.

* Saiba mais sobre o [Time Series insights Explorer](./time-series-insights-explorer.md).
