---
title: Modelo de série temporal na visualização de Azure Time Series Insights | Microsoft Docs
description: Compreendendo Azure Time Series Insights modelo de série temporal.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034057"
---
# <a name="time-series-model"></a>Modelo de Série de Tempo

Este artigo descreve a parte do modelo de série temporal da versão prévia do Azure Time Series Insights. Ele aborda o próprio modelo, seus recursos e como começar a criar e atualizar seu próprio modelo.

Tradicionalmente, os dados coletados de dispositivos IoT não têm informações contextuais, o que dificulta a localização e a análise rápida de sensores. A principal motivação para o modelo de série temporal é simplificar a localização e a análise de dados de IoT. Ele atinge esse objetivo ao habilitar a consulta, a manutenção e o enriquecimento de dados de série temporal para ajudar a preparar conjuntos de os clientes prontos para o consumidor.

Os modelos de série temporal desempenham um papel vital em consultas e navegação porque eles contextualizem entidades de dispositivo e não dispositivo. Os dados persistidos no modelo de série temporal impulsionam as computações de consulta de série temporal aproveitando as fórmulas armazenadas nelas.

[Visão geral do modelo da série @no__t 1Time](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Principais capacidades

Com o objetivo de tornar simples e fácil gerenciar a congestão de séries temporais, o modelo de série temporal permite os seguintes recursos na visualização Time Series Insights. Ele ajuda você a:

* Crie e gerencie cálculos ou fórmulas, transforme dados que aproveitam funções escalares, operações de agregação e assim por diante.
* Defina relações pai-filho para habilitar a navegação e a referência e fornecer contexto para telemetria de série temporal.
* Defina as propriedades associadas à parte de instâncias de *campos de instância* e use-as para criar hierarquias.

## <a name="entity-components"></a>Componentes de entidade

Os modelos de série temporal têm três componentes principais:

* <a href="#time-series-model-types">Tipos de modelo de série temporal</a>
* <a href="#time-series-model-hierarchies">Hierarquias de modelo de série temporal</a>
* <a href="#time-series-model-instances">Instâncias de modelo de série temporal</a>

Esses componentes são combinados para especificar um modelo de série temporal e para organizar seus dados de Azure Time Series Insights.

## <a name="time-series-model-types"></a>Tipos de modelo de série temporal

Os *tipos* de modelo de série temporal ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica de Time Series Insights. Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância de Time Series Insights pode ser do tipo *sensor de temperatura*, que consiste nas variáveis de *temperatura média*, *mínimo*e temperatura *máxima*. Criamos um tipo padrão quando os dados começam a fluir para Time Series Insights. O tipo padrão pode ser recuperado e atualizado com base nas configurações do modelo. Os tipos padrão têm uma variável que conta o número de eventos.

### <a name="time-series-model-type-json-example"></a>Exemplo de JSON do tipo de modelo de série temporal

Exemplo:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Para obter mais informações sobre tipos de modelo de série temporal, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Variáveis

Os tipos de Time Series Insights têm variáveis, que são cálculos nomeados sobre valores dos eventos. Time Series Insights definições de variáveis contêm fórmulas e regras de computação. As definições de variáveis incluem *tipo*, *valor*, *filtro*, *redução*e *limites*. As variáveis são armazenadas na definição de tipo no modelo de série temporal e podem ser fornecidas embutidas por meio de APIs de consulta para substituir a definição armazenada.

A matriz a seguir funciona como uma legenda para definições de variáveis:

[tabela de definição de variável ![Type](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definição | Descrição |
| --- | ---|
| Tipo de variável |  Há suporte para tipos *numéricos* e *agregados* |
| Filtro de variável | Filtros de variáveis especificam uma cláusula de filtro opcional para restringir o número de linhas que estão sendo consideradas para computação com base em condições. |
| Valor da variável | Os valores de variáveis são e devem ser usados na computação. O campo relevante para se referir ao ponto de dados em questão. |
| Agregação de variável | A função de agregação da variável habilita parte da computação. O Time Series Insights dá suporte a agregações regulares (isto é, *mín*., *máx*., *média*, *soma*e *contagem*). |

## <a name="time-series-model-hierarchies"></a>Hierarquias de modelo de série temporal

As hierarquias organizam instâncias especificando nomes de propriedade e suas relações. Você pode ter uma única hierarquia ou várias hierarquias. Eles não precisam ser uma parte atual dos seus dados, mas cada instância deve ser mapeada para uma hierarquia. Uma instância de modelo de série temporal pode ser mapeada para uma única hierarquia ou várias hierarquias.

As hierarquias são definidas por *ID de hierarquia*, *nome*e *origem*. As hierarquias têm um caminho, que é uma ordem pai-filho de cima para baixo da hierarquia que os usuários desejam criar. As propriedades pai-filho mapeiam *campos de instância*.

### <a name="time-series-model-hierarchy-json-example"></a>Exemplo de JSON de hierarquia de modelo de série temporal

Exemplo:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Para obter mais informações sobre hierarquias de modelo de série temporal, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Comportamento da definição da hierarquia

Considere o exemplo a seguir em que a hierarquia H1 tem *prédio*, *piso*e *sala* como parte de sua definição:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Dependendo dos campos de *instância*, os valores e atributos de hierarquia aparecem conforme mostrado na tabela a seguir:

| ID da série temporal | Campos de instância |
| --- | --- |
| ID1 | "compilando" = "1000", "piso" = "10", "sala" = "55"  |
| ID2 | "criando" = "1000", "sala" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "criando" = "1000", "Floor" = "10"  |
| ID5 | Nenhum de "prédio", "andar" ou "sala" está definido |

No exemplo anterior, **ID1** e **ID4** mostram como parte da hierarquia H1 no Azure Time Series insights Explorer, e o resto é classificado em instâncias sem *pai* porque eles não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-instances"></a>Instâncias de modelo de série temporal

As instâncias são a própria série temporal. Na maioria dos casos, o *DeviceID* ou *AssetID* é o identificador exclusivo do ativo no ambiente. As instâncias têm informações descritivas associadas a elas chamadas Propriedades de instância. No mínimo, as propriedades de instância incluem informações de hierarquia. Eles também podem incluir dados úteis e descritivos, como o fabricante, o operador ou a data do último serviço.

As instâncias são definidas por *typeId*, *timeseriesid*, *nome*, *Descrição*, *hierarchyIds*e *instanceFields*. Cada instância é mapeada para apenas um *tipo*e uma ou mais hierarquias. As instâncias herdam todas as propriedades de hierarquias e *instanceFields* adicionais podem ser adicionadas para uma definição de propriedade de instância adicional.

*instanceFields* são propriedades de uma instância e de quaisquer dados estáticos que definem uma instância. Eles definem valores de propriedades de hierarquia ou de não hierarquia, além de oferecer suporte à indexação para executar operações de pesquisa.

A propriedade *Name* é opcional e diferencia maiúsculas de minúsculas. Se o *nome* não estiver disponível, ele usará como padrão a ID da série temporal. Se um *nome* for fornecido, a ID da série temporal ainda estará disponível no bem (a grade abaixo dos gráficos no Explorer).

### <a name="time-series-model-instance-json-example"></a>Exemplo de JSON da instância de modelo de série temporal

Exemplo:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Para obter mais informações sobre instâncias de modelo de série temporal, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Exemplo de configurações de modelo de série temporal

Exemplo:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Para obter mais informações sobre configurações de modelo de série temporal, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Passos seguintes

- Consulte [Azure Time Series insights visualização de armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

- Consulte o novo [modelo de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).