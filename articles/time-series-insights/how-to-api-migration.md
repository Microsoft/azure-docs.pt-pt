---
title: Migrando para novas versões AZure Time Series Insights Gen2 API | Microsoft Docs
description: Como atualizar os ambientes da Azure Time Series Insights Gen2 para utilizar novas versões geralmente disponíveis.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 15f1a814b302611029cf6459b8d2df93a32a2d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97740560"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrando para novas versões Azure Time Series Insights Gen2 API

## <a name="overview"></a>Descrição Geral

Se criou um ambiente Azure Time Series Insights Gen2 quando esteve em Visualização Pública (antes de 16 de julho de 2020), por favor atualize o seu ambiente de TSI para utilizar as novas versões geralmente disponíveis de APIs seguindo os passos descritos neste artigo. Esta alteração não afeta nenhum utilizadores que estejam a utilizar a versão Gen1 do Azure Time Series Insights.

> [!IMPORTANT]
> As atualizações descritas neste artigo apenas atualizarão as versões API utilizadas pelo seu ambiente TSI. Esta mudança não está relacionada com as novas [regras de achatamento e fuga da JSON](./concepts-json-flattening-escaping-rules.md) introduzidas para os ambientes da Gen2.

A nova versão API é `2020-07-31` e utiliza uma [sintaxe](/rest/api/time-series-insights/reference-time-series-expression-syntax)de expressão de série sonora atualizada.

Os utilizadores devem migrar as variáveis modelos de [séries de tempo](./concepts-variables.md)do seu ambiente, consultas guardadas, consultas de Power BI e quaisquer ferramentas personalizadas que estivam chamadas para os pontos finais da API. Se tiver alguma dúvida ou preocupação sobre o processo de migração, envie um bilhete de apoio através do portal Azure e mencione este documento.

> [!IMPORTANT]
> A versão API de pré-visualização `2018-11-01-preview` continuará a ser suportada até 31 de outubro de 2020. Preencha todas as etapas aplicáveis desta migração antes disso para evitar quaisquer perturbações no serviço.

## <a name="migrate-time-series-model-and-saved-queries"></a>Modelo de séries de tempo migrar e consultas guardadas

Para ajudar os utilizadores a migrar as suas [variáveis de Modelo de Séries de Tempo](./concepts-variables.md) e consultas guardadas, existe uma ferramenta incorporada disponível através do [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com). Navegue para o ambiente que deseja migrar e siga os passos abaixo. **Pode completar parcialmente a migração e voltar a completá-la mais tarde, no entanto, nenhuma das atualizações pode ser revertida.**

> [!NOTE]
> Tem de ser um contribuinte para o ambiente para fazer atualizações ao Modelo da Série De Tempo e guardar consultas. Se não for um Contribuinte, só poderá migrar as suas consultas pessoais salvas. Por favor, reveja [as políticas de acesso ao ambiente](./concepts-access-policies.md) e o seu nível de acesso antes de prosseguir.

