---
title: Adicionar suporte para tipo de dados longos ! Microsoft Docs
description: Suporte para tipo de dados longos
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: dpalled
ms.openlocfilehash: c31ca7fd3eca89159d583b8a51b59a7bd6b8ed67
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531937"
---
# <a name="adding-support-for-long-data-type"></a>Adicionar suporte para um tipo de dados longo

Estas alterações serão aplicadas apenas aos ambientes da Gen2. Se tiver um ambiente da Gen1, pode ignorar estas mudanças.

Estamos a fazer alterações na forma como armazenamos e indexamos dados numéricos na Azure Time Series Insights Gen2 que podem impactar-te. Se for afetado por algum dos casos abaixo, faça as alterações necessárias o mais rápido possível. Os seus dados começarão a ser indexados como Longo e Duplo entre 29 de junho e 30 de junho de 2020, dependendo da sua região. Se tiver alguma dúvida ou preocupação com esta alteração, envie um bilhete de apoio através do portal Azure e mencione esta comunicação.

Esta alteração tem impacto nos seguintes casos:

1. Se utilizar atualmente variáveis do Modelo série de tempo e enviar apenas tipos de dados integrais nos seus dados de telemetria.
1. Se utilizar atualmente variáveis do Modelo série de tempo e enviar dados integrais e não-culturais nos seus dados de telemetria.
1. Se utilizar variáveis categóricas para mapear valores inteiros para categorias.
1. Se utilizar o SDK JavaScript para construir uma aplicação frontal personalizada.
1. Se estiver perto do limite de 1.000 nomes de propriedade na Warm Store (WS) e enviar dados integrais e não culturais, a contagem de propriedades pode ser vista como uma métrica no [portal Azure.](https://portal.azure.com/)

Se algum dos casos acima referidos se aplicar a si, terá de fazer alterações no seu modelo para acomodar esta alteração. Atualize a Expressão da Série De Tempo na sua definição variável tanto no Azure Time Series Insights Gen2 Explorer como em qualquer cliente personalizado que utilize as nossas APIs com as alterações recomendadas. Veja abaixo os detalhes.

Dependendo da sua solução e constrangimentos IoT, poderá não ter visibilidade nos dados enviados para o seu ambiente Azure Time Series Insights Gen2. Se não tiver a certeza se os seus dados são apenas integrais ou integrais e não,tem algumas opções. Pode esperar que a funcionalidade seja lançada e, em seguida, explorar os seus eventos crus na UI exploradora para entender quais propriedades foram guardadas em duas colunas separadas. Você pode fazer preventivamente as alterações abaixo para todas as tags numéricas, ou temporariamente encaminhar um subconjunto de eventos para armazenamento para entender melhor e explorar o seu esquema. Para armazenar eventos, ligue a captura de [eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) para Os Centros de Eventos ou [a rota](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) do seu IoT Hub para o Azure Blob Storage. Os dados também podem ser observados através do [Event Hub Explorer,](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)ou utilizando o [Anfitrião do Processador de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Se utilizar o IoT Hub, consulte a documentação [aqui](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) sobre como aceder ao ponto final incorporado.

Note que se for afetado por estas alterações e não conseguir fazê-las nas datas acima referidas, poderá sofrer uma perturbação em que as variáveis de séries de tempo impactadas acedidas através da consulta APIs ou Time Series Insights Explorer retornam *nulas* (ou seja, não mostram dados no Explorer).

## <a name="recommended-changes"></a>Alterações recomendadas

Caso 1 & 2: **Utilizar variáveis do Modelo série de tempo e enviar apenas tipos de dados integrais OU enviando tipos integrais e não-culturais em dados de telemetria.**

Se estiver a enviar dados inteiros de telemetria, os seus dados serão divididos em duas colunas: "propertyValue_double" e "propertyValue_long".

Os seus dados inteiros serão escritos para "propertyValue_long" quando as alterações entrarem em vigor e os dados numéricos previamente ingeridos (e ingeridos no futuro) em "propertyValue_double" não serão copiados.

Se desejar consultar dados através destas duas colunas para a propriedade "propertyValue", terá de utilizar a função de escalar de *coalesce* no seu TSX. A função aceita argumentos do mesmo DataType e devolve o primeiro valor não nulo na lista de argumentos (leia mais sobre a utilização [aqui).](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Definição variável no Explorador de Séries tempotaciona - Numérico

*Definição variável anterior:*

[![Definição variável anterior](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nova Definição Variável:*

[![Nova Definição Variável](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Também pode utilizar *"coalesce($event.propertyValue.Double, toDouble ($event.propertyValue.Long)) "* como expressão de [série sonora personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Definição variável inline usando APIs de consulta de série de tempo - numérico

*Definição variável anterior:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Nova Definição Variável:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Também pode utilizar *"coalesce($event.propertyValue.Double, toDouble ($event.propertyValue.Long)) "* como expressão de [série sonora personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Recomendamos que atualize estas variáveis em todos os locais onde podem ser utilizadas (Modelo série de tempo, consultas guardadas, consultas de conector Power BI).

Caso 3: **Utilização de variáveis categóricas para mapear valores inteiros para categorias**

Se está atualmente a utilizar variáveis categóricas que mapeiam valores inteiros para categorias, é provável que utilize a função toLong para converter dados do tipo Duplo para o tipo Longo. Tal como nos casos acima, terá de colidirár as colunas DataType duplo e longo.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definição variável no Explorador de Séries tempotacionais - Categórico

*Definição variável anterior:*

[![Definição variável anterior](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nova Definição Variável:*

[![Nova Definição Variável](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Também pode utilizar *"coalesce($event.propertyValue.Double, toDouble ($event.propertyValue.Long)) "* como expressão de [série sonora personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

As variáveis categóricas ainda requerem que o valor seja de um tipo inteiro. O DataType de todos os argumentos no coalesce() deve ser do tipo Longo na expressão de [série de tempo personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Definição variável inline usando APIs de consulta de séries de tempo - Categórico

*Definição variável anterior:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Nova Definição Variável:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

As variáveis categóricas ainda requerem que o valor seja de um tipo inteiro. O DataType de todos os argumentos no coalesce() deve ser do tipo Longo na expressão de [série de tempo personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Recomendamos que atualize estas variáveis em todos os locais onde podem ser utilizadas (Modelo série de tempo, consultas guardadas, consultas de conector Power BI).

Caso 4: **Utilização do SDK JavaScript para construir uma aplicação frontal personalizada**

Se for afetado por casos 1-3 acima e construir aplicações personalizadas, precisa atualizar as suas consultas para utilizar a função *coalesce()* como demonstrado nos exemplos acima.

Caso 5: **Perto da Loja Quente 1.000 limite de propriedade**

Se você é um utilizador da Warm Store com um grande número de propriedades e acredita que esta mudança iria empurrar o seu ambiente sobre o limite de 1.000 WS propriedade-name, enviar um bilhete de apoio através do portal Azure e mencionar esta comunicação.

## <a name="next-steps"></a>Próximos passos

* Consulte [tipos de dados suportados](concepts-supported-data-types.md) para visualizar a lista completa de tipos de dados suportados.
