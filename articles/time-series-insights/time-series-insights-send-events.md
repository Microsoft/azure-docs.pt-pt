---
title: Enviar eventos para um ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Saiba como configurar um hub de eventos e executar um aplicativo de exemplo para enviar eventos, pode ver no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 55b19a6cf71730858fcf42880f71a2c9c07a3b31
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683967"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente do Time Series Insights com um hub de eventos

Este artigo explica como criar e configurar um hub de eventos nos Hubs de eventos do Azure e, em seguida, execute um aplicativo de exemplo para enviar eventos. Se tiver um hub de eventos existente que tenha de eventos no formato JSON, ignorar este tutorial e ver o seu ambiente no [do Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para saber como criar um hub de eventos, consulte a [documentação dos Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/).
1. Na caixa de pesquisa, procure **os Hubs de eventos**. Na lista devolvida, selecione **os Hubs de eventos**.
1. Selecione o seu hub de eventos.
1. Quando cria um hub de eventos, realmente está a criar um espaço de nomes do hub de eventos. Se ainda não criou um hub de eventos no espaço de nomes, no menu, em **entidades**, criar um hub de eventos.  

    ![Lista de hubs de eventos][1]

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, sob **entidades**, selecione **dos Hubs de eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Sob **entidades**, selecione **grupos de consumidores**e, em seguida, selecione **grupo de consumidores**.

    ![Criar um grupo de consumidores][2]

1. Confirme que cria um grupo de consumidores que seja utilizado exclusivamente pela sua origem de eventos do Time Series Insights.

    > [!IMPORTANT]
    > Certifique-se de que este grupo de consumidores não é utilizado por nenhum outro serviço (como uma tarefa do Azure Stream Analytics ou outro ambiente do Time Series Insights). Se o grupo de consumidores for utilizado por outros serviços, operações de leitura são afetados negativamente para este ambiente e para outros serviços. Se usar **$Default** como o grupo de consumidores, outros leitores potencialmente poderão reutilizar seu grupo de consumidores.

1. No menu, sob **configurações**, selecione **políticas de acesso partilhado**e, em seguida, selecione **Add**.

    ![Selecione políticas de acesso partilhado e, em seguida, selecione o botão Adicionar][3]

1. Na **Adicionar nova política de acesso partilhado** painel, criar um acesso partilhado com o nome **MySendPolicy**. Irá utilizar esta política de acesso partilhado para enviar eventos no C# exemplos neste artigo.

    ![Na caixa de nome de política, introduza MySendPolicy][4]

1. Sob **afirmação**, selecione a **enviar** caixa de verificação.

## <a name="add-a-time-series-insights-instance"></a>Adicionar uma instância do Time Series Insights

A atualização do Time Series Insights utiliza instâncias para adicionar dados contextuais para dados telemétricos recebidos. Os dados são associados no momento da consulta ao utilizar um **ID de série de tempo**. O **ID de série de tempo** para os windmills de exemplo é o projeto que podemos utilizar posteriormente neste artigo **Id**. Para saber mais sobre as instâncias de Time Series Insight e **ID de série de tempo**, consulte [modelos de série de tempo](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Criar uma fonte de eventos do Time Series Insights

1. Se não tiver criado uma origem de evento, conclua os passos para [criar uma origem de evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Definir um valor para `timeSeriesId`. Para saber mais sobre **ID de série de tempo**, consulte [modelos de série de tempo](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Enviar eventos (exemplo de windmills)

1. Na barra de pesquisa, procure **os Hubs de eventos**. Na lista devolvida, selecione **os Hubs de eventos**.

1. Selecione o seu hub de eventos.

1. Aceda a **partilhados políticas de acesso** > **RootManageSharedAccessKey**. Copie o valor para **ligação chave primária sting**.

    ![Copie o valor para a cadeia de ligação da chave primária][5]

1. Aceda a https://tsiclientsample.azurewebsites.net/windFarmGen.html. O URL é executado windmill simulado dispositivos.
1. Na **cadeia de ligação do Hub de eventos** caixa na página Web, cole a cadeia de ligação que copiou no [enviar eventos](#push-events).
  
    ![Cole a cadeia de ligação da chave primária na caixa de cadeia de ligação do Hub de eventos][6]

1. Selecione **clique aqui para iniciar**. O simulador gera instância JSON que pode utilizar diretamente.

1. Regresse ao seu hub de eventos no portal do Azure. Sobre o **descrição geral** página, deverá ver os novos eventos que está a ser recebidos pelo hub de eventos:

    ![Uma página de descrição geral do hub de eventos que mostra as métricas para o hub de eventos][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Formas JSON suportadas

### <a name="sample-1"></a>Exemplo 1

#### <a name="input"></a>Input

Um objeto JSON simples:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Saída: Um evento

|ID|carimbo de data/hora|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exemplo 2

#### <a name="input"></a>Input

Uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido para um evento.

```json
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

#### <a name="output-two-events"></a>Saída: Dois eventos

|ID|carimbo de data/hora|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exemplo 3

#### <a name="input"></a>Input

Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON:

```json
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

#### <a name="output-two-events"></a>Saída: Dois eventos

A propriedade **localização** é copiada para cada evento.

|localização|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exemplo 4

#### <a name="input"></a>Input

Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que propriedades globais podem ser representadas pelo objeto JSON complexo.

```json
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

#### <a name="output-two-events"></a>Saída: Dois eventos

|localização|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Passos Seguintes

- [Ver o seu ambiente](https://insights.timeseries.azure.com) no Explorador do Time Series Insights.

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
