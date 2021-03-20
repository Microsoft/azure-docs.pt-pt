---
title: Eventos de Grelha de Eventos avançados para IoTHub - Azure Event Grid IoT Edge | Microsoft Docs
description: Eventos de Grelha de Eventos avançados para ioTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171555"
---
# <a name="tutorial-forward-events-to-iothub"></a>Tutorial: Encaminhar eventos para ioTHub

Este artigo percorre todos os passos necessários para encaminhar eventos de Grade de Eventos para outros módulos IoT Edge, IoTHub usando rotas. Talvez queira fazê-lo pelas seguintes razões:

* Continue a utilizar quaisquer investimentos já existentes com o encaminhamento do EdgeHub
* Prefere encaminhar todos os eventos de um dispositivo apenas através do IoT Hub

Para completar este tutorial, precisa entender os seguintes conceitos:

- [Conceitos de grelha de eventos](concepts.md)
- [Hub IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Azure subscrição** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Azure IoT Hub e IoT Edge dispositivo** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Criar tópico

Como editor de um evento, você precisa criar um tópico de grelha de eventos. O tópico refere-se a um ponto final para onde os editores podem então enviar eventos para.

1. Crie topic4.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para mais detalhes sobre a carga útil.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Executar o seguinte comando para criar o tópico. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

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

Os assinantes podem inscrever-se para eventos publicados num tema. Para receber qualquer evento, eles precisarão criar uma subscrição da grelha de eventos sobre um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription4.jscom o conteúdo abaixo. Consulte a nossa [documentação da API](api.md) para obter mais detalhes sobre a carga útil.

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
   > O `endpointType` especia que o assinante é `edgeHub` . A `outputName` especifica a saída em que o módulo De Grelha de Evento irá encaminhar eventos que correspondam a esta subscrição ao EdgeHub. Por exemplo, os eventos que correspondam à subscrição acima será escrito para `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. Executar o seguinte comando para criar a subscrição. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

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

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com)
1. Navegue até ao **Hub IoT.**
1. Selecione **IoT Edge** do menu
1. Selecione o ID do dispositivo alvo na lista de dispositivos.
1. Selecione **módulos de conjunto**.
1. Selecione **Seguinte** e para a secção de rotas.
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
   > A rota acima irá encaminhar quaisquer eventos compatíveis para que esta subscrição seja reencaminhada para o hub IoT. Pode utilizar as funcionalidades [de encaminhamento](../../iot-edge/module-composition.md) do hub Edge para filtrar mais e encaminhar os eventos da Grade de Eventos para outros módulos IoT Edge.

## <a name="setup-iot-hub-route"></a>Rota do Hub IoT de configuração

Consulte o [tutorial de encaminhamento IoT Hub](../../iot-hub/tutorial-routing.md) para configurar uma rota a partir do hub IoT para que possa ver eventos reencaminhados a partir do módulo De Grelha de Eventos. Utilize `true` para a consulta para manter o tutorial simples.  



## <a name="publish-an-event"></a>Publicar um evento

1. Crie event4.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para mais detalhes sobre a carga útil.

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

Consulte o tutorial [de encaminhamento](../../iot-hub/tutorial-routing.md) IoT Hub para ver os eventos.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Executar o seguinte comando para eliminar o tópico e todas as suas subscrições no limite:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Elimine todos os recursos criados durante a configuração do encaminhamento do IoTHub na nuvem também.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um tópico de grelha de eventos, subscrição de edge hub e eventos publicados. Agora que conhece os passos básicos para avançar para um hub de borda, veja os seguintes artigos:

* Para resolver problemas com a utilização da Grelha de Eventos Azure no IoT Edge, consulte o [guia de resolução de problemas](troubleshoot.md).
* Use filtros de rota [do hub de borda](../../iot-edge/module-composition.md) para eventos de partição
* Configurar a persistência do módulo de grelha de eventos no [linux](persist-state-linux.md) ou [Windows](persist-state-windows.md)
* Siga [a documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhar eventos para Azure Event Grid na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)