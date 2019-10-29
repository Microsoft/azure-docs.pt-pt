---
title: Enviar eventos para um ambiente de Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar um hub de eventos e executar um aplicativo de exemplo para enviar eventos por push que você pode exibir em Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2878a77918fdd1c1cd298ae536bcdd3bec065e91
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991134"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente de Time Series Insights usando um hub de eventos

Este artigo explica como criar e configurar um hub de eventos nos hubs de eventos do Azure. Ele também descreve como executar um aplicativo de exemplo para enviar eventos por push a Azure Time Series Insights dos hubs de eventos. Se você tiver um hub de eventos existente com eventos no formato JSON, pule este tutorial e exiba seu ambiente em [Azure Time Series insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para saber como criar um hub de eventos, consulte a [documentação dos hubs de eventos](https://docs.microsoft.com/azure/event-hubs/).
1. Na caixa de pesquisa, procure por **hubs de eventos**. Na lista retornada, selecione **hubs de eventos**.
1. Selecione seu hub de eventos.
1. Ao criar um hub de eventos, você está criando um namespace de Hub de eventos. Se você ainda não criou um hub de eventos dentro do namespace, no menu, em **entidades**, crie um hub de eventos.  

    [![lista de hubs de eventos](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, em **entidades**, selecione **hubs de eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Em **visão geral**, selecione **grupos de consumidores**e, em seguida, selecione grupo de **consumidores**.

    [![criar um grupo de consumidores](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Certifique-se de criar um grupo de consumidores que é usado exclusivamente pelo seu Time Series Insights origem do evento.

    > [!IMPORTANT]
    > Verifique se esse grupo de consumidores não é usado por nenhum outro serviço, como um trabalho Azure Stream Analytics ou outro ambiente de Time Series Insights. Se o grupo de consumidores for usado por outros serviços, as operações de leitura serão afetadas negativamente para esse ambiente e para outros serviços. Se você usar **$Default** como o grupo de consumidores, outros leitores poderão potencialmente reutilizar seu grupo de consumidores.

1. No menu, em **configurações**, selecione **políticas de acesso compartilhado**e, em seguida, selecione **Adicionar**.

    [![selecione políticas de acesso compartilhado e, em seguida, selecione o botão Adicionar](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. No painel **Adicionar nova política de acesso compartilhado** , crie um acesso compartilhado chamado **MySendPolicy**. Você usa essa política de acesso compartilhado para enviar eventos nos C# exemplos mais adiante neste artigo.

    [![na caixa nome da política, digite MySendPolicy](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. Em **declaração**, marque a caixa de seleção **Enviar** .

## <a name="add-a-time-series-insights-instance"></a>Adicionar uma instância de Time Series Insights

A atualização de Time Series Insights usa instâncias para adicionar dados contextuais a dados de telemetria de entrada. Os dados são Unidos no momento da consulta usando uma **ID de série temporal**. A **ID da série temporal** para o projeto Windmills de exemplo que usamos mais adiante neste artigo é `id`. Para saber mais sobre as instâncias do time Series insights e a **ID da série temporal**, confira [modelos de série temporal](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Criar uma fonte de eventos de Time Series Insights

1. Se você não tiver criado uma origem do evento, conclua as etapas para [criar uma origem do evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Defina um valor para `timeSeriesId`. Para saber mais sobre a **ID da série temporal**, consulte [modelos de série temporal](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Enviar eventos por push para o exemplo de Windmills

1. Na barra de pesquisa, procure por **hubs de eventos**. Na lista retornada, selecione **hubs de eventos**.

1. Selecione sua instância do hub de eventos.

1. Acesse **políticas de acesso compartilhado** > **MySendPolicy**. Copie o valor da **cadeia de conexão-chave primária**.

    [![copiar o valor da cadeia de conexão de chave primária](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. Aceda a https://tsiclientsample.azurewebsites.net/windFarmGen.html. A URL executa dispositivos simulados do Windmill.
1. Na caixa **cadeia de conexão do hub de eventos** na página da Web, Cole a cadeia de conexão que você copiou no [campo de entrada Windmill](#push-events-to-windmills-sample).
  
    [![colar a cadeia de conexão de chave primária na caixa Cadeia de conexão do hub de eventos](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. Selecione **clique para iniciar**. O simulador gera a instância JSON que você pode usar diretamente.

1. Volte para o Hub de eventos na portal do Azure. Na página **visão geral** , você verá os novos eventos que são recebidos pelo hub de eventos.

    [![uma página de visão geral do hub de eventos que mostra as métricas para o Hub de eventos](media/send-events/7-telemetry.png)](media/send-events/7-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Formas JSON suportadas

### <a name="example-one"></a>Exemplo um

* **Entrada**: um objeto JSON simples.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Saída**: um evento.

    |ID|carimbo de data/hora|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exemplo dois

* **Input**: uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido em um evento.

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

* **Saída**: dois eventos.

    |ID|carimbo de data/hora|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exemplo três

* **Input**: um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON.

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

* **Saída**: dois eventos. O **local** da propriedade é copiado para cada evento.

    |localização|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemplo quatro

* **Input**: um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que as propriedades globais podem ser representadas pelo objeto JSON complexo.

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

* **Saída**: dois eventos.

    |localização|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passos seguintes

- [Exiba seu ambiente](https://insights.timeseries.azure.com) no time Series insights Explorer.

- Leia mais sobre [as mensagens do dispositivo do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
