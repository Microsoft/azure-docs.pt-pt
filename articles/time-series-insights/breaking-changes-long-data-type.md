---
title: Suporte para um tipo de dados longo em Azure Time Series Insights Gen2 Microsoft Docs
description: Suporte para um longo tipo de dados em Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780829"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Adicionar suporte para um longo tipo de dados em Azure Time Series Insights Gen2

A adição de suporte para o tipo de dados longos afeta a forma como armazenamos e indexamos dados numéricos apenas nos ambientes Azure Time Series Insights Gen2. Se tiver um ambiente gen1, pode ignorar estas mudanças.

A partir de 29 de junho ou 30 de junho de 2020, dependendo da sua região, os seus dados serão indexados como **Longo** e **Duplo.**  Se tiver alguma dúvida ou preocupação com esta alteração, envie um bilhete de apoio através do portal Azure e mencione esta comunicação.

Se for afetado por algum dos seguintes casos, faça as alterações recomendadas:

- **Caso 1**: Atualmente utiliza variáveis modelo séries de tempo e envia apenas tipos de dados integrais nos seus dados de telemetria.
- **Caso 2**: Atualmente utiliza variáveis modelo séries de tempo e envia tipos de dados integrais e não-culturais nos seus dados de telemetria.
- **Caso 3**: Utiliza variáveis categóricas para mapear valores inteiros para categorias.
- **Caso 4**: Utilize o SDK JavaScript para construir uma aplicação frontal personalizada.
- **Caso 5**: Está a aproximar-se do limite de 1.000 nomes de propriedade na Warm Store e a enviar dados integrais e não-avós. A contagem de imóveis pode ser vista como uma métrica no [portal Azure.](https://portal.azure.com/)

Se algum dos casos se aplicar a si, faça alterações no seu modelo. Atualize a Expressão série de tempo (TSX) na sua definição variável com as alterações recomendadas. Atualizar ambos:

- Azure Time Series Insights Explorer
- Qualquer cliente personalizado que use os nossos APIs

Dependendo da sua solução e restrições IoT, pode não ter visibilidade nos dados enviados para o seu ambiente Azure Time Series Insights Gen2. Se não tiver a certeza se os seus dados são apenas integrais ou integrais e não,tem algumas opções:

- Pode esperar que a funcionalidade seja lançada. Em seguida, explore os seus eventos crus na UI explorador para entender quais propriedades são guardadas em duas colunas separadas.
- Pode fazer preventivamente as alterações recomendadas para todas as etiquetas numéricas.
- Você pode temporariamente encaminhar um subconjunto de eventos para armazenamento para entender melhor e explorar o seu esquema.

Para armazenar eventos, ligue a captura de [eventos](../event-hubs/event-hubs-capture-overview.md) para Azure Event Hubs ou [vá](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) desde o seu IoT Hub até ao armazenamento Azure Blob.

Os dados também podem ser observados através do [Event Hub Explorer,](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)ou utilizando o [Anfitrião do Processador de Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events).

Se utilizar o IoT Hub, vá a [ler mensagens dispositivo-a-nuvem a partir do ponto final incorporado](../iot-hub/iot-hub-devguide-messages-read-builtin.md) para saber como aceder ao ponto final incorporado.

> [!NOTE]
> Pode sofrer uma perturbação se não fizer as alterações recomendadas. Por exemplo, as variáveis Time Series Insights afetadas que são acedidas através da consulta APIs ou Time Series Insights explorer retornarão **nulas** (isto é, não mostram nenhum dado no explorador).

## <a name="recommended-changes"></a>Alterações recomendadas

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Caso 1: Utilizar variáveis modelo séries de tempo e enviar apenas tipos de dados integrais em dados de telemetria

As alterações recomendadas para o Caso 1 são as mesmas que para o Caso 2. Siga as instruções na secção do caso 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Caso 2: Utilização de variáveis modelo de série de tempo e envio de tipos integrais e não-culturais em dados de telemetria

Se enviar atualmente dados inteiros de telemetria, os seus dados serão divididos em duas colunas:

- **propertyValue_double**
- **propertyValue_long**

Os seus dados inteiros escrevem para **propertyValue_long**. Dados numéricos previamente ingeridos (e ingeridos no futuro) em **propertyValue_double** não são copiados.

Se quiser consultar dados através destas duas colunas para a **propriedadeValue,** precisa de utilizar a função de escalar de **coalesce** no seu TSX. A função aceita argumentos do mesmo **DataType** e devolve o primeiro valor não nulo na lista de argumentos. Para obter mais informações, consulte os [conceitos de acesso a dados da Azure Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Definição variável em TSX - numérico

*Definição variável anterior:*

[![A screenshot mostra a adicionar uma nova caixa de diálogo variável para a Variável PropertyValue, numérica.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nova definição variável:*

[![A screenshot mostra a adicionar uma nova caixa de diálogo variável para a Variável PropertyValue com um valor personalizado, numérico.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Também pode utilizar **coalesce ($event.propertyValue.Double, toDouble ($event.propertyValue.Long))** como expressão de [série sonora](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizada .

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Definição variável inline usando APIs de consulta TSX - numérico

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

*Nova definição variável:*

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

Também pode utilizar **coalesce ($event.propertyValue.Double, toDouble ($event.propertyValue.Long))** como expressão de [série sonora](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizada .

> [!NOTE]
> Recomendamos que atualize estas variáveis em todos os locais onde possam ser utilizadas. Estes locais incluem Modelo de Séries de Tempo, consultas guardadas e consultas de conector Power BI.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Caso 3: Utilização de variáveis categóricas para mapear valores inteiros para categorias

Se atualmente utilizar variáveis categóricas que mapeiam valores inteiros para categorias, é provável que utilize a função **toLong** para converter dados do tipo **Duplo** para o tipo **Longo.** Tal como os casos 1 e 2, é necessário colidír as colunas **DataType** **duplo** e **longo.**

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definição variável no Time Series Explorer - categórico

*Definição variável anterior:*

[![A screenshot mostra a adicionar uma nova caixa de diálogo variável para a Variável PropertyValue, categórica.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nova definição variável:*

[![A screenshot mostra a adicionar uma nova caixa de diálogo variável para a Variável PropertyValue com um valor personalizado, categórico.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Também pode utilizar **coalesce ($event.propertyValue.Double, toDouble ($event.propertyValue.Long))** como expressão de [série sonora](/rest/api/time-series-insights/preview#time-series-expression-and-syntax)personalizada .

As variáveis categóricas ainda requerem que o valor seja de um tipo inteiro. O **DataType** de todos os argumentos no **coalesce()** deve ser do tipo **Longo** na expressão de [série de tempo personalizada.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Definição variável inline usando APIs de consulta TSX - categórico

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

*Nova definição variável:*

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

As variáveis categóricas ainda requerem que o valor seja de um tipo inteiro. O **DataType** de todos os argumentos na **coalesce()** deve ser do tipo **Longo** na expressão de [série de tempo](/rest/api/time-series-insights/reference-time-series-expression-syntax)personalizada .

> [!NOTE]
> Recomendamos que atualize estas variáveis em todos os locais onde possam ser utilizadas. Estes locais incluem Modelo de Séries de Tempo, consultas guardadas e consultas de conector Power BI.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Caso 4: Utilização do SDK JavaScript para construir uma aplicação frontal personalizada

Se for afetado pelos casos 1 a 3 e construir aplicações personalizadas, tem de atualizar as suas consultas para utilizar a função **coalesce()** como demonstrado nos exemplos anteriores.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Caso 5: Perto da Loja Quente 1.000 limite de propriedade

Se você é um utilizador da Warm Store com um grande número de propriedades e acredita que esta mudança iria empurrar o seu ambiente sobre o limite de 1.000 propriedade Warm Store, envie um bilhete de apoio através do portal Azure e mencione esta comunicação.

## <a name="next-steps"></a>Passos seguintes

- Veja a lista completa dos tipos de [dados suportados.](concepts-supported-data-types.md)
