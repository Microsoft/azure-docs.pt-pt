---
title: Enviar eventos para um ambiente - Azure Time Series Insights Microsoft Docs
description: Aprenda a configurar um centro de eventos, executar uma aplicação de amostra e enviar eventos para o seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 9a9115b5400cc6d6c1ecc5740af796d831f5dee3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023263"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Envie eventos para um ambiente Azure Time Series Insights Gen1 usando um centro de eventos

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo explica como criar e configurar um centro de eventos em Azure Event Hubs. Também descreve como executar uma aplicação de amostra para empurrar eventos para Azure Time Series Insights de Event Hubs. Se tiver um centro de eventos existente com eventos em formato JSON, ignore este tutorial e veja o seu ambiente em [Azure Time Series Insights](./tutorials-set-up-tsi-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para aprender a criar um centro de eventos, leia a documentação do [Event Hubs.](../event-hubs/index.yml)
1. Na caixa de pesquisa, procure por **Centros de Eventos.** Na lista de devoluções, selecione **Event Hubs**.
1. Selecione o seu centro de eventos.
1. Quando crias um centro de eventos, estás a criar um espaço de nomes de centros de eventos. Se ainda não criou um centro de eventos dentro do espaço de nomes, no menu, em Entidades, crie um centro de **eventos.**  

    [![Lista de centros de eventos](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Depois de criar um centro de eventos, selecione-o na lista de centros de eventos.
1. No menu, em **Entidades,** selecione **Event Hubs**.
1. Selecione o nome do centro de eventos para configurá-lo.
1. Sob **visão geral**, selecione **grupos de consumidores** e, em seguida, selecione **Consumer Group**.

    [![Criar um grupo de consumidores](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Certifique-se de criar um grupo de consumidores que é utilizado exclusivamente pela sua fonte de eventos Azure Time Series Insights.

    > [!IMPORTANT]
    > Certifique-se de que este grupo de consumidores não é utilizado por nenhum outro serviço, como um trabalho Azure Stream Analytics ou outro ambiente Azure Time Series Insights. Se o grupo de consumidores for utilizado pelos outros serviços, as operações de leitura são negativamente afetadas tanto para este ambiente como para outros serviços. Se utilizar **$Default** como grupo de consumidores, outros leitores poderão potencialmente reutilizar o seu grupo de consumidores.

1. No menu, em **Definições**, selecione **Políticas de acesso partilhado** e, em seguida, selecione **Adicionar**.

    [![Selecione políticas de acesso compartilhado e, em seguida, selecione o botão Adicionar](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. No novo painel **de política de acesso partilhado Add,** crie um acesso partilhado chamado **MySendPolicy**. Utilize esta política de acesso partilhado para enviar eventos nos exemplos C# mais tarde neste artigo.

    [![Na caixa de nomes da Política, insira MySendPolicy](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. In **Claim**, selecione a caixa de verificação **Enviar.**

## <a name="add-an-azure-time-series-insights-instance"></a>Adicione uma instância de insights da série de tempo Azure

No Azure Time Series Insights Gen2, pode adicionar dados contextuais à telemetria de entrada utilizando o Modelo de Séries Tempotadas (TSM). Na TSM, as suas etiquetas ou sinais são referidos como *casos,* e pode armazenar dados contextuais em *campos de exemplo.* Os dados são unidos no tempo de consulta utilizando um **ID da Série De Tempo**. O **ID da Série De Tempo** para o projeto de moinhos de vento que usamos mais tarde neste artigo é `id` . Para saber mais sobre o armazenamento de dados em campos de exemplo, leia a visão geral do Modelo série de [tempo.](./concepts-model-overview.md)

### <a name="create-an-azure-time-series-insights-event-source"></a>Criar uma fonte de eventos Azure Time Series Insights

1. Se não criou uma fonte de evento, complete os passos para [criar uma fonte de eventos](./how-to-ingest-data-event-hub.md).

1. Desa fixar um valor para `timeSeriesId` . Para saber mais sobre **o ID da Série de Tempo,** leia [modelos de séries de tempo](./concepts-model-overview.md).

### <a name="push-events-to-windmills-sample"></a>Empurre os eventos para a amostra de moinhos de vento

1. Na barra de pesquisa, procure por **Event Hubs.** Na lista de devoluções, selecione **Event Hubs**.

1. Selecione a sua instância do centro de eventos.

1. Ir para **políticas de acesso compartilhado**  >  **MySendPolicy**. Copie o valor da **tecla primária de ligação**.

    [![Copie o valor para a cadeia de ligação da chave primária](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Aceda a <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. O URL cria e executa dispositivos simulados de moinho de vento.
1. Na caixa **de cordas de ligação** do centro de eventos na página web, cole a cadeia de ligação que copiou no campo de entrada do moinho de [vento](#push-events-to-windmills-sample).
  
    [![Cole a cadeia de ligação principal da chave na caixa de cordas de ligação do centro de eventos](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selecione **Clique para iniciar**.

    > [!TIP]
    > O simulador de moinho de vento também cria JSON que pode usar como uma carga útil com as APIs de [Consulta de Consultas Azure Time Series Insights GA](/rest/api/time-series-insights/gen1-query).

    > [!NOTE]
    > O simulador continuará a enviar dados até que o separador do navegador esteja fechado.

1. Volte para o seu centro de eventos no portal Azure. Na página **'Visão Geral',** são apresentados os novos eventos recebidos pelo centro de eventos.

    [![Uma página de visão geral do centro de eventos que mostra métricas para o centro do evento](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

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

* **Entrada**: Uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido num evento.

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

* **Saída**: Dois eventos. A **localização da** propriedade é copiada para cada evento.

    |localização|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exemplo quatro

* **Entrada**: Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Esta entrada demonstra que as propriedades globais podem ser representadas pelo complexo objeto JSON.

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
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressão|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Próximos passos

* [Veja o seu ambiente](https://insights.timeseries.azure.com) no Azure Time Series Insights Explorer.

* Leia mais sobre [mensagens de dispositivo IoT Hub](../iot-hub/iot-hub-devguide-messages-construct.md)