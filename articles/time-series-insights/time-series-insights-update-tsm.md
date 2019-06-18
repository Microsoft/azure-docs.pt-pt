---
title: Pré-visualização do modelo de série de tempo no Azure Time Series Insights | Documentos da Microsoft
description: Modelo de série de tempo de noções básicas sobre o Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 3e6e8ae76c0ae6f688dd4a039b34c52af16b6e0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244015"
---
# <a name="time-series-model"></a>Modelo de Série de Tempo

Este artigo descreve a parte do modelo de série de tempo da pré-visualização do Azure Time Series Insights. Ele aborda o modelo em si, as suas capacidades e como começar a criar e para atualizar o seu próprio modelo.

Tradicionalmente, os dados que são recolhidos a partir de dispositivos IoT não possui informações contextuais, o que torna difícil localizar e analisar rapidamente os sensores. A principal motivação para o modelo de série de tempo é simplificar a localizar e analisar os dados de IoT. Este objetivo é alcançado, permitindo que a organização, manutenção e enriquecimento de dados de séries de tempo para o ajudar a preparar preparado para o consumidor conjuntos de dados.

Modelos de série de tempo desempenham um papel fundamental em consultas e navegação porque eles contextualizar os dispositivos e o dispositivo não entidades. Dados que manteve no modelo de série de tempo alimentam os cálculos de consulta de séries de tempo ao tirar partido das fórmulas armazenados nas mesmas.

