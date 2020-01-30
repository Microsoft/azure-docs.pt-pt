---
title: Eventos de vanguarda para a nuvem de Rede de Eventos - Azure Event Grid IoT Edge  Microsoft Docs
description: Eventos de borda avançada para nuvem de rede de eventos
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844722"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Tutorial: Eventos para a frente para a nuvem da Grelha de Eventos

Este artigo percorre todos os passos necessários para encaminhar eventos de borda para event grid na nuvem Azure. Talvez queira fazê-lo pelas seguintes razões:

* Reaja a eventos de borda na nuvem.
* Encaminhar eventos para Event Grid na nuvem e utilizar hubs de eventos Azure ou filas de armazenamento azure para eventos tampão antes de processá-los na nuvem.

 Para completar este tutorial, você precisa ter uma compreensão dos conceitos de Event Grid no [edge](concepts.md) e [Azure](../concepts.md). Para obter tipos de destino adicionais, consulte [os manipuladores de eventos](event-handlers.md). 

## <a name="prerequisites"></a>Pré-requisitos 
Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no arranque rápido dos [dispositivos](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) ou Windows se ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Criar tópico de grelha de eventos e subscrição na nuvem

Crie um tópico de grelha de eventos e subscrição na nuvem seguindo [este tutorial.](../custom-event-quickstart-portal.md) Note `topicURL`, `sasKey`, e `topicName` do tema recém-criado que usará mais tarde no tutorial.

Por exemplo, se criasse um tópico chamado `testegcloudtopic` nos EUA Ocidentais, os valores seriam parecidos com:

* **TópicoUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Nome tópico**: `testegcloudtopic`
* **SasKey**: Disponível no **AccessKey** do seu tópico. Utilize **a tecla1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Criar tópico de grelha de evento sintetizador

1. Crie topic3.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Executar o seguinte comando para criar o tópico. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Executar o seguinte comando para verificar o tópico foi criado com sucesso. O código de status HTTP de 200 OK deve ser retornado.

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
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Criar subscrição da Grelha de Eventos no limite

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscrição3.json com o seguinte conteúdo. Consulte a nossa [documentação da API](api.md) para obter detalhes sobre a carga útil.

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
   > O **endpointUrl** especifica que o TÓPICO da Grelha de Eventos URL na nuvem. O **sasKey** refere-se à chave do tópico da nuvem de evento. O valor em **tópicoName** será usado para carimbar todos os eventos de saída para A Grelha de Eventos. Isto pode ser útil ao publicar um tópico de domínio da Grelha de Eventos. Para mais informações sobre o tópico de domínio da Grelha de [Eventos, consulte domínios de eventos](../event-domains.md)

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

2. Executar o seguinte comando para criar a subscrição. O código de status HTTP de 200 OK deve ser retornado.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

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

## <a name="publish-an-event-at-the-edge"></a>Publique um evento no limite

1. Crie evento3.json com o seguinte conteúdo. Consulte a [documentação da API](api.md) para obter detalhes sobre a carga útil.

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

Para obter informações sobre os eventos de visualização entregues pelo tópico da nuvem, consulte o [tutorial](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Recursos de limpeza

* Executar o seguinte comando para apagar o tópico e todas as suas subscrições

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Elimine também o tópico e as subscrições criadas na nuvem (Azure Event Grid).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, publicou um evento no limite e reencaminhou-se para event grid na nuvem Azure. Agora que sabe os passos básicos para avançar para event grid na nuvem:

* Para resolver problemas com a utilização da Grelha de Eventos Azure na IoT Edge, consulte [o guia de resolução de problemas](troubleshoot.md).
* Encaminhar eventos para ioTHub seguindo este [tutorial](forward-events-iothub.md)
* Encaminhar eventos para Webhook na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
* [Monitorize tópicos e subscrições no limite](monitor-topics-subscriptions.md)
