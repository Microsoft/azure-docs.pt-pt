---
title: Manipuladores e destinos de eventos - Azure Event Grid IoT Edge / Microsoft Docs
description: Manipuladores de eventos e destinos em Grade de Eventos na Borda
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171589"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Manipuladores de eventos e destinos em Grade de Eventos na Borda

Um manipulador de eventos é o local onde o evento para mais ações ou para processar o evento. Com o módulo 'Grade de Eventos on Edge', o manipulador de eventos pode estar no mesmo dispositivo de borda, outro dispositivo ou na nuvem. Você pode usar qualquer WebHook para lidar com eventos, ou enviar eventos para um dos manipuladores nativos como Azure Event Grid.

Este artigo fornece informações sobre como configurar cada um.

## <a name="webhook"></a>WebHook

Para publicar num ponto final webHook, desloque o `endpointType` ponto de acesso e `WebHook` forneça:

* endpointUrl: O URL de ponta webHook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Para publicar num ponto final de nuvem de Azure Event Grid, desconfiem `endpointType` `eventGrid` e forneçam:

* endpointUrl: URL tópico de grelha de evento na nuvem
* sasKey: Chave SAS do Tópico de Grelha de Eventos
* nome tópico: Nome para carimbar todos os eventos de saída para a Grade de Eventos. O nome tópico é útil ao publicar um tópico de Domínio de Grelha de Evento.

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

## <a name="iot-edge-hub"></a>IoT Edge Hub

Para publicar num módulo Edge Hub, desemote o `endpointType` to `edgeHub` e forneça:

* nome de saída: A saída na qual o módulo De Grelha de Eventos irá encaminhar eventos que correspondam a esta subscrição ao EdgeHub. Por exemplo, os eventos que correspondam à subscrição abaixo serão escritos para /mensagens/módulos/eventgridmodule/outputs/sampleSub4.

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

## <a name="event-hubs"></a>Hubs de Eventos

Para publicar num Centro de Eventos, desemote o `endpointType` e `eventHub` forneça:

* ligaçãoStragem: Cadeia de ligação para o centro de eventos específico que está a direcionar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > O fio de ligação deve ser específico da entidade. A utilização de uma cadeia de ligação namespace não funcionará. Pode gerar uma cadeia de conexão específica da entidade navegando para o Centro de Eventos específico que gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de connecção específica de entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Filas de Service Bus

Para publicar numa fila de autocarros de serviço, desemote `endpointType` o `serviceBusQueue` toe e forneça:

* ligaçãoStragem: Cadeia de ligação para a fila específica do autocarro de serviço que está a direcionar gerada através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > O fio de ligação deve ser específico da entidade. A utilização de uma cadeia de ligação namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando para a fila específica do Service Bus que gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de connecção específica de entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Tópicos de Service Bus

Para publicar num Tópico de Autocarro de Serviço, desemote o `endpointType` tema `serviceBusTopic` e forneça:

* conexãoStragem: Cadeia de ligação para o tópico específico do ônibus de serviço que está a direcionar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > O fio de ligação deve ser específico da entidade. A utilização de uma cadeia de ligação namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando para o tópico específico do Service Bus que gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de connecção específica de entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Filas de Armazenamento

Para publicar numa fila de armazenamento, desemote o  `endpointType` e `storageQueue` forneça:

* nome da fila de armazenamento a que vai publicar.
* ligaçãoStragem: Cadeia de ligação para a conta de armazenamento em que a fila de armazenamento está dentro.

    >[!NOTE]
    > Unline Event Hubs, Service Bus Queues e Service Bus Topics, a cadeia de ligação utilizada para as filas de armazenamento não é específica da entidade. Em vez disso, deve ser apenas o fio de ligação para a Conta de Armazenamento.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```