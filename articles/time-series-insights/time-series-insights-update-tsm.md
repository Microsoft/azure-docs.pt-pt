---
title: Modelo de série temporal-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre o modelo de série temporal na visualização Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5c045a4b5ccda47b786d86f1c004e9da4c8d85f3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112305"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Modelo de série temporal na visualização Azure Time Series Insights

Este artigo descreve o modelo de série temporal, os recursos e como começar a criar e atualizar seus próprios modelos no ambiente de visualização de Azure Time Series Insights.

> [!TIP]
>  * Vá para o ambiente de [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) para obter um exemplo de modelo de série temporal em tempo real.
> * Leia sobre o [Gerenciador de visualização Azure Time Series insights](time-series-insights-update-explorer.md) para saber como navegar pela interface do usuário do modelo de série temporal.

## <a name="summary"></a>Resumo

Tradicionalmente, os dados coletados de dispositivos IoT não têm informações contextuais, o que dificulta a localização e a análise rápida de sensores. A principal motivação para o modelo de série temporal é simplificar a localização e a análise de dados IoT ou de série temporal. Ele alcança esse objetivo ao habilitar a consulta, a manutenção e o enriquecimento de dados de série temporal para ajudar a preparar conjuntos de os clientes prontos para o consumidor para análise.

## <a name="scenario-contosos-new-smart-oven"></a>Cenário: novo forno inteligente da contoso

**Considere o cenário fictício de um forno inteligente da contoso.** Neste cenário, suponha que cada forno inteligente da Contoso tenha cinco sensores de temperatura, um para cada um dos quatro gravadores principais e outro para o próprio forno. Até recentemente, cada sensor de temperatura da Contoso enviou, armazenou e visualizou seus dados individualmente. Para o monitoramento do seu dispositivo de cozinha, a contoso dependia de gráficos básicos, um para cada sensor individual.

Embora a contoso esteja satisfeita com sua solução inicial de dados e visualização, várias limitações se tornaram aparentes:

* Os clientes queriam saber o quanto o forno geral obteria quando a maioria dos principais gravadores estivesse. A contoso teve mais dificuldade em analisar e apresentar uma resposta unificada sobre as condições do forno geral.
* Os engenheiros da Contoso queriam verificar se os principais gravadores que estão sendo executados simultaneamente não resultarão em um consumo de energia ineficiente. Houve dificuldade em fazer referência cruzada que sensores de temperatura e voltagem foram associados entre si e como localizá-los na loja.
* A equipe de garantia de qualidade da Contoso queria auditar e comparar o histórico entre duas versões de sensor. Houve dificuldade em determinar quais dados pertenciam a qual versão do sensor.

Sem a capacidade de estruturar, organizar e definir o modelo de série temporal de forno inteligente abrangente, cada sensor de temperatura manteve pontos de dados desalocados, isolados e menos informativos. A desativação desses pontos de dados em informações acionáveis era mais difícil, pois cada um deles se vida independentemente dos outros.

Essas limitações revelaram a importância da agregação de dados inteligentes e das ferramentas de visualização para acompanhar o novo forno da Contoso:

* A visualização de dados comprova-se útil quando você é capaz de associar e combinar dados em uma exibição conveniente. Um exemplo é mostrar sensores de tensão junto com sensores de temperatura.
* O gerenciamento de dados multidimensionais para várias entidades juntamente com a comparação, o zoom e as funcionalidades de intervalo de tempo podem ser difíceis de realizar.

O **modelo de série temporal fornece uma solução conveniente** para muitos dos cenários encontrados neste exemplo fictício:

