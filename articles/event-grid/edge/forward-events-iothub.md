---
title: Eventos forward Event Grid para IoTHub - Azure Event Grid IoT Edge [ Microsoft Docs
description: Eventos forward Event Grid para IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844705"
---
# <a name="tutorial-forward-events-to-iothub"></a>Tutorial: Eventos para a frente para IoTHub

Este artigo percorre todos os passos necessários para encaminhar eventos da Rede de Eventos para outros módulos IoT Edge, IoTHub usando rotas. Talvez queira fazê-lo pelas seguintes razões:

* Continue a utilizar quaisquer investimentos existentes já em vigor com o encaminhamento do EdgeHub
* Prefira encaminhar todos os eventos a partir de um dispositivo apenas através do IoT Hub

Para completar este tutorial, precisa entender os seguintes conceitos:

- [Conceitos de grelha de eventos](concepts.md)
- [Hub IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, necessitará:

* **Assinatura Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no arranque rápido dos [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Criar tópico

Como editor de um evento, você precisa criar um tópico de grelha de eventos. O tópico refere-se a um ponto final onde os editores podem então enviar eventos para.

1. Crie topic4.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Executar o seguinte comando para criar o tópico. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Resultado do exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Criar subscrição de eventos

Os assinantes podem inscrever-se para eventos publicados num tópico. Para receber qualquer evento, terão de criar uma subscrição da grelha de eventos sobre um tema de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscrição4.json com o conteúdo abaixo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > Os `endpointType` especificações que `edgeHub`o assinante é . A `outputName` saída especifica a saída em que o módulo Event Grid irá encaminhar eventos que correspondam a esta subscrição ao edgeHub. Por exemplo, os eventos que correspondam `/messages/modules/eventgridmodule/outputs/sampleSub4`à subscrição acima serão escritos para .
2. Executar o seguinte comando para criar a subscrição. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. Http Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Configurar uma rota de hub de borda

Atualize a rota do edge hub para encaminhar os eventos de subscrição de eventos a serem encaminhados para o IoTHub da seguinte forma:

1. Inscreva-se no [portal Azure](https://ms.portal.azure.com)
1. Navegue até ao **Hub IoT.**
1. Selecione **IoT Edge** do menu
1. Selecione a identificação do dispositivo-alvo da lista de dispositivos.
1. Selecione **Módulos de Conjunto**.
1. Selecione **Next** e à secção rotas.
1. Nas rotas, adicione uma nova rota

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Por exemplo,

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > A rota acima irá encaminhar quaisquer eventos combinados para que esta subscrição seja reencaminhada para o centro IoT. Pode utilizar as funcionalidades de [encaminhamento](../../iot-edge/module-composition.md) do hub Edge para filtrar ainda mais e encaminhar os eventos da Rede de Eventos para outros módulos IoT Edge.

## <a name="setup-iot-hub-route"></a>Configuração ioT hub rota

Consulte o tutorial de [encaminhamento do IoT Hub](../../iot-hub/tutorial-routing.md) para configurar uma rota a partir do hub IoT para que possa ver eventos reencaminhados a partir do módulo Event Grid. Use `true` para a consulta para manter o tutorial simples.  



## <a name="publish-an-event"></a>Publicar um evento

1. Crie evento4.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Executar o seguinte comando para publicar evento:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

Consulte o tutorial de [encaminhamento](../../iot-hub/tutorial-routing.md) do IoT Hub para ver os eventos.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Executar o seguinte comando para apagar o tópico e todas as suas subscrições no limite:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Elimine todos os recursos criados durante a criação de encaminhamento IoTHub na nuvem também.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um tópico de grelha de eventos, subscrição edge hub e eventos publicados. Agora que conhece os passos básicos para avançar para um hub de borda, veja os seguintes artigos:

* Para resolver problemas com a utilização da Grelha de Eventos Azure na IoT Edge, consulte [o guia de resolução de problemas](troubleshoot.md).
* Utilize filtros de rota [do hub](../../iot-edge/module-composition.md) de borda para eventos de partição
* Configurar a persistência do módulo Event Grid no [linux](persist-state-linux.md) [ou](persist-state-windows.md) windows
* Siga [a documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhar eventos para Azure Event Grid na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)