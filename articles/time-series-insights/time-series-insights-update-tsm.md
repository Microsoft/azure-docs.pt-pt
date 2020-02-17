---
title: Modelo de Série temporal - Azure Time Series Insights  Microsoft Docs
description: Saiba mais sobre o Modelo da Série de Tempo na Pré-visualização de Insights da Série De Tempo Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 884244b245be06f1477d27a4828cad18e36eca24
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368630"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Modelo de série de tempo na pré-visualização de insights da série de tempo azure

Este artigo descreve o Modelo da Série de Tempo, as capacidades e como começar a construir e atualizar os seus próprios modelos no ambiente de pré-visualização da Série De Tempo Azure Insights.

> [!TIP]
>  * Vá ao ambiente de demonstração do [Parque Eólico De Contoso](https://insights.timeseries.azure.com/preview/samples) para um exemplo de Modelo da Série Do Tempo ao vivo.
> * Leia sobre o explorador de [pré-visualização](time-series-insights-update-explorer.md) da Série De Tempo Azure Insights para aprender a navegar no seu Modelo De Série De Tempo UI.
> * Saiba como trabalhar com o Modelo da [Série De Tempo](time-series-insights-update-how-to-tsm.md) utilizando o web explorer da Série Time Insights.

## <a name="summary"></a>Resumo

Tradicionalmente, os dados recolhidos a partir de dispositivos IoT carecem de informação contextual, o que dificulta a localização e análise rápida dos sensores. A principal motivação para o Modelo de Série selecionada é simplificar a descoberta e a análise de dados de IoT ou Séries de Tempo. Alcança este objetivo permitindo a curadoria, manutenção e enriquecimento de dados da série time para ajudar a preparar conjuntos de dados prontos para o consumidor para análise.

## <a name="scenario-contosos-new-smart-oven"></a>Cenário: novo forno inteligente de Contoso

**Considere o cenário fictício de um forno inteligente Contoso.** Neste cenário, suponha que cada forno inteligente Contoso tem cinco sensores de temperatura, um para cada um dos quatro queimadores de topo e um para o próprio forno. Até recentemente, cada sensor de temperatura Contoso enviou, armazenou e visualizava os seus dados individualmente. Para a sua monitorização de eletrodomésticos de cozinha, Contoso baseou-se em gráficos básicos, um para cada sensor individual.

Enquanto Contoso estava satisfeito com a sua solução inicial de dados e visualização, várias limitações tornaram-se evidentes:

* Os clientes queriam saber o quão quente o forno ficaria quando a maioria dos queimadores de topo estivessem ligados. Contoso teve mais dificuldade em analisar e apresentar uma resposta unificada sobre as condições do forno geral.
* Os engenheiros da Contoso queriam verificar se os queimadores de topo que estavam a ser executados simultaneamente não resultariam num empate de energia ineficiente. Havia dificuldade em cruzar os sensores de temperatura e tensão que estavam associados uns aos outros e como localizá-los na loja.
* A equipa de garantia de qualidade da Contoso quis auditar e comparar o histórico entre duas versões de sensores. Havia dificuldade em determinar que dados pertenciam a que versão de sensor.

Sem a capacidade de estruturar, organizar e definir o modelo de série de tempo do forno inteligente, cada sensor de temperatura manteve pontos de dados deslocados, isolados e menos informativos. Transformar estes pontos de dados em insights atuais foi mais difícil, uma vez que cada conjunto de dados vivia independentemente dos outros.

Estas limitações revelaram a importância de ferramentas inteligentes de agregação e visualização de dados para acompanhar o novo forno de Contoso:

* A visualização de dados revela-se útil quando é capaz de associar e combinar dados numa visão conveniente. Um exemplo é mostrar sensores de tensão juntamente com sensores de temperatura.
* Gerir dados multidimensionais para várias entidades juntamente com as funcionalidades de comparação, zooming e intervalo de tempo pode ser difícil de realizar.

O Modelo série de **tempo fornece uma solução conveniente** para muitos dos cenários encontrados neste exemplo fictício:

[![Time Series Model exemplo de gráficos de forno inteligente](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* O Modelo série de tempo desempenha um papel vital nas consultas e na navegação porque contextualiza os dados, permitindo que as comparações sejam desenhadas entre intervalos de tempo e entre os tipos de sensores e dispositivos. (**A**) 
* Os dados são ainda contextualizados porque os dados persistidos no Modelo da Série Temporal preservam as computações da série temporal como variáveis e reutilizam-nas no momento da consulta.
* O Modelo série de tempo organiza e agrega dados para uma melhor visualização e capacidades de gestão. B 

### <a name="key-capabilities"></a>Principais capacidades

Com o objetivo de torná-lo simples e sem esforço para gerir a contextualização da série de tempo, o Modelo série de tempo permite as seguintes capacidades na Pré-visualização de Insights da Série De Tempo. Ajuda-te:

* Autor e gerenciar computações ou fórmulas alavancando funções escalar, operações agregadas, e assim por diante.
* Defina as relações pai-filho para permitir a navegação, pesquisa e referência.
* Defina propriedades que estão associadas aos casos, definidos como campos de *exemplo,* e use-as para criar hierarquias.

### <a name="components"></a>Componentes

O Modelo série tem três componentes principais:

* [Instâncias do Modelo da Série De Tempo](#time-series-model-instances)
* [Hierarquias modelo de série seleções](#time-series-model-hierarchies)
* [Tipos de modelos de série seleções](#time-series-model-types)

Estes componentes são combinados para especificar um modelo de série de tempo e para organizar os seus dados de Insights da Série De Tempo Azure.

[gráfico de visão geral do modelo da série de tempo ![](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Um modelo de série de tempo pode ser criado e gerido através da interface de [pré-visualização](time-series-insights-update-how-to-tsm.md) de Insights da Série De Tempo. As definições do Modelo série de tempo podem ser geridas através da API de [Definições](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)do Modelo .

## <a name="time-series-model-instances"></a>Instâncias do Modelo da Série De Tempo

Os *casos* do Modelo série de tempo são representações virtuais da própria série temporal.

Na maioria dos casos, os casos são identificados exclusivamente pelo **dispositivoId** ou **assetId**, que são guardados como IDs da série de tempo.

Os casos têm informações descritivas associadas a elas chamadas propriedades de *instâncias*, tais como uma série de tempo ID, tipo, nome, descrição, hierarquias e campos de instância. No mínimo, as propriedades por exemplo incluem informações de hierarquia.

*Os campos* de exemplo são uma coleção de informações descritivas que podem incluir valores para níveis de hierarquia, bem como fabricante, operador, e assim por diante.

Depois de uma fonte de evento ser configurada para o ambiente Time Series Insights, as instâncias são automaticamente descobertas e criadas num modelo de série de tempo. As instâncias podem ser criadas ou atualizadas através do explorador time series Insights utilizando consultas de Modelo de Série de Tempo.

A demonstração do [Parque Eólico De Contoso](https://insights.timeseries.azure.com/preview/samples) fornece vários exemplos de exemploao vivo.

[exemplo de exemplo de modelo de série de tempo ![](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Propriedades de instância

As instâncias são definidas por **timeSeriesId**, **typeId,** **nome,** **descrição,** **hierarquias** **e, por exemplo, Campos**. Cada instância mapeia apenas um *tipo,* e uma ou mais *hierarquias.*

| Propriedade | Descrição |
| --- | ---|
| timeSeriesId | O UUID da série de tempo a que a instância está associada. |
| tipoid | O UUID do tipo modelo da Série De Tempo a instância está associada. Por padrão, todos os novos casos descobertos ficam associados a um tipo padrão.
| nome | A propriedade **do nome** é opcional e sensível a casos. Se o **nome** não estiver disponível, não está disponível para **timeSeriesId**. Se for fornecido um nome, o **timeSeriesId** ainda está disponível no [poço](time-series-insights-update-explorer.md#4-time-series-well). |
| descrição | Uma descrição de texto da instância. |
| hierarquiaIds | Define a que hierarquias a instância pertence. |
| instânciaS | As propriedades de um caso e quaisquer dados estáticos que definam um exemplo. Definem valores de propriedades hierárquicas ou não-hierarquias, ao mesmo tempo que apoiam a indexação para realizar operações de pesquisa. |

> [!NOTE]
> As hierarquias são construídas usando campos de exemplo. **Outros casos** podem ser adicionados para definições de propriedade posteriores.

As instâncias têm a seguinte representação da JSON:

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
> Para a Time Series Insights Instance API e criar, ler, atualizar e eliminar o suporte (CRUD), leia o artigo de consulta de [Dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a documentação de [repouso da API de Instância](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarquias modelo de série seleções

As *hierarquias* do Modelo série de tempo organizam instâncias especificando nomes de propriedade e suas relações.

Pode configurar várias hierarquias num dado ambiente time series Insights. Um exemplo de Modelo de Série de Tempo pode mapear para uma única hierarquia ou múltiplas hierarquias (relação de muitas a muitas).

A interface de cliente de demonstração de Parque [Eólico Contoso](https://insights.timeseries.azure.com/preview/samples) apresenta uma instância padrão e hierarquia de tipo.

[exemplo de hierarquia do modelo da série de tempo ![](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definição de hierarquia

As hierarquias são definidas por **id**hierarquia, **nome,** e **fonte.**

| Propriedade | Descrição |
| ---| ---|
| ID | O identificador único para a hierarquia, que é usado, por exemplo, quando se define um exemplo. |
| nome | Uma corda usada para dar um nome à hierarquia. |
| source | Especifica a hierarquia organizacional ou o caminho, que é uma ordem de pais e filhos de cima para baixo da hierarquia que os utilizadores querem criar. As propriedades pai-filho mapeiam campos de instância. |

As hierarquias estão representadas na JSON como:

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

No exemplo json anterior:

* `Location` define uma hierarquia com `states` dos pais e `cities`infantil. Cada `location` pode ter vários `states`, que por sua vez podem ter vários `cities`.
* `ManufactureDate` define uma hierarquia com `year` dos pais e `month`infantil. Cada `ManufactureDate` pode ter vários `years`, que por sua vez podem ter vários `months`.

> [!TIP]
> Para insights de série de tempo Suporte API e CRUD, leia o artigo de [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a documentação de repouso da Hierarquia [API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Exemplo de hierarquia

Considere um exemplo em que a hierarquia **H1** `building`, `floor`, e `room` como parte da sua definição **de FieldNames:**

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

Dadas as instâncias utilizadas na definição anterior e várias séries temporais, os atributos e valores da hierarquia aparecem como mostrado na tabela seguinte:

| ID da Série De Tempo | Campos de instância |
| --- | --- |
| ID1 | "Construção" = "1000", "andar" = "10", "quarto" = "55"  |
| ID2 | "Construção" = "1000", "quarto" = "55" |
| ID3 | "andar" = "10" |
| ID4 | "Edifício" = "1000", "andar" = "10"  |
| ID5 | Nenhum de "edifício", "chão", ou "quarto" está definido. |

As séries de tempo **ID1** e **ID4** são exibidas como parte da hierarquia **H1** no explorador de Insights da [Série De Tempo Azure](time-series-insights-update-explorer.md) porque eles têm totalmente definido e corretamente ordenado *edifício,* *piso,* e parâmetros de *sala.*

Os outros são classificados em *Instâncias Não Parentais* porque não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-types"></a>Tipos de modelos de série seleções

Os *tipos* de modelos da Série De Tempo ajudam-no a definir variáveis ou fórmulas para fazer cálculos. Os tipos estão associados a uma instância específica da Time Series Insights.

Um tipo pode ter uma ou mais variáveis. Por exemplo, um modelo de série temporal pode ser de tipo *Sensor*de Temperatura , que consiste nas variáveis *temperatura avg,* temperatura *min*, e *temperatura máxima*.

A demonstração do [Parque Eólico De Contoso](https://insights.timeseries.azure.com/preview/samples) visualiza vários tipos de Modelos da Série Tempo associados às respetivas instâncias.

[![exemplo do modelo da Série De Tempo](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Para insights de série de tempo Suporte API e CRUD, leia o artigo de [consulta de dados](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e a [documentação tipo API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Propriedades de tipo

Os tipos de modelos da Série De Tempo são definidos por **id,** **nome,** **descrição**e **variáveis.**

| Propriedade | Descrição |
| ---| ---|
| ID | O UUID para o tipo. |
| nome | Uma corda usada para fornecer um nome para o tipo. |
| descrição | Uma descrição de corda para o tipo. |
| variáveis | Especifique variáveis associadas ao tipo. |

Os tipos estão em conformidade com o seguinte exemplo JSON:

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

Os tipos de Insights da Série Time podem ter muitas variáveis que especificam regras de fórmula e cálculo em eventos.

Cada variável pode ser um de três *tipos:* *numérico,* *categórico,* e *agregado.*

* Os tipos **numéricos** funcionam com valores contínuos. 
* **Tipos categóricos** funcionam com um conjunto definido de valores discretos.
* Os valores **agregados** combinam múltiplas variáveis de um único tipo (seja todos numéricos ou todos categóricos).

A tabela a seguir mostra quais as propriedades relevantes para cada tipo variável.

[tabela variável modelo de série de tempo ![](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variáveis numéricas

| Propriedade variável | Descrição |
| --- | ---|
| Filtro variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão a ser consideradas para a computação. |
| Valor variável | Valores de telemetria utilizados para a computação provenientes do dispositivo ou sensores ou transformados utilizando expressões de séries de tempo. Variáveis numéricas do tipo *duplo.*|
| Interpolação variável | A Interpolação especifica como reconstruir um sinal utilizando os dados existentes. *As* opções de interpolação de passo e *linear* estão disponíveis para variáveis numéricas. |
| Agregação variável | Computação de suporte através dos operadores *Avg*, *Min,* *Max*, *Sum*, *Count,* *First,* *Last* e Time-Weighted (*Avg,* *Min,* *Max,* *Sum,* *Left*). |

As variáveis estão em conformidade com o seguinte exemplo JSON:

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

| Propriedade variável | Descrição |
| --- | ---|
| Filtro variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão a ser consideradas para a computação. |
| Valor variável | Valores de telemetria utilizados para a computação provenientes do dispositivo ou sensores. Variáveis categóricas de tipo devem ser *longas* ou *string*. |
| Interpolação variável | A Interpolação especifica como reconstruir um sinal utilizando os dados existentes. A opção de interpolação *step* está disponível para variáveis categóricas. |
| Categorias variáveis | As categorias criam um mapeamento entre os valores provenientes do dispositivo ou sensores para uma etiqueta. |
| Categoria padrão variável | A categoria padrão é para todos os valores que não estão sendo mapeados na propriedade "categorias". |

As variáveis estão em conformidade com o seguinte exemplo JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)" 
},
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

#### <a name="aggregate-variables"></a>Variáveis agregadas

| Propriedade variável | Descrição |
| --- | ---|
| Filtro variável | Os filtros são cláusulas condicionais opcionais para restringir o número de linhas que estão a ser consideradas para a computação. |
| Agregação variável | Computação de suporte através de *Avg,* *Min,* *Max*, *Soma,* *Conde,* *Primeiro,* *Último*. |

As variáveis estão em conformidade com o seguinte exemplo JSON:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

As variáveis são armazenadas na definição de tipo de modelo de série de tempo e podem ser fornecidas inline via [Query APIs](time-series-insights-update-tsq.md) para anular a definição armazenada.

## <a name="next-steps"></a>Passos seguintes

- Leia o armazenamento e a [entrada de visualização da Série de Tempo Azure](./time-series-insights-update-storage-ingress.md)Insights .

- Conheça as operações comuns do Modelo da Série de Tempo na [modelação de dados na Pré-visualização de Insights](./time-series-insights-update-how-to-tsm.md) da Série de Tempo azure

- Leia a nova documentação de referência do [Modelo da Série Temporal.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)
