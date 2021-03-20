---
title: Partilhe vistas personalizadas com URLs parametrizados - Azure Time Series Insights | Microsoft Docs
description: Aprenda a criar URLs parametrizados para partilhar facilmente visualizações personalizadas do Explorer em Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91665332"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partilhar vistas personalizadas com um URL parametrizado

Para partilhar uma visão personalizada no Azure Time Series Insights Explorer, pode criar programáticamente um URL parametrizado da vista personalizada.

Azure Time Series Insights Explorer suporta parâmetros de consulta de URL para especificar pontos de vista na experiência diretamente do URL. Por exemplo, ao utilizar apenas o URL, pode especificar um ambiente de destino, um predicado de pesquisa e um intervalo de tempo pretendido. Quando um utilizador seleciona o URL personalizado, a interface fornece uma ligação diretamente a esse ativo no portal Azure Time Series Insights. Aplicam-se as políticas de acesso a dados.

> [!TIP]
>
> * Ver a demonstração gratuita da [Série de Tempos Azure](https://insights.timeseries.azure.com/samples)Insights .
> * Leia a documentação do [Azure Time Series Insights Explorer.](./time-series-insights-explorer.md)

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

Os valores aceites correspondem ao menu de **tempo insights** explorer da Série de Tempo Azure e incluem:

* `1800000` (Últimos 30 minutos)
* `3600000` (Últimos 60 minutos)
* `10800000` (Últimas 3 horas)
* `21600000` (Últimas 6 horas)
* `43200000` (Últimas 12 horas)
* `86400000` (Últimas 24 horas)
* `604800000` (Últimos 7 dias)
* `2592000000` (Últimas 30 horas)

### <a name="optional-parameters"></a>Parâmetros opcionais

O `timeSeriesDefinitions=<collection of term objects>` parâmetro especifica termos predicados que aparecerão numa vista Azure Time Series Insights:

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
| `multiChartStack=false` | `true` é ativado por padrão assim passar `false` para empilhar. |
| `multiChartStack=false&multiChartSameScale=true` | O empilhamento tem de estar ativado para utilizar a mesma escala de eixo Y entre termos.  É `false` por padrão, por isso a passagem `true` permite esta funcionalidade. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unidades = `days` , , , , . `hours` `minutes` `seconds` `milliseconds` .  Capitalize sempre a unidade. </br> Defina o número de unidades passando o inteiro desejado para **o timeBucketSize**.  |
| `timezoneOffset=-<integer>` | O número inteiro é sempre em milissegundos. |

> [!NOTE]
> os valores **timeBucketUnit** podem ser suavizado até 7 dias.
> os valores **de timezoneOffset** não são utc nem hora local.

### <a name="examples"></a>Exemplos

Para adicionar definições de séries de tempo a um ambiente Azure Time Series Insights como parâmetro URL, apêndice:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Utilize as definições de séries hortemporais de exemplo para:

* A iD do ambiente
* Os últimos 60 minutos de dados
* Os termos **(F1PressureID,** **F2TempStation** e **F3VibrationPL**) que compõem os parâmetros opcionais

Pode construir o seguinte URL parametrizado para uma vista:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![URL parametrizado do Azure Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Consulte o Explorer ao vivo [usando o](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) exemplo URL acima.

O URL acima descreve e exibe a vista paratizada do Azure Time Series Insights Explorer.

* Os predicados parametrizados.

  [![Azure Time Series Insights Explorer predicados parametrizados.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A vista de gráfico completo compartilhado.

  [![A vista de gráfico completo compartilhado.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [consultar os dados utilizando C#](time-series-insights-query-data-csharp.md).

* Saiba mais sobre o [Azure Time Series Insights Explorer](./time-series-insights-explorer.md).
