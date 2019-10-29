---
title: Modelo de série temporal na visualização de Azure Time Series Insights | Microsoft Docs
description: Compreendendo Azure Time Series Insights modelo de série temporal.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 802fd444c953c69dfa99fbd49fdf9541cda372ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989831"
---
# <a name="time-series-model"></a>Modelo de Série de Tempo

Este documento descreve o modelo de série temporal, seus recursos e como começar a criar e atualizar seu próprio ambiente de Time Series Insights.

> [!TIP]
>  * Navegue até o ambiente de [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) para obter um exemplo de modelo de série temporal em tempo real.
> * Leia sobre o [Gerenciador de visualização Time Series insights](time-series-insights-update-explorer.md) para saber como navegar pela interface do usuário do modelo de série temporal.

## <a name="summary"></a>Resumo

Tradicionalmente, os dados coletados de dispositivos IoT não têm informações contextuais, o que dificulta a localização e a análise rápida de sensores. A principal motivação para o modelo de série temporal é simplificar a localização e a análise de dados de séries IoT/temporais. Ele alcança esse objetivo ao habilitar a consulta, a manutenção e o enriquecimento de dados de série temporal para ajudar a preparar conjuntos de os clientes prontos para o consumidor para análise.

## <a name="scenario-contosos-new-smart-oven"></a>Cenário: novo forno inteligente da contoso

**Considere o cenário fictício de um novo forno inteligente da contoso.** Neste cenário, suponha que cada forno inteligente da Contoso tenha cinco sensores de temperatura, um para cada um dos quatro gravadores principais e outro para o próprio forno. Até recentemente, cada sensor de temperatura da Contoso enviou, armazenou e visualizou seus dados individualmente. Para o monitoramento do seu dispositivo de cozinha, a contoso dependia de gráficos básicos, um para cada sensor individual.

Embora a contoso esteja satisfeita com sua solução inicial de dados e visualização, várias limitações se tornaram aparentes:

* Os clientes queriam saber o quanto o forno geral ficaria quando a maioria dos principais gravadores estavam, e a Contoso tinha mais dificuldade em analisar e apresentar uma resposta unificada sobre as condições do forno geral.
* Os engenheiros da Contoso queriam verificar se os principais gravadores que estão sendo executados simultaneamente não resultarão em um consumo de energia ineficiente, e houve dificuldade em fazer referência cruzada que sensores de temperatura e voltagem estavam associados entre si e como localizá-los em o repositório.
* A equipe do contoso Quality Assurance queria auditar e comparar o histórico entre duas versões de sensor, e houve dificuldade em determinar quais dados pertenciam a qual versão do sensor.

Sem a capacidade de estruturar, organizar e definir o modelo de série temporal de forno inteligente abrangente, cada sensor de temperatura manteve pontos de dados desalocados, isolados e menos informativos. É mais difícil transformar esses pontos de dados em informações acionáveis, pois cada conjunto de dados se encontrava independentemente dos outros.

Essas limitações revelaram a importância da agregação de dados inteligentes e das ferramentas de visualização para acompanhar o novo forno da Contoso:

* A visualização de dados comprova-se útil quando você é capaz de associar e combinar dados em uma exibição conveniente. Por exemplo, mostrando sensores de tensão junto com sensores de temperatura.
* É difícil realizar o gerenciamento de dados multidimensionais para várias entidades juntamente com as funcionalidades de comparação, zoom e intervalo de tempo.

O **modelo de série temporal fornece uma solução conveniente** para muitos dos cenários encontrados no exemplo fictício acima:

[gráfico de modelo de série temporal![](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* O modelo de série temporal exerce um papel vital em consultas e navegação porque ele contextualiza dados, permitindo que as comparações sejam desenhadas entre intervalos de tempo e entre os tipos de sensor e de dispositivo.
* Os dados são mais contextuais porque os dados persistidos em um modelo de série temporal preservam as computações de consulta de série temporal como variáveis e as usam no momento da consulta.
* O modelo de série temporal organiza e agrega dados para recursos aprimorados de visualização e gerenciamento.

### <a name="key-capabilities"></a>Principais capacidades

Com o objetivo de tornar simples e fácil gerenciar a congestão de séries temporais, o modelo de série temporal permite os seguintes recursos na visualização Time Series Insights. Ele ajuda você a:

* Crie e gerencie cálculos ou fórmulas que aproveitam funções escalares, operações de agregação e assim por diante.
* Defina relações pai-filho para habilitar a navegação, pesquisa e referência.
* Defina as propriedades associadas às instâncias, definidas como campos de *instância* e use-as para criar hierarquias.

### <a name="components"></a>Componentes

O modelo de série temporal tem três componentes principais:

* [Instâncias de modelo de série temporal](#time-series-model-instances)
* [Hierarquias de modelo de série temporal](#time-series-model-hierarchies)
* [Tipos de modelo de série temporal](#time-series-model-types)

Esses componentes são combinados para especificar um modelo de série temporal e para organizar seus dados de Azure Time Series Insights.

[Visão geral do modelo de série temporal![](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

O modelo de série temporal pode ser criado e gerenciado por meio da interface de [visualização Time Series insights](time-series-insights-update-how-to-tsm.md) . As configurações do modelo de série temporal podem ser gerenciadas por meio da [API de configurações do modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Instâncias de modelo de série temporal

*Instâncias* de modelo de série temporal são representações virtuais da própria série temporal.

Na maioria dos casos, as instâncias são identificadas exclusivamente por **DeviceID** ou **AssetID**, elas são salvas como IDs de série temporal.

As instâncias têm informações descritivas associadas a elas chamadas *Propriedades de instância* , como uma ID de série temporal, tipo, nome, descrição, hierarquias e campos de instância. No mínimo, as propriedades de instância incluem informações de hierarquia.

Os campos de instância são uma coleção de informações descritivas que podem incluir valores para níveis de hierarquia, bem como fabricante, operador, etc.

Depois que uma fonte de evento é configurada para Time Series Insights ambiente, as instâncias são automaticamente descobertas e criadas no modelo de série temporal. As instâncias podem ser criadas ou atualizadas por meio do Time Series Insights Explorer usando consultas de modelo de série temporal.

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) fornece vários exemplos de instância ao vivo.

[instâncias do modelo de série temporal![](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Propriedades da instância

As instâncias são definidas por **Timeseriesid**, **typeId**, **Name**, **Description**, **hierarchyIds**e **instanceFields**. Cada instância é mapeada para apenas um *tipo*e uma ou mais *hierarquias*.

| Propriedade | Descrição |
| --- | ---|
| Série temporal | O UUID da série temporal à qual a instância está associada. |
| Identificação | O UUID do tipo de modelo de série temporal ao qual a instância está associada. Por padrão, todas as novas instâncias descobertas são associadas a um tipo padrão|
| nome | A propriedade *Name* é opcional e diferencia maiúsculas de minúsculas. Se o *nome* não estiver disponível, ele usará como padrão *timeseriesid*. Se um *nome* for fornecido, a *timeseriesid* ainda estará disponível no [bem](time-series-insights-update-explorer.md#preview-well). |
| descrição | Uma descrição de texto da instância. |
| hierarchyIds | Define a quais hierarquias a instância pertence. |
| instanceFields | *instanceFields* são propriedades de uma instância e de quaisquer dados estáticos que definem uma instância. Eles definem valores de propriedades de hierarquia ou de não hierarquia, além de oferecer suporte à indexação para executar operações de pesquisa. |

> [!NOTE]
> As hierarquias são criadas usando **campos de instância**, e **instanceFields** adicionais podem ser adicionados para uma definição de propriedade de instância adicional.

As instâncias têm a seguinte representação JSON:

```JSON
{
    "timeSeriesId": ["PU2"],
    "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
    "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
    "description": "Pump #2",
    "instanceFields": {
        "Location": "Redmond",
        "Fleet": "Fleet 5",
        "Unit": "Pump Unit 3",
        "Manufacturer": "Contoso",
        "ScalePres": "0.54",
        "scaleTemp": "0.54"
    }
}
```

> [!TIP]
> Para obter Time Series Insights API de instância e suporte CRUD, consulte o artigo [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação REST da API de instância](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarquias de modelo de série temporal

As *hierarquias* de modelo de série temporal organizam instâncias especificando nomes de propriedade e suas relações.

Você pode configurar várias hierarquias em um determinado ambiente de Time Series Insights. Uma instância de modelo de série temporal pode ser mapeada para uma única hierarquia ou várias hierarquias (relação muitos para muitos).

A interface do cliente de [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) exibe uma instância padrão e uma hierarquia de tipos.

[hierarquias de modelo de série temporal![](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Definição de hierarquia

As hierarquias são definidas por **ID**de hierarquia, **nome**e **origem**.

| Propriedade | Descrição |
| ---| ---|
| ID | O identificador exclusivo para a hierarquia – usado, por exemplo, ao definir uma instância. |
| nome | Uma cadeia de caracteres usada para fornecer um nome para a hierarquia. |
| source | Especifica a hierarquia organizacional ou o caminho, que é uma ordem pai-filho de cima para baixo da hierarquia que os usuários desejam criar. As propriedades pai-filho mapeiam *campos de instância*. |

As hierarquias são representadas em JSON como:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Anel

* `Location` define uma hierarquia com `states` pai e `cities`filho. Cada `location` pode ter vários `states` que, por sua vez, podem ter vários `cities`.
* `ManufactureDate` define uma hierarquia com `year` pai e `month`filho. Cada `ManufactureDate` pode ter vários `years` que, por sua vez, podem ter vários `months`.

> [!TIP]
> Para obter Time Series Insights API de instância e suporte CRUD, consulte o artigo [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação REST da API de hierarquia](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Exemplo de hierarquia

Considere um exemplo em que a hierarquia **H1** tem *prédio*, *andar*e *sala* como parte de sua definição de **instanceFieldNames** :

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Considerando os **campos de instância** usados na definição acima e várias séries temporais, os atributos e valores de hierarquia serão exibidos conforme mostrado na tabela a seguir:

| ID da série temporal | Campos de instância |
| --- | --- |
| ID1 | "compilando" = "1000", "piso" = "10", "sala" = "55"  |
| ID2 | "criando" = "1000", "sala" = "55" |
| VERSÕES | "Floor" = "10" |
| ID4 | "criando" = "1000", "Floor" = "10"  |
| ID5 | Nenhum de "prédio", "andar" ou "sala" está definido |

A série temporal **ID1** e **ID4** serão exibidas como parte da hierarquia **H1** no [Azure Time Series insights Explorer](time-series-insights-update-explorer.md) , já que elas têm total definição e correta *classificação,* *piso*e *espaço* parâmetro.

Os outros serão classificados em *instâncias não pais* , pois não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-types"></a>Tipos de modelo de série temporal

Os *tipos* de modelo de série temporal ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica de Time Series Insights.

Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância de modelo de série temporal pode ser do tipo *sensor de temperatura*, que consiste nas variáveis de *temperatura média*, *mínima*e temperatura *máxima*.

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) visualiza vários tipos de modelo de série temporal associados a suas respectivas instâncias.

[![tipos de modelo de série temporal](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Para a API de instância do Time Series Insights e o suporte CRUD, consulte o artigo [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação do tipo API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Propriedades do tipo

Os tipos de modelo de série temporal são definidos por uma **ID**, **nome**, **Descrição**e **variáveis**.

| Propriedade | Descrição |
| ---| ---|
| ID | O UUID para o tipo. |
| nome | Uma cadeia de caracteres usada para fornecer um nome para o tipo. |
| descrição | Uma descrição de cadeia de caracteres para o tipo. |
| As | Especifique as variáveis associadas ao tipo. |

Os tipos estarão de acordo com o seguinte exemplo de JSON:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Variáveis

Os tipos de Time Series Insights podem ter muitas variáveis que especificam fórmulas e regras de computação em eventos.

Cada variável pode ser um dos três *tipos*: *numeric*, *categórico*e *Aggregate*.

* Os tipos *numéricos* funcionam com valores contínuos. 
* Os tipos *categóricos* funcionam com um conjunto definido de valores discretos.
* Os valores de *agregação* combinam várias variáveis de tipo único (todos *numéricos* ou todos *categóricos*).

A tabela a seguir exibe quais propriedades são relevantes para cada tipo de variável.

[![tipos de modelo de série temporal](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variáveis numéricas

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria usados para computação provenientes do dispositivo/sensores ou transformados usando expressões de série temporal. Variáveis de tipo numérico devem ser do tipo *Double*.|
| Interpolação de variável | Interpolação especifica como reconstruir um sinal usando dados existentes. As opções de interpolação de *etapa* e *linear* estão disponíveis para variáveis numéricas. |
| Agregação de variável | Suporte a computação por meio dos operadores *AVG*, *min*, *Max*, *sum*, *Count*, *primeiro*, *último* e Timed (*AVG*, *min*, *Max*, *sum*e *Left*). |

As variáveis estão em conformidade com o seguinte exemplo de JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Variáveis categóricas

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria usados para computação provenientes do dispositivo/sensores. As variáveis de tipo categórico devem ser *longas* ou de *cadeia de caracteres*. |
| Interpolação de variável | Interpolação especifica como reconstruir um sinal usando dados existentes. A opção de interpolação de *etapa* está disponível para variáveis categóricas. |
| Categorias de variáveis | Categorias crie um mapeamento entre os valores provenientes do dispositivo/sensores para um rótulo. |
| Categoria padrão da variável | A categoria padrão é para todos os valores que não estão sendo mapeados na propriedade "Categories". |

As variáveis estão em conformidade com o seguinte exemplo de JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": "toLong($event.[Status].Double)",
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Variáveis de agregação

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Agregação de variável | Suporte a computação por meio de *Méd*, *mín*, *máx*, *soma*, *contagem*, *primeiro*, *último*. |

As variáveis estão em conformidade com o seguinte exemplo de JSON:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

As variáveis são armazenadas na definição de tipo de um modelo de série temporal e podem ser fornecidas embutidas por meio de [APIs de consulta](time-series-insights-update-tsq.md) para substituir a definição armazenada.

## <a name="next-steps"></a>Passos seguintes

- Consulte [Azure Time Series insights visualização de armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

- Saiba mais sobre operações de modelo de série temporal comuns na [modelagem de dados na visualização Azure Time Series insights](./time-series-insights-update-how-to-tsm.md)

- Leia a nova documentação de referência do [modelo de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