[![Descrição geral do modelo de série de tempo](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Principais capacidades

Com o objetivo para que seja simples e sem esforço para gerir contextualization de série de tempo, o modelo de série de tempo permite as seguintes funcionalidades na pré-visualização do Time Series Insights. Ajuda-o a:

* Criar e gerir computações ou fórmulas, transformar dados tirar partido das funções escalares, Agregar operações e assim por diante.
* Defina relações pai-filho para ativar a navegação e de referência e fornecer contexto para a telemetria de série de tempo.
* Definir propriedades que estão associadas a parte de instâncias da *campos de instância* e usá-los para criar hierarquias.

## <a name="entity-components"></a>Componentes de entidade

Modelos de série de tempo tem três componentes principais:

* <a href="#time-series-model-types">Tipos de modelo de série de tempo</a>
* <a href="#time-series-model-hierarchies">Hierarquias de modelo de série de tempo</a>
* <a href="#time-series-model-instances">Instâncias do modelo de série de tempo</a>

Esses componentes são combinados para especificar um modelo de série de tempo e organizar os dados do Azure Time Series Insights.

## <a name="time-series-model-types"></a>Tipos de modelo de série de tempo

Modelo de série de tempo *tipos* ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos estão associados uma instância específica do Time Series Insights. Um tipo pode ter uma ou mais variáveis. Por exemplo, uma instância do Time Series Insights pode ser do tipo *Sensor de temperatura*, que consiste as variáveis *temperatura média*, *min temperatura*e *máximo de temperatura*. Vamos criar um tipo de padrão quando os dados começaram a fluir para o Time Series Insights. O tipo de padrão pode ser obtido e atualizado de definições do modelo. Tipos predefinidos de ter uma variável que conte o número de eventos.

### <a name="time-series-model-type-json-example"></a>Exemplo de JSON de tipo de modelo de série de tempo

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

Para obter mais informações sobre os tipos de modelo de série de tempo, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Variáveis

Tipos de informações de série de tempo tem variáveis, que são nomeadas cálculos sobre os valores dos eventos. Definições de variável do Time Series Insights contêm regras de fórmula e computação. As definições de variável incluem *tipo*, *valor*, *filtro*, *redução*, e *limites*. As variáveis são armazenadas na definição de tipo no modelo de série de tempo e podem ser fornecidas inline através de APIs de consulta para substituir a definição armazenada.

A matriz seguinte funciona como uma legenda para as definições de variável:

[![Tabela de definição de variável de tipo](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definição | Descrição |
| --- | ---|
| Tipo de variável |  *Numérico* e *agregado* tipos são suportados |
| Filtro de variável | Filtros de variável especifique uma cláusula de filtro opcional para restringir o número de linhas a ser considerado para computação com base em condições. |
| Valor da variável | Os valores das variáveis são e devem ser usados no cálculo. O campo relevante para fazer referência para a ponto de dados em questão. |
| Agregação de variável | A função de agregação da variável permite que a parte de computação. Time Series Insights suporta agregações regulares (ou seja, ele *min*, *máximo*, *média*, *soma*, e *contagem*). |

## <a name="time-series-model-hierarchies"></a>Hierarquias de modelo de série de tempo

Hierarquias de organizam instâncias ao especificar os nomes das propriedades e suas relações. Pode ter uma hierarquia única ou várias hierarquias. Não precisam de ser uma parte atual dos seus dados, mas cada instância deve ser mapeada para uma hierarquia. Uma instância do modelo de série de tempo pode mapear para uma hierarquia única ou várias hierarquias.

Hierarquias são definidas pelas *ID de hierarquia*, *nome*, e *origem*. Hierarquias de ter um caminho, o que é uma ordem de cima para baixo principal-subordinado da hierarquia que os usuários querem criar. O mapa de propriedades de principal-subordinado *campos de instância*.

### <a name="time-series-model-hierarchy-json-example"></a>Exemplo de JSON de hierarquia de modelo de série de tempo

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

Para obter mais informações sobre as hierarquias de modelo de série de tempo, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Comportamento de definição de hierarquia

Considere o exemplo seguinte, em que tem a hierarquia H1 *criando*, *piso*, e *sala* como parte de sua definição:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Consoante a *campos de instância*, os valores de atributos de hierarquia e aparecem como mostrado na tabela a seguir:

| ID de série de tempo | Campos de instância |
| --- | --- |
| ID1 | "Criar" = "1000", "piso" = "10", "espaço" = "55"  |
| ID2 | "Criar" = "1000", "espaço" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "Criar" = "1000", "piso" = "10"  |
| ID5 | Nenhuma das "Criar", "floor" ou "espaço" está definida |

No exemplo anterior, **ID1** e **ID4** Mostrar como parte da hierarquia H1 no Explorador do Azure Time Series Insights, e como o rest *instâncias Unparented* porque eles não estão em conformidade com a hierarquia de dados especificada.

## <a name="time-series-model-instances"></a>Instâncias do modelo de série de tempo

As instâncias são a série de tempo propriamente ditas. Na maioria dos casos, o *deviceId* ou *assetId* é o identificador exclusivo do recurso no ambiente. As instâncias têm informações descritivas associadas a eles chamado propriedades de instância. No mínimo, as propriedades de instância incluem informações de hierarquia. Eles também podem incluir dados úteis e descritivos, como o fabricante, o operador ou a última data de serviço.

Instâncias são definidas pelas *typeId*, *timeSeriesId*, *nome*, *Descrição*, *hierarchyIds* , e *instanceFields*. Cada instância é mapeado para um único *tipo*e um ou mais hierarquias. Instâncias de herdam de todas as propriedades de hierarquias e outras *instanceFields* podem ser adicionados para definição de propriedade de instância ainda mais.

*instanceFields* são propriedades de uma instância e quaisquer dados estáticos que define uma instância. Elas definem valores de propriedades de hierarquia ou não da hierarquia, também dar suporte a indexação para realizar operações de pesquisa.

O *nome* propriedade é opcional e diferencia maiúsculas de minúsculas. Se *nome* é não está disponível, a predefinição será a série de tempo de ID. Se um *nome* for fornecido, o ID de série de tempo continuarão a estar disponível no poço (a grade abaixo os gráficos no explorer).

### <a name="time-series-model-instance-json-example"></a>Exemplo de JSON de instância de modelo de série de tempo

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

Para obter mais informações sobre as instâncias de modelo de série de tempo, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Exemplo de definições do modelo de série de tempo

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

Para obter mais informações sobre as definições do modelo de série de tempo, consulte a [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Passos Seguintes

- Ver [armazenamento de pré-visualização do Azure Time Series Insights e entrada](./time-series-insights-update-storage-ingress.md).

- Ver a nova [modelo de série de tempo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).