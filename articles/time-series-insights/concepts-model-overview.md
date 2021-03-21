---
title: Modelo da Série De Tempo - Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o modelo da Série De Tempo em Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 37c24f2d785bbdd9847e0dadaa47969f5b090ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698098"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Modelo de série de tempo em Azure Time Series Insights Gen2

Este artigo descreve o Modelo da Série de Tempo, as capacidades e como começar a construir e atualizar os seus próprios modelos no ambiente Azure Time Series Insights Gen2.

> [!TIP]
>
> * Vá ao ambiente [de demonstração do Parque Eólico Contoso](https://insights.timeseries.azure.com/preview/samples) para um exemplo ao vivo da Série Time.
> * Aprenda a trabalhar com o [Modelo da Série De Tempo](./how-to-edit-your-model.md) utilizando o Azure Time Series Insights Explorer.

## <a name="summary"></a>Resumo

Tradicionalmente, os dados recolhidos a partir de dispositivos IoT carecem de informação contextual, o que dificulta a localização e análise rápida dos sensores. A principal motivação para o Modelo série de tempo é simplificar a descoberta e análise de dados de IoT ou Séries de Tempo. Alcança este objetivo, permitindo a cura, manutenção e enriquecimento de dados de séries temporinhas para ajudar a preparar conjuntos de dados prontos para o consumidor para análise.

## <a name="scenario-contosos-new-smart-oven"></a>Cenário: o novo forno inteligente de Contoso

**Considere o cenário fictício de um forno inteligente Contoso.** Neste cenário, suponha que cada forno inteligente Contoso tem cinco sensores de temperatura, um para cada um dos quatro queimadores superiores e um para o próprio forno. Até recentemente, cada sensor de temperatura Contoso enviou, armazenou e visualizava os seus dados individualmente. Para a sua monitorização de eletrodomésticos de cozinha, Contoso baseou-se em gráficos básicos, um para cada sensor individual.

Enquanto Contoso estava satisfeito com a sua solução inicial de dados e visualização, várias limitações tornaram-se evidentes:

* Os clientes queriam saber o quão quente o forno geral ficaria quando a maioria dos queimadores de topo estavam ligados. Contoso teve mais dificuldade em analisar e apresentar uma resposta unificada sobre as condições do forno geral.
* Os engenheiros da Contoso queriam verificar se os queimadores de topo que estão a ser executados simultaneamente não resultariam numa desatenção de energia ineficiente. Havia dificuldade em cruzar referências que sensores de temperatura e tensão estavam associados uns aos outros e como localizá-los na loja.
* A equipa de garantia de qualidade da Contoso quis auditar e comparar o histórico entre duas versões sensoriais. Havia dificuldade em determinar a que dados pertenciam à versão sensor.

Sem a capacidade de estruturar, organizar e definir o modelo abrangente de séries de tempo de forno inteligente, cada sensor de temperatura manteve pontos de dados deslocados, isolados e menos informativos. A transformação destes pontos de dados em insights acccáveis foi mais difícil, uma vez que cada conjunto de dados vivia independentemente dos outros.

Estas limitações revelaram a importância das ferramentas inteligentes de agregação e visualização de dados para acompanhar o novo forno de Contoso:

* A visualização de dados revela-se útil quando é capaz de associar e combinar dados numa visão conveniente. Um exemplo é mostrar sensores de tensão juntamente com sensores de temperatura.
* Gerir dados multidimensionais para várias entidades juntamente com funcionalidades de comparação, zooming e intervalo de tempo pode ser difícil de realizar.

**O Modelo série de tempo fornece uma solução conveniente** para muitos dos cenários encontrados neste exemplo fictício:

[![Exemplo de gráfico inteligente de forno de série de tempo](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* O Modelo de Séries de Tempo desempenha um papel vital nas consultas e na navegação porque contextualiza os dados, permitindo que as comparações sejam desenhadas através dos intervalos de tempo e entre os tipos de sensores e dispositivos. **(A)**
* Os dados são ainda contextualizados porque os dados persistidos no Time Series Model preserva os cálculos de consultas de séries temporizadas como variáveis e reutiliza-os no tempo de consulta.
* O Modelo de Séries de Tempo organiza e agrega dados para melhorar as capacidades de visualização e gestão. **(B)**

### <a name="key-capabilities"></a>Principais capacidades

Com o objetivo de torná-lo simples e sem esforço para gerir a contextualização de séries de tempo, o Time Series Model permite as seguintes capacidades em Azure Time Series Insights Gen2. Ajuda-o:

* Autor e gerir computações ou fórmulas aproveitando funções escalar, operações agregadas, e assim por diante.
* Defina relações pai-filho para permitir a navegação, pesquisa e referência.
* Defina propriedades que estejam associadas aos casos, definidas como *campos de exemplo,* e use-as para criar hierarquias.

### <a name="components"></a>Componentes

O Modelo série de tempo tem três componentes principais:

* [Exemplos de modelos de séries de tempo](#time-series-model-instances)
* [Hierarquias modelo séries de tempo](#time-series-model-hierarchies)
* [Tipos de modelos de séries de tempo](#time-series-model-types)

Estes componentes são combinados para especificar um modelo de série sonora e para organizar os seus dados.

[![Gráfico de visão geral do modelo de série de tempo](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Um modelo de série sonora pode ser criado e gerido através do [Azure Time Series Insights Explorer](./concepts-ux-panels.md). As definições do modelo série de tempo podem ser geridas através da [API de Definições](/rest/api/time-series-insights/reference-model-apis)de Modelos .

## <a name="time-series-model-instances"></a>Exemplos de modelos de séries de tempo

As instâncias do Modelo de *Séries de* Tempo são representações virtuais das próprias séries tempor.000.

Na maioria dos casos, os casos são identificados exclusivamente por **deviceId** ou **assetId**, que são guardados como IDs de séries temporizadas.

Os casos têm informações descritivas associadas a eles chamadas *propriedades de instância*, tais como um ID de série sonora, tipo, nome, descrição, hierarquias e campos de instância. No mínimo, as propriedades da instância incluem informações de hierarquia.

*Os campos* de exemplo são uma coleção de informações descritivas que podem incluir valores para níveis de hierarquia, bem como fabricante, operador, e assim por diante.

Depois de uma fonte de evento ser configurada para o ambiente Azure Time Series Insights Gen2, as instâncias são automaticamente descobertas e criadas num modelo de série sonora. Os casos podem ser criados ou atualizados através do Azure Time Series Insights Explorer utilizando consultas de Modelos de Séries Temporais.

A [demonstração do Parque Eólico Contoso](https://insights.timeseries.azure.com/preview/samples) fornece vários exemplos de instâncias ao vivo.

[![Exemplo de exemplo de modelo de série de tempo](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Propriedades de exemplo

As instâncias são definidas por **timeSeriesId**, **typeId**, **name**, **description,** **hierarchyIds**, e **instanceFields**. Cada instância mapeia apenas um *tipo*, e uma ou mais *hierarquias*.

| Propriedade | Descrição |
| --- | ---|
| timeSeriesId | O ID único da série-tempo com que o caso está associado. Na maioria dos casos, os casos são identificados exclusivamente por uma propriedade como deviceId ou assetId. Em alguns casos, pode ser usado um ID compósito mais específico que combina até 3 propriedades. |
| typeId | O ID de cadeia único sensível ao caso do modelo série de tempo a que o caso está associado. Por padrão, todos os novos casos descobertos ficam associados a um tipo padrão.
| name | A propriedade **do nome** é opcional e sensível a maiôs. Se o **nome** não estiver disponível, está em incumprimento do **timeSeriesId**. Se um nome for fornecido, **o TimeSeriesId** ainda está disponível no [poço](./concepts-ux-panels.md#4-time-series-well). |
| descrição | Uma descrição do texto do caso. |
| hierarquias | Define a que hierarquias pertence o caso. |
| casoFields | As propriedades de um caso e quaisquer dados estáticos que definem um caso. Definem valores de propriedades de hierarquia ou não-hierarquia, ao mesmo tempo que apoiam a indexação para realizar operações de busca. |

> [!NOTE]
> As hierarquias são construídas usando campos de exemplo. **Instância adicionalFields** pode ser adicionado para definições de propriedade de mais exemplo.

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
> Por exemplo, a API cria, lê, atualiza e elimina o suporte (CRUD), lê-se o artigo [de consulta de dados](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) e a documentação relativa à [API REST](/rest/api/time-series-insights/reference-model-apis#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarquias modelo séries de tempo

As *hierarquias* do Modelo série de tempo organizam instâncias especificando nomes de propriedade e suas relações.

Você pode configurar várias hierarquias em um dado ambiente Azure Time Series Insights Gen2. Uma instância modelo de séries de tempo pode mapear para uma única hierarquia ou múltiplas hierarquias (muitas para muitas relação).

A [demonstração do Parque Eólico Contoso](https://insights.timeseries.azure.com/preview/samples) exibe uma instância padrão e uma hierarquia tipo.

[![Exemplo da hierarquia do modelo da Série de Tempo](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definição de hierarquia

As hierarquias são definidas por **id** hierárquica, **nome** e **fonte.**

| Propriedade | Descrição |
| ---| ---|
| ID | O identificador único para a hierarquia, que é usado, por exemplo, quando se define um caso. |
| name | Uma corda usada para fornecer um nome para a hierarquia. |
| source | Especifica a hierarquia ou caminho organizacional, que é uma ordem de pais e filhos de cima para baixo da hierarquia que os utilizadores querem criar. As propriedades dos pais e filhos mapeiam campos de instância. |

As hierarquias estão representadas no JSON como:

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

No exemplo JSON anterior:

* `Location` define uma hierarquia com pai `states` e `cities` filho. Cada um `location` pode ter `states` vários, que por sua vez pode ter múltiplos `cities` .
* `ManufactureDate` define uma hierarquia com pai `year` e `month` filho. Cada um `ManufactureDate` pode ter `years` vários, que por sua vez pode ter múltiplos `months` .

> [!TIP]
> Para a API hierárquica criar, ler, atualizar e eliminar (CRUD), leia o artigo [de consulta de dados](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e a documentação da [API REST da Hierarquia](/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

### <a name="hierarchy-example"></a>Exemplo de hierarquia

Considere um exemplo onde a hierarquia **H1** `building` `floor` tem, e `room` como parte da sua **definição de ExemploFieldNames:**

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

Tendo em conta os campos de instância utilizados na definição anterior e em várias séries temporárias, os atributos e valores da hierarquia aparecem como indicados no quadro seguinte:

| ID da série de tempo | Campos de exemplo |
| --- | --- |
| ID1 | "Edifício" = "1000", "piso" = "10", "quarto" = "55"  |
| ID2 | "Construção" = "1000", "quarto" = "55" |
| ID3 | "Chão" = "10" |
| ID4 | "Edifício" = "1000", "piso" = "10"  |
| ID5 | Nenhum de "edifício", "chão", ou "quarto" está definido. |

As Séries De Tempo **ID1** e **ID4** são apresentadas como parte da hierarquia **H1** no [Azure Time Series Insights Explorer,](./concepts-ux-panels.md) porque eles têm *um edifício* totalmente definido e corretamente ordenado, *piso,* e parâmetros *de sala.*

Os outros são classificados em *Instâncias NãoParentes* porque não se conformam com a hierarquia de dados especificada.

## <a name="time-series-model-types"></a>Tipos de modelos de séries de tempo

Os tipos de modelos de *séries de* tempo ajudam-no a definir variáveis ou fórmulas para fazer cálculos. Os tipos estão associados a uma instância específica.

Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância modelo de série de tempo pode ser do tipo Sensor de *Temperatura,* que consiste nas variáveis *avg temperatura,* *temperatura min* e *temperatura máxima*.

A [demonstração do Parque Eólico Contoso](https://insights.timeseries.azure.com/preview/samples) visualiza vários tipos de Modelos de Séries Tempotacionadas associadas às respetivas instâncias.

[![Exemplo de modelo de série de tempo](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Para os tipos API criar, ler, atualizar e eliminar (CRUD), leia o artigo [de consulta de dados](concepts-query-overview.md#time-series-model-query-tsm-q-apis) e a [documentação do Tipo API REST](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Tipo de propriedades

Os tipos de modelos de séries de tempo são definidos por **id,** **nome,** **descrição** e **variáveis**.

| Propriedade | Descrição |
| ---| ---|
| ID | O ID de corda único sensível ao caso para o tipo. |
| name | Uma corda usada para fornecer um nome para o tipo. |
| descrição | Uma descrição de cadeia para o tipo. |
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
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Os tipos de modelos de séries de tempo podem ter muitas variáveis que especificam regras de fórmula e computação em eventos. Leia mais sobre [como definir variáveis de modelo de séries de tempo](./concepts-variables.md)

## <a name="next-steps"></a>Passos seguintes

* Leia a documentação de referência do [Modelo série](/rest/api/time-series-insights/reference-model-apis) de tempo para obter mais informações sobre como editar o modelo através de APIs.

* Explore as fórmulas e computações que pode criar com [variáveis modelo de séries de tempo](./concepts-variables.md)

* Saiba mais sobre [a consulta de dados](concepts-query-overview.md) em Azure Time Series Insights Gen2