---
title: Publique, subscreva eventos na cloud - Azure Event Grid IoT Edge [ Microsoft Docs
description: Publique, subscreva eventos na nuvem usando Webhook com Grelha de Eventos em IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844599"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Tutorial: Publicar, subscrever eventos na nuvem

Este artigo percorre todos os passos necessários para publicar e subscrever eventos usando a Grelha de Eventos no IoT Edge. Este tutorial utiliza e função Azure como Manipulador de Eventos. Para obter tipos de destino adicionais, consulte [os manipuladores de eventos](event-handlers.md).

Consulte o [Event Grid Concepts](concepts.md) para entender o que é um tópico de grelha de eventos e subscrição antes de prosseguir.

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, necessitará:

* **Assinatura Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no arranque rápido dos [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Criar uma função Azure no portal Azure

Siga os passos descritos no [tutorial](../../azure-functions/functions-create-first-azure-function.md) para criar uma função Azure. 

Substitua o código por um código que se segue:

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

Na sua nova função, selecione **"Obter URL** de função no direito superior direito", selecione 'Tecla de**função' (Tecla de função)** e, em seguida, selecione **Copy**. Utilizará o valor URL da função mais tarde no tutorial.

> [!NOTE]
> Consulte a documentação das [Funções Azure](../../azure-functions/functions-overview.md) para mais amostras e tutoriais sobre a reação a eventos que um evento de eventos utilizando.

## <a name="create-a-topic"></a>Criar um tópico

Como editor de um evento, você precisa criar um tópico de grelha de eventos. O tópico refere-se a um ponto final para onde os editores podem enviar eventos.

1. Crie o tópico2.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Executar o seguinte comando para criar o tópico. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. Http Status Code of 200 OK deve ser devolvido.

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

Os assinantes podem inscrever-se para eventos publicados num tópico. Para receber qualquer evento, os subscritores terão de criar uma subscrição da grelha de eventos sobre um tema de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscrição2.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

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
   > O **endpointType** especifica que o assinante é um Webhook.  O **endpointUrl** especifica o URL no qual o assinante está a ouvir eventos. Este URL corresponde à amostra da Função Azure que configura mais cedo.
2. Executar o seguinte comando para criar a subscrição. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. Http Status Code of 200 OK deve ser devolvido.

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

1. Crie evento2.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

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

* Executar o seguinte comando para apagar o tópico e todas as suas subscrições

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Elimine a função Azure criada no portal Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um tópico de grelha de eventos, subscrição e eventos publicados. Agora que conhece os passos básicos, consulte os seguintes artigos:

* Para resolver problemas com a utilização da Grelha de Eventos Azure na IoT Edge, consulte [o guia de resolução de problemas](troubleshoot.md).
* Criar/atualizar subscrição com [filtros](advanced-filtering.md).
* Configurar a persistência do módulo Event Grid no [linux](persist-state-linux.md) [ou](persist-state-windows.md) windows
* Siga [a documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhar eventos para Azure Event Grid na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)