1. O Explorer irá solicitar que atualize a sintaxe utilizada pelas variáveis do Modelo série de tempo e que guarde as consultas.

    [![Prompt](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Se fechar acidentalmente a notificação, pode ser encontrada no painel de notificação.

1. Clique **em 'Mostrar' Atualizações** para abrir a ferramenta de migração.

1. Clique **em Baixar os tipos**. Uma vez que a migração substituirá os seus tipos atuais para alterar a sintaxe variável, será necessário guardar uma cópia dos seus Tipos atuais. A ferramenta irá notificá-lo quando os Tipos tiverem sido descarregados.

    [![Tipos de descarregamento](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Clique **em Variáveis de Atualização**. A ferramenta irá notificá-lo quando as variáveis tiverem sido atualizadas.

    > [!IMPORTANT]
    > Se atualizar os seus Tipos, as aplicações personalizadas utilizando uma versão API mais antiga ( `2018-11-01-preview` ) terão de usar a nova versão API para `2020-07-31` continuar a funcionar. Se não tiver a certeza da versão API que está a usar, consulte o seu Administrador antes de atualizar. Pode fechar a ferramenta de migração e voltar a estes passos mais tarde. Leia mais sobre [como migrar aplicações personalizadas.](#migrate-custom-applications)

    [![Atualizar variáveis](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Clique **em Atualização para consultas guardadas**. A ferramenta irá notificá-lo quando as consultas guardadas tiverem sido atualizadas.

    [![Atualizar consultas guardadas](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Clique em **Concluído**.

    [![Migração concluída](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Reveja o seu ambiente atualizado, cartografando algumas das variáveis recém-criadas e consultas guardadas. Se vir algum comportamento inesperado durante o gráfico, envie-nos feedback utilizando a ferramenta de feedback no Explorer.

## <a name="migrate-power-bi-queries"></a>Consultas de Bi de Poder migratório

Se tiver gerado consultas utilizando o Conector Power BI, estão a fazer chamadas para Azure Time Series Insights utilizando a versão API de pré-visualização e a antiga Sintaxe de Expressão de Séries temporais. Estas consultas continuarão a recuperar dados com sucesso até que a API de pré-visualização seja depreciada.

Para atualizar as consultas para utilizar a nova versão API e a nova Sintaxe de Expressão de Séries De Tempo, as consultas terão de ser regeneradas a partir do Explorer. Leia mais sobre como [criar consultas utilizando o Conector Power BI](./how-to-connect-power-bi.md).

> [!NOTE]
> Deve utilizar pelo menos a versão de julho de 2020 do Power BI Desktop. Caso contrário, poderá ver um erro de "Versão de carga útil de consulta inválida".

## <a name="migrate-custom-applications"></a>Migrar aplicações personalizadas

Se a sua aplicação personalizada estiver a fazer chamadas para os seguintes pontos finais REST, basta atualizar a versão API para `2020-07-31` o URI:

- APIs modelo série de tempo
  - APIs de Configurações de Modelos
    - [Get](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Atualizar](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - APIs de instância
    - [Todas as operações do lote](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Pesquisa](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Sugerir](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - APIs da hierarquia
    - [Todas as operações do lote](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Tipos APIs
    - [Excluir, obter operações](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

Para os seguintes pontos finais REST, deve atualizar a versão API para `2020-07-31` o URI e certificar-se de que todas as ocorrências da `tsx` propriedade utilizam a [sintaxe](/rest/api/time-series-insights/reference-time-series-expression-syntax)de expressão de série sonora atualizada .

- Tipos APIs
  - [Colocar operação](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- APIs de Consulta
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateseries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Exemplos

#### <a name="typesbatchput"></a>TiposBatchPut

Antigo Corpo de Pedido (usado `2018-11-01-preview` por:

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Corpo de Pedido Atualizado (utilizado `2020-07-31` por:

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Em alternativa, o `filter` pode também `$event.Mode.String = 'outdoor'` ser. A `value` deve utilizar os suportes para escapar ao carácter especial `_` ().

#### <a name="getevents"></a>GetEvents

Antigo Corpo de Pedido (usado `2018-11-01-preview` por:

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Corpo de Pedido Atualizado (utilizado `2020-07-31` por:

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Em alternativa, o `filter` pode também `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` ser.

#### <a name="getseries"></a>GetSeries

Antigo Corpo de Pedido (usado `2018-11-01-preview` por:

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Corpo de Pedido Atualizado (utilizado `2020-07-31` por:

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Em alternativa, o `value` pode também `$event['Bar-Pressure-Offset'].Double` ser. Se nenhum tipo de dados for especificado, o tipo de dados é sempre assumido como Duplo. A notação do suporte deve ser utilizada para escapar ao carácter especial ( `-` ).

#### <a name="aggregateseries"></a>Agregados

Antigo Corpo de Pedido (usado `2018-11-01-preview` por:

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Corpo de Pedido Atualizado (utilizado `2020-07-31` por:

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Em alternativa, o `value` pode também `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` ser.

### <a name="potential-errors"></a>Erros potenciais

#### <a name="invalidinput"></a>Inválido

Se vir o seguinte erro, está a utilizar a nova versão API `2020-07-31` (), mas a sintaxe TSX não foi atualizada. Por favor, reveja a [Sintaxe de Expressão de Séries Temporais](/rest/api/time-series-insights/reference-time-series-expression-syntax) e os exemplos de migração acima. Certifique-se de que todas as `tsx` propriedades estão corretamente atualizadas antes de voltar a enviar o pedido da API.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

- Teste o seu ambiente através do [Azure Time Series Insights Explorer](./concepts-ux-panels.md) ou através da sua aplicação personalizada.