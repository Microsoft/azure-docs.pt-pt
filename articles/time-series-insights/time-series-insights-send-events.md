---
title: Enviar eventos para um ambiente-Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar um hub de eventos, executar um aplicativo de exemplo e enviar eventos para seu ambiente de Azure Time Series Insights.
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
ms.openlocfilehash: cdcd64b5281ce16002720072db3b5f29f1978cac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014834"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente do Time Series Insights com um hub de eventos

Este artigo explica como criar e configurar um hub de eventos nos hubs de eventos do Azure. Ele também descreve como executar um aplicativo de exemplo para enviar eventos por push a Azure Time Series Insights dos hubs de eventos. Se você tiver um hub de eventos existente com eventos no formato JSON, pule este tutorial e exiba seu ambiente em [Azure Time Series insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para saber como criar um hub de eventos, consulte a [documentação dos Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/).
1. Na caixa de pesquisa, procure **os Hubs de eventos**. Na lista devolvida, selecione **os Hubs de eventos**.
1. Selecione o seu hub de eventos.
1. Ao criar um hub de eventos, você está criando um namespace de Hub de eventos. Se você ainda não criou um hub de eventos dentro do namespace, no menu, em **entidades**, crie um hub de eventos.  

    [![lista de hubs de eventos](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, em **entidades**, selecione **hubs de eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Em **visão geral**, selecione **grupos de consumidores**e, em seguida, selecione grupo de **consumidores**.

    [![criar um grupo de consumidores](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Certifique-se de criar um grupo de consumidores que é usado exclusivamente pelo seu Time Series Insights origem do evento.

    > [!IMPORTANT]
    > Verifique se esse grupo de consumidores não é usado por nenhum outro serviço, como um trabalho Azure Stream Analytics ou outro ambiente de Time Series Insights. Se o grupo de consumidores for utilizado por outros serviços, operações de leitura são afetados negativamente para este ambiente e para outros serviços. Se usar **$Default** como o grupo de consumidores, outros leitores potencialmente poderão reutilizar seu grupo de consumidores.

1. No menu, em **configurações**, selecione **políticas de acesso compartilhado**e, em seguida, selecione **Adicionar**.

    [![selecione políticas de acesso compartilhado e, em seguida, selecione o botão Adicionar](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. Na **Adicionar nova política de acesso partilhado** painel, criar um acesso partilhado com o nome **MySendPolicy**. Você usa essa política de acesso compartilhado para enviar eventos nos C# exemplos mais adiante neste artigo.

    [![na caixa nome da política, digite MySendPolicy](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. Em **declaração**, marque a caixa de seleção **Enviar** .

## <a name="add-a-time-series-insights-instance"></a>Adicionar uma instância do Time Series Insights

A atualização do Time Series Insights utiliza instâncias para adicionar dados contextuais para dados telemétricos recebidos. Os dados são associados no momento da consulta ao utilizar um **ID de série de tempo**. A **ID da série temporal** para o projeto Windmills de exemplo que usamos mais adiante neste artigo é `id`. Para saber mais sobre as instâncias de Time Series Insight e **ID de série de tempo**, consulte [modelos de série de tempo](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Criar uma fonte de eventos do Time Series Insights

1. Se não tiver criado uma origem de evento, conclua os passos para [criar uma origem de evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Definir um valor para `timeSeriesId`. Para saber mais sobre **ID de série de tempo**, consulte [modelos de série de tempo](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Enviar eventos por push para o exemplo de Windmills

1. Na barra de pesquisa, procure **os Hubs de eventos**. Na lista devolvida, selecione **os Hubs de eventos**.

1. Selecione sua instância do hub de eventos.

1. Vá para **políticas de acesso compartilhado** > **MySendPolicy**. Copie o valor para **ligação chave primária da cadeia de caracteres**.

    [![copiar o valor da cadeia de conexão de chave primária](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. Aceda a https://tsiclientsample.azurewebsites.net/windFarmGen.html. O URL é executado windmill simulado dispositivos.
1. Na caixa **cadeia de conexão do hub de eventos** na página da Web, Cole a cadeia de conexão que você copiou no [campo de entrada Windmill](#push-events-to-windmills-sample).
  
    [![colar a cadeia de conexão de chave primária na caixa Cadeia de conexão do hub de eventos](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. Selecione **clique aqui para iniciar**. O simulador gera instância JSON que pode utilizar diretamente.

1. Regresse ao seu hub de eventos no portal do Azure. Na página **visão geral** , você verá os novos eventos que são recebidos pelo hub de eventos.

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

* **Input**: uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido para um evento.

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

* **Saída**: dois eventos. A propriedade **localização** é copiada para cada evento.

    |localização|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemplo quatro

* **Input**: um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que propriedades globais podem ser representadas pelo objeto JSON complexo.

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
