---
title: Publicar, subscrever eventos na nuvem - Azure Event Grid IoT Edge | Microsoft Docs
description: Publicar, subscrever eventos em nuvem usando Webhook com Grade de Eventos no IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ec7ecb77d37ed1cdf1d13aa7191f5d50e0008c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790804"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Tutorial: Publicar, subscrever eventos na nuvem

Este artigo percorre todos os passos necessários para publicar e subscrever eventos usando a Grade de Eventos no IoT Edge. Este tutorial usa e funciona a Azure como o Manipulador de Eventos. Para outros tipos de destino, consulte [os manipuladores de eventos.](event-handlers.md)

Consulte [os Conceitos de Grelha de Eventos](concepts.md) para entender o que é um tópico e subscrição de grelha de eventos antes de prosseguir.

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Azure subscrição** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Azure IoT Hub e IoT Edge dispositivo** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Criar uma função Azure no portal Azure

Siga os passos descritos no [tutorial](../../azure-functions/functions-get-started.md) para criar uma função Azure. 

Substitua o corte de código pelo seguinte código:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Na sua nova função, selecione Obter URL de **função** no direito superior, selecione predefinição **(Tecla função)** e, em seguida, selecione **Copy**. Utilizará o valor URL da função mais tarde no tutorial.

> [!NOTE]
> Consulte a documentação do [Azure Functions](../../azure-functions/functions-overview.md) para obter mais amostras e tutoriais sobre a reação aos eventos que utilizam os gatilhos do evento EventGrid.

## <a name="create-a-topic"></a>Criar um tópico

Como editor de um evento, você precisa criar um tópico de grelha de eventos. Tópico refere-se a um ponto final para onde os editores podem enviar eventos.

1. Crie topic2.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para mais detalhes sobre a carga útil.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Executar o seguinte comando para criar o tópico. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Resultado do exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Criar uma subscrição de evento

Os assinantes podem inscrever-se para eventos publicados num tema. Para receber qualquer evento, os subscritores terão de criar uma subscrição da grelha de eventos sobre um tema de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription2.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter mais detalhes sobre a carga útil.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > O **ponto finalType** especifica que o assinante é um Webhook.  O **endpointUrl** especifica o URL no qual o assinante está a ouvir eventos. Este URL corresponde à amostra da Função Azure que configura anteriormente.
2. Executar o seguinte comando para criar a subscrição. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicar um evento

1. Crie event2.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter mais detalhes sobre a carga útil.

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Executar o seguinte comando para publicar evento

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

Pode ver o evento entregue no portal Azure sob a opção **Monitor** da sua função.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Executar o seguinte comando para eliminar o tópico e todas as suas subscrições

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Eliminar a função Azure criada no portal Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um tópico de grelha de eventos, subscrição e eventos publicados. Agora que conhece os passos básicos, veja os seguintes artigos:

* Para resolver problemas com a utilização da Grelha de Eventos Azure no IoT Edge, consulte o [guia de resolução de problemas](troubleshoot.md).
* Criar/atualizar a subscrição com [filtros](advanced-filtering.md).
* Configurar a persistência do módulo de grelha de eventos no [linux](persist-state-linux.md) ou [Windows](persist-state-windows.md)
* Siga [a documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhar eventos para Azure Event Grid na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)