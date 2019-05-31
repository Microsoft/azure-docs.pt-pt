---
title: Partilhar vistas personalizadas do Azure Time Series Insights com URLs parametrizados | Microsoft Docs
description: Este artigo descreve como desenvolver URLs parametrizados no Azure Time Series Insights, para partilhar facilmente a vista de um cliente.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dfc04397b1d7e9f3256810cbe469067ae52c99bd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238979"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partilhar vistas personalizadas com um URL parametrizado

Para partilhar uma vista personalizada no Explorador do Time Series Insights, pode criar programaticamente um URL parametrizado dessa vista.

O Explorador do Time Series Insights suporta parâmetros de consulta de URL para especificar vistas na experiência diretamente a partir do URL. Por exemplo, ao utilizar apenas o URL, pode especificar um ambiente de destino, um predicado de pesquisa e um intervalo de tempo pretendido. Quando um utilizador clica no URL personalizado, a interface fornece uma ligação diretamente para esse recurso no portal do Time Series Insights. Aplicam-se as políticas de acesso a dados.

> [!TIP]
> * Ver o gratuito [demonstração do Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Leia o que acompanha [Explorador do Time Series Insights](./time-series-insights-explorer.md) documentação.

## <a name="environment-id"></a>ID do Ambiente

O parâmetro `environmentId=<guid>` especifica o ID do ambiente de destino. É um componente do FQDN de acesso a dados e pode encontrá-lo no canto superior direito da descrição geral do ambiente no portal do Azure. É tudo o que vem antes de `env.timeseries.azure.com`.

Um exemplo de parâmetro de ID de ambiente é `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Pode especificar valores de hora absolutos ou relativos com URLs parametrizados.

### <a name="absolute-time-values"></a>Valores de hora absolutos

Para valores de hora absolutos, utilize os parâmetros `from=<integer>` e `to=<integer>`.

* `from=<integer>` é um valor em milissegundos de JavaScript da hora de início do intervalo de pesquisa.
* `to=<integer>` é um valor em milissegundos de JavaScript da hora de fim do intervalo de pesquisa.

Para identificar os milissegundos de JavaScript de uma data, veja [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Conversor de Carimbo de Data/Hora Epoch e Unix).

### <a name="relative-time-values"></a>Valores de hora relativos

Para valores de hora relativos, utilize `relativeMillis=<value>`, em que o *valor* está em milissegundos de JavaScript da data mais recente no back-end.

Por exemplo, `&relativeMillis=3600000` apresenta os 60 segundos de dados mais recentes.

Os valores aceites correspondem para o Explorador do Time Series Insights **tempo Rápido** menu e incluem:

* `1800000` (Últimos 30 minutos)
* `3600000` (Últimos 60 min.)
* `10800000` (Últimas 3 horas)
* `21600000` (Últimas 6 horas)
* `43200000` (Últimas 12 horas)
* `86400000` (Últimas 24 horas)
* `604800000` (Últimos 7 dias)
* `2592000000` (Últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica as condições de uma vista do Time Series Insights:

| Parâmetro | Item de URL | Descrição |
| --- | --- | --- |
| **name** | `\<string>` | É o nome do *termo*. |
| **splitBy** | `\<string>` | É o nome da coluna pela qual *dividir*. |
| **measureName** | `\<string>` | É o nome da coluna da *medida*. |
| **predicate** | `\<string>` | É a cláusula *where* para a filtragem do lado do servidor. |
| **useSum** | `true` | Um parâmetro opcional que especifica através da soma para a sua medida. </br>  Tenha em atenção que se `Events` é a medida selecionada, a contagem é selecionada por predefinição.  </br>  Se `Events` não é selecionado, a média é selecionada por predefinição. |

* O `multiChartStack=<true/false>` par chave-valor permite o empilhamento no gráfico.
* O `multiChartSameScale=<true/false>` par chave-valor permite que a mesma escala de eixo y entre termos dentro de um parâmetro opcional.  
* O `timeBucketUnit=<Unit>&timeBucketSize=<integer>` permite-lhe ajustar o controlo de deslize de intervalo para fornecer mais granular ou suave agregada vista do gráfico.  
* O `timezoneOffset=<integer>` parâmetro permite-lhe definir o fuso horário para o gráfico a ser visualizado como desvio em UTC.

| Pair(s) | Descrição |
| --- | --- |
| `multiChartStack=false` | `true` está ativada por predefinição por isso, passar `false` em pilhas. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento tem de estar ativado para utilizar a mesma escala de eixo Y entre termos.  Ele tem `false` por predefinição, por isso, passar 'true' permite esta funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = dias, horas, minutos, segundos, milissegundos.  Capitalize sempre a unidade. </br> Defina o número de unidades transferindo o número inteiro pretendido para timeBucketSize.  Tenha em atenção que tem até 7 dias.  |
| `timezoneOffset=-<integer>` | O número inteiro é sempre em milissegundos. </br> Tenha em atenção que esta funcionalidade é ligeiramente diferente do que ativamos no Explorador do Time Series Insights, onde lhe permitimos escolher local (hora do browser) ou UTC. |

### <a name="examples"></a>Exemplos

Para adicionar definições de séries de tempo para um ambiente do Time Series Insights como um parâmetro de URL, acrescente:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Utilize as definições de série de tempo de exemplo para:

* O ID de ambiente
* Últimos 60 minutos de dados
* Os termos (F1PressureID, F2TempStation e F3VibrationPL) que compõem os parâmetros opcionais

pode construir o URL parametrizado seguinte para obter uma exibição:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Ver o Explorador do live [através do URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

O URL acima descreve e cria a vista do Explorador do Time Series Insights:

[![Termos do Explorador do Time Series Insights](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

A vista completa (incluindo o gráfico):

[![Vista de gráfico](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [consulta dados com C# ](time-series-insights-query-data-csharp.md).

* Saiba mais sobre o [Time Series Insights Explorer](./time-series-insights-explorer.md).
