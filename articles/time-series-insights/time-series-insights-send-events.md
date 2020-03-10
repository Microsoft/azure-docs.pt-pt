---
title: Envie eventos para um ambiente - Azure Time Series Insights [ Azure Time Series Insights ] Microsoft Docs
description: Aprenda a configurar um centro de eventos, executar uma aplicação de amostra e envie eventos para o seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385321"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente do Time Series Insights com um hub de eventos

Este artigo explica como criar e configurar um centro de eventos em Azure Event Hubs. Também descreve como executar uma aplicação de amostra para empurrar eventos para Azure Time Series Insights de Event Hubs. Se tiver um hub de eventos existente com eventos em formato JSON, ignore este tutorial e veja o seu ambiente em Insights da [Série De Tempo Azure](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para aprender a criar um hub de eventos, leia a documentação do [Event Hubs.](https://docs.microsoft.com/azure/event-hubs/)
1. Na caixa de pesquisa, procure Hubs de **Eventos**. Na lista devolvida, selecione **Event Hubs**.
1. Selecione o seu hub de eventos.
1. Quando crias um centro de eventos, estás a criar um espaço de nome sem nome para eventos. Se ainda não criou um hub de eventos dentro do espaço de nome, no menu, em Entidades, crie um hub de **eventos.**  

    [![Lista de centros de eventos](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, em **Entidades,** selecione **Centros de Eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Em **resumo,** selecione **grupos de consumidores**e, em seguida, selecione **Consumer Group**.

    [![Criar um grupo de consumidores](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Certifique-se de criar um grupo de consumidores que seja utilizado exclusivamente pela sua fonte de eventos Time Series Insights.

    > [!IMPORTANT]
    > Certifique-se de que este grupo de consumidores não é utilizado por nenhum outro serviço, como um trabalho de Azure Stream Analytics ou outro ambiente time series Insights. Se o grupo de consumidores for utilizado por outros serviços, operações de leitura são afetados negativamente para este ambiente e para outros serviços. Se utilizar **$Default** como grupo de consumidores, outros leitores poderão potencialmente reutilizar o seu grupo de consumidores.

1. No menu, em **Definições,** selecione políticas de **acesso partilhados,** e, em seguida, selecione **Adicionar**.

    [![Selecione as políticas de acesso partilhado e, em seguida, selecione o botão Adicionar](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. No novo painel de política de **acesso partilhado Add,** crie um acesso partilhado chamado **MySendPolicy**. Você usa esta política de acesso C# partilhado para enviar eventos nos exemplos mais tarde neste artigo.

    [![Na caixa de nome política, introduza MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Em **'Reclamação',** selecione a caixa de verificação **Enviar.**

## <a name="add-a-time-series-insights-instance"></a>Adicionar uma instância do Time Series Insights

A atualização do Time Series Insights utiliza instâncias para adicionar dados contextuais para dados telemétricos recebidos. Os dados são unidos na hora da consulta utilizando um ID da **Série De Tempo**. O ID da **Série Tempo** para o projeto de moinhos de vento que usamos mais tarde neste artigo é `id`. Para saber mais sobre os casos de Insight da Série Tempo e **id da Série de Tempo,** leia os [Modelos da Série de Tempo](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Criar uma fonte de eventos do Time Series Insights

1. Se ainda não criou uma fonte de evento, complete os passos para criar uma fonte de [evento.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)

1. Detete um valor para `timeSeriesId`. Para saber mais sobre o **ID da Série Tempo,** leia [os Modelos da Série De Tempo](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Empurre eventos para a amostra de moinhos de vento

1. Na barra de pesquisa, procure Hubs de **Eventos.** Na lista devolvida, selecione **Event Hubs**.

1. Selecione a sua instância de centro de eventos.

1. Vá para **as políticas** de acesso partilhado > **MySendPolicy**. Copie o valor da tecla primária de fio de **ligação**.

    [![Copiar o valor para a cadeia de ligação principal](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Aceda a https://tsiclientsample.azurewebsites.net/windFarmGen.html. O URL cria e executa dispositivos simulados de moinho de vento.
1. Na caixa de cordas de **ligação** do Event Hub na página web, cola a cadeia de ligação que copiou no campo de entrada do moinho de [vento](#push-events-to-windmills-sample).
  
    [![Colar a cadeia de ligação principal principal na caixa de cordas de ligação do Hub de Evento](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selecione **Clique para começar**. 

    > [!TIP]
    > O simulador de moinho de vento também cria JSON que pode usar como carga útil com as [APIs](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)de consulta GA Da Série De Tempo .

    > [!NOTE]
    > O simulador continuará a enviar dados até que o separador do navegador esteja fechado.

1. Regresse ao seu hub de eventos no portal do Azure. Na página **'Overview',** os novos eventos recebidos pelo centro do evento são apresentados.

    [![Uma página de visão geral do centro de eventos que mostra métricas para o centro de eventos](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Formas JSON suportadas

### <a name="example-one"></a>Exemplo um

* **Entrada**: Um simples objeto JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Saída**: Um evento.

    |ID|carimbo de data/hora|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exemplo dois

* **Entrada**: Uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido para um evento.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Saída**: Dois eventos.

    |ID|carimbo de data/hora|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exemplo três

* **Entrada**: Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Saída**: Dois eventos. A **localização** da propriedade é copiada para cada evento.

    |localização|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemplo quatro

* **Entrada**: Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que propriedades globais podem ser representadas pelo objeto JSON complexo.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Saída**: Dois eventos.

    |localização|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passos Seguintes

- [Veja o seu ambiente](https://insights.timeseries.azure.com) no explorador time series Insights.

- Ler mais sobre mensagens de [dispositivo IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