[gráfico de modelo de série temporal ![](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* O modelo de série temporal exerce um papel vital em consultas e navegação porque ele contextualiza dados, permitindo que as comparações sejam desenhadas entre intervalos de tempo e entre os tipos de sensor e de dispositivo.
* Os dados são mais contextuais porque os dados persistentes no modelo de série temporal preservam as computações de consulta de série temporal como variáveis e as usam no momento da consulta.
* O modelo de série temporal organiza e agrega dados para recursos aprimorados de gerenciamento e visualização.

### <a name="key-capabilities"></a>Principais capacidades

Com o objetivo de tornar simples e fácil gerenciar a congestão de séries temporais, o modelo de série temporal permite os seguintes recursos na visualização Time Series Insights. Ele ajuda você a:

* Crie e gerencie cálculos ou fórmulas que aproveitam funções escalares, operações de agregação e assim por diante.
* Defina relações pai-filho para habilitar a navegação, pesquisa e referência.
* Defina as propriedades associadas às instâncias, definidas como campos de *instância*, e use-as para criar hierarquias.

### <a name="components"></a>Componentes

O modelo de série temporal tem três componentes principais:

* [Instâncias de modelo de série temporal](#time-series-model-instances)
* [Hierarquias de modelo de série temporal](#time-series-model-hierarchies)
* [Tipos de modelo de série temporal](#time-series-model-types)

Esses componentes são combinados para especificar um modelo de série temporal e para organizar seus dados de Azure Time Series Insights.

[Visão geral do modelo de série temporal ![](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

Um modelo de série temporal pode ser criado e gerenciado por meio da interface de [visualização Time Series insights](time-series-insights-update-how-to-tsm.md) . As configurações do modelo de série temporal podem ser gerenciadas por meio da [API de configurações do modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Instâncias de modelo de série temporal

*Instâncias* de modelo de série temporal são representações virtuais da própria série temporal.

Na maioria dos casos, as instâncias são identificadas exclusivamente por **DeviceID** ou **AssetID**, que são salvas como IDs de série temporal.

As instâncias têm informações descritivas associadas a elas chamadas *Propriedades de instância*, como uma ID de série temporal, tipo, nome, descrição, hierarquias e campos de instância. No mínimo, as propriedades de instância incluem informações de hierarquia.

Os *campos de instância* são uma coleção de informações descritivas que podem incluir valores para níveis de hierarquia, bem como fabricante, operador e assim por diante.

Depois que uma fonte de evento é configurada para o ambiente de Time Series Insights, as instâncias são descobertas automaticamente e criadas em um modelo de série temporal. As instâncias podem ser criadas ou atualizadas por meio do Time Series Insights Explorer usando consultas de modelo de série temporal.

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) fornece vários exemplos de instância ao vivo.

[instâncias do modelo de série temporal ![](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Propriedades da instância

As instâncias são definidas por **Timeseriesid**, **typeId**, **Name**, **Description**, **hierarchyIds**e **instanceFields**. Cada instância é mapeada para apenas um *tipo*e uma ou mais *hierarquias*.

| Propriedade | Descrição |
| --- | ---|
| Série temporal | O UUID da série temporal à qual a instância está associada. |
| Identificação | O UUID do tipo de modelo de série temporal ao qual a instância está associada. Por padrão, todas as novas instâncias descobertas são associadas a um tipo padrão.
| nome | A propriedade **Name** é opcional e diferencia maiúsculas de minúsculas. Se o **nome** não estiver disponível, o padrão será **timeseriesid**. Se um nome for fornecido, **Timeseriesid** ainda estará disponível no [bem](time-series-insights-update-explorer.md#4-time-series-well). |
| descrição | Uma descrição de texto da instância. |
| hierarchyIds | Define a quais hierarquias a instância pertence. |
| instanceFields | As propriedades de uma instância e quaisquer dados estáticos que definem uma instância. Eles definem valores de propriedades de hierarquia ou de não hierarquia, além de oferecer suporte à indexação para executar operações de pesquisa. |

> [!NOTE]
> As hierarquias são criadas usando campos de instância. **InstanceFields** adicionais podem ser adicionados para outras definições de propriedade de instância.

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
> Para Time Series Insights API de instância e suporte para criar, ler, atualizar e excluir (CRUD), consulte o artigo [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação REST da API de instância](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarquias de modelo de série temporal

As *hierarquias* de modelo de série temporal organizam instâncias especificando nomes de propriedade e suas relações.

Você pode configurar várias hierarquias em um determinado ambiente de Time Series Insights. Uma instância de modelo de série temporal pode ser mapeada para uma única hierarquia ou várias hierarquias (relação muitos para muitos).

A interface do cliente de [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) exibe uma instância padrão e uma hierarquia de tipos.

[hierarquias de modelo de série temporal ![](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Definição de hierarquia

As hierarquias são definidas por **ID**de hierarquia, **nome**e **origem**.

| Propriedade | Descrição |
| ---| ---|
| ID | O identificador exclusivo da hierarquia, que é usado, por exemplo, quando você define uma instância. |
| nome | Uma cadeia de caracteres usada para fornecer um nome para a hierarquia. |
| source | Especifica a hierarquia organizacional ou o caminho, que é uma ordem pai-filho de cima para baixo da hierarquia que os usuários desejam criar. As propriedades pai-filho mapeiam campos de instância. |

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

No exemplo de JSON anterior:

* `Location` define uma hierarquia com `states` pai e `cities`filho. Cada `location` pode ter vários `states`, que, por sua vez, podem ter vários `cities`.
* `ManufactureDate` define uma hierarquia com `year` pai e `month`filho. Cada `ManufactureDate` pode ter vários `years`, que, por sua vez, podem ter vários `months`.

> [!TIP]
> Para obter Time Series Insights API de instância e suporte CRUD, consulte o artigo [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação REST da API de hierarquia](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Exemplo de hierarquia

Considere um exemplo em que a hierarquia **H1** tem `building`, `floor`e `room` como parte de sua definição de **instanceFieldNames** :

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

Considerando os campos de instância usados na definição anterior e várias séries temporais, os valores e atributos de hierarquia aparecem conforme mostrado na tabela a seguir:

| ID da série temporal | Campos de instância |
| --- | --- |
| ID1 | "compilando" = "1000", "piso" = "10", "sala" = "55"  |
| ID2 | "criando" = "1000", "sala" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "criando" = "1000", "Floor" = "10"  |
| ID5 | Nenhum de "prédio", "andar" ou "sala" é definido. |

A série temporal **ID1** e **ID4** são exibidos como parte da hierarquia **H1** no [Azure Time Series insights Explorer](time-series-insights-update-explorer.md) porque eles têm total definido e corretamente ordenado de *compilação*, *piso*e *sala* parâmetro.

Os outros são classificados em *instâncias sem pai* porque não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-types"></a>Tipos de modelo de série temporal

Os *tipos* de modelo de série temporal ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica de Time Series Insights.

Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância de modelo de série temporal pode ser do tipo *sensor de temperatura*, que consiste nas variáveis de *temperatura média*, *mínima*e temperatura *máxima*.

A [demonstração do farm de vento da Contoso](https://insights.timeseries.azure.com/preview/samples) visualiza vários tipos de modelo de série temporal associados a suas respectivas instâncias.

[![tipos de modelo de série temporal](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Para obter Time Series Insights API de instância e suporte CRUD, consulte o artigo [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação do tipo API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Propriedades do tipo

Os tipos de modelo de série temporal são definidos por **ID**, **nome**, **Descrição**e **variáveis**.

| Propriedade | Descrição |
| ---| ---|
| ID | O UUID para o tipo. |
| nome | Uma cadeia de caracteres usada para fornecer um nome para o tipo. |
| descrição | Uma descrição de cadeia de caracteres para o tipo. |
| as | Especifique as variáveis associadas ao tipo. |

Os tipos estão em conformidade com o seguinte exemplo de JSON:

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

Tipos de Time Series Insights podem ter muitas variáveis que especificam fórmulas e regras de computação em eventos.

Cada variável pode ser um dos três *tipos*: *numeric*, *categórico*e *Aggregate*.

* Os tipos **numéricos** funcionam com valores contínuos. 
* Os tipos **categóricos** funcionam com um conjunto definido de valores discretos.
* Valores de **agregação** combinam várias variáveis de um único tipo (todos numéricos ou todos categóricos).

A tabela a seguir exibe quais propriedades são relevantes para cada tipo de variável.

[![tipos de modelo de série temporal](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variáveis numéricas

| Propriedade Variable | Descrição |
| --- | ---|
| Filtro de variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão sendo consideradas para computação. |
| Valor da variável | Valores de telemetria usados para computação provenientes do dispositivo ou sensores ou transformados usando expressões de série temporal. Variáveis de tipo numérico devem ser do tipo *Double*.|
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
| Valor da variável | Valores de telemetria usados para computação provenientes do dispositivo ou dos sensores. As variáveis de tipo categórico devem ser *longas* ou de *cadeia de caracteres*. |
| Interpolação de variável | Interpolação especifica como reconstruir um sinal usando dados existentes. A opção de interpolação de *etapa* está disponível para variáveis categóricas. |
| Categorias de variáveis | Categorias crie um mapeamento entre os valores provenientes do dispositivo ou dos sensores para um rótulo. |
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
