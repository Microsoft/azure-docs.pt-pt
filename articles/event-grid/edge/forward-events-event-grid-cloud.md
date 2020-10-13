---
title: Eventos de borda para a nuvem de event grid - Azure Event Grid IoT Edge ! Microsoft Docs
description: Eventos de borda para a frente para a nuvem de grade de evento
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171606"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Tutorial: Eventos avançados para a nuvem de grelha de evento

Este artigo percorre todos os passos necessários para encaminhar eventos de borda para a Grade de Eventos na nuvem Azure. Talvez queira fazê-lo pelas seguintes razões:

* Reagir a eventos de borda na nuvem.
* Encaminhe os eventos para a Grade de Eventos na nuvem e use as filas de Azure Event Ou Azure Storage para eventos tampão antes de os processar na nuvem.

 Para completar este tutorial, você precisa ter uma compreensão dos conceitos de Grade de Eventos no [limite](concepts.md) e [Azure.](../concepts.md) Para outros tipos de destino, consulte [os manipuladores de eventos.](event-handlers.md) 

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Azure subscrição** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se ainda não tiver uma. 
* **Azure IoT Hub e IoT Edge dispositivo** - Siga os passos no arranque rápido para [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Crie tópico de grelha de evento e subscrição na nuvem

Crie um tópico de grelha de eventos e subscrição na nuvem seguindo [este tutorial.](../custom-event-quickstart-portal.md) Note para baixo `topicURL` , e do tópico `sasKey` `topicName` recém-criado que você usará mais tarde no tutorial.

Por exemplo, se criasse um tópico chamado `testegcloudtopic` nos EUA Ocidentais, os valores seriam semelhantes:

* **TópicoUrl:**`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Nome tópico:**`testegcloudtopic`
* **SasKey**: Disponível no **AccessKey** do seu tópico. Utilize **a tecla1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Criar tópico de grelha de eventos na borda

1. Crie topic3.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para mais detalhes sobre a carga útil.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Executar o seguinte comando para criar o tópico. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Resultado do exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Criar subscrição de Grade de Eventos no limite

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription3.jscom o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para mais detalhes sobre a carga útil.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > O **endpointUrl** especifica que o URL tópico de grelha de evento na nuvem. O **sasKey** refere-se à chave do tópico da nuvem de 'Grade de Eventos'. O valor no **temaName** será usado para carimbar todos os eventos de saída para a Grade de Eventos. Isto pode ser útil ao publicar num tópico de domínio de Grade de Eventos. Para obter mais informações sobre o tópico de domínio da Grade de [Eventos,](../event-domains.md) consulte os domínios do Evento

    Por exemplo,
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Executar o seguinte comando para criar a subscrição. HTTP Código de Estado de 200 OK deve ser devolvido.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Executar o seguinte comando para verificar se a subscrição foi criada com sucesso. HTTP Código de Estado de 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Resultado do exemplo:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publicar um evento no limite

1. Crie event3.jscom o seguinte conteúdo. Consulte [a documentação da API](api.md) para obter mais detalhes sobre a carga útil.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Execute o seguinte comando:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Verifique o evento de borda na nuvem

Para obter informações sobre os eventos de visualização entregues pelo tema da nuvem, consulte o [tutorial.](../custom-event-quickstart-portal.md)

## <a name="cleanup-resources"></a>Recursos de limpeza

* Executar o seguinte comando para eliminar o tópico e todas as suas subscrições

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Elimine tópicos e subscrições criadas na nuvem (Azure Event Grid) também.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, publicou um evento no limite e reencaminhado para a Grade de Eventos na nuvem Azure. Agora que conhece os passos básicos para avançar para a Grelha de Eventos em nuvem:

* Para resolver problemas com a utilização da Grelha de Eventos Azure no IoT Edge, consulte o [guia de resolução de problemas](troubleshoot.md).
* Encaminhar eventos para o IoTHub seguindo este [tutorial](forward-events-iothub.md)
* Encaminhar eventos para Webhook na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
* [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)
