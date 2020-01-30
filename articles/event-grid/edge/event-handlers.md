---
title: Manipuladores e destinos de eventos - Azure Event Grid IoT Edge  Microsoft Docs
description: Manipuladores de eventos e destinos em Event Grid on Edge
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849750"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Manipuladores de eventos e destinos em Event Grid on Edge

Um manipulador de eventos é o local onde o evento para mais ações ou para processar o evento. Com o módulo Event Grid on Edge, o manipulador de eventos pode estar no mesmo dispositivo de borda, outro dispositivo ou na nuvem. Pode utilizar qualquer WebHook para lidar com eventos ou enviar eventos para um dos manipuladores nativos como o Azure Event Grid.

Este artigo fornece informações sobre como configurar cada um.

## <a name="webhook"></a>WebHook

Para publicar num ponto final do WebHook, detete te `endpointType` para `WebHook` e fornecer:

* endpointUrl: O URL final do webhook

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

Para publicar num ponto final da Rede de Eventos Azure, detete a `endpointType` para `eventGrid` e fornecer:

* endpointUrl: URL tópico da grelha de eventos na nuvem
* sasKey: Chave SAS do Tópico da Grelha de Eventos
* nome tópico: Nome para carimbar todos os eventos de saída para A Grelha de Eventos. O nome tópico é útil ao publicar um tópico de domínio de grelha de eventos.

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

Para publicar num módulo Edge Hub, detete a `endpointType` para `edgeHub` e fornecer:

* nome de saída: A saída em que o módulo Event Grid irá encaminhar eventos que correspondam a esta subscrição ao EdgeHub. Por exemplo, os eventos que correspondam à subscrição abaixo serão escritos para /mensagens/módulos/eventgridmodule/outputs/sampleSub4.

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

## <a name="event-hubs"></a>Event Hubs

Para publicar num Event Hub, detete a `endpointType` para `eventHub` e fornecer:

* conexãoString: Linha de ligação para o Hub de Evento específico que você está a visar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > A cadeia de ligação deve ser específica da entidade. A utilização de uma cadeia de ligação espaço-nome não funcionará. Você pode gerar uma cadeia de conexão específica de entidade navegando para o Hub de Evento específico que você gostaria de publicar no Portal Azure e clicando em políticas de **acesso partilhado** para gerar uma nova cadeia de conneceção específica da entidade.

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

## <a name="service-bus-queues"></a>Filas do Service Bus

Para publicar numa fila de ônibus de serviço, detete a `endpointType` para `serviceBusQueue` e fornecer:

* ligaçãoString: Linha de ligação para a fila específica do ônibus de serviço que você está a visar gerada através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > A cadeia de ligação deve ser específica da entidade. A utilização de uma cadeia de ligação espaço-nome não funcionará. Gere uma cadeia de ligação específica para a entidade navegando para a fila específica de ônibus de serviço que gostaria de publicar no Portal Azure e clicando em políticas de **acesso partilhado** para gerar uma nova cadeia de conneceção específica da entidade.

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

## <a name="service-bus-topics"></a>Tópicos do Service Bus

Para publicar num Tópico de Autocarro de Serviço, detete a `endpointType` para `serviceBusTopic` e fornecer:

* conexãoString: Linha de ligação para o tópico específico do ônibus de serviço que você está a visar gerado através de uma Política de Acesso Partilhado.

    >[!NOTE]
    > A cadeia de ligação deve ser específica da entidade. A utilização de uma cadeia de ligação espaço-nome não funcionará. Gere uma cadeia de ligação específica para uma entidade navegando para o tópico específico do bus de serviço que gostaria de publicar no Portal Azure e clicando em políticas de **acesso partilhado** para gerar uma nova cadeia de conneceção específica da entidade.

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

## <a name="storage-queues"></a>Filas de armazenamento

Para publicar numa fila de armazenamento, delineie a `endpointType` para `storageQueue` e fornecer:

* nome da fila de armazenamento para a qual está a publicar.
* ligaçãoString: Fio de ligação para a conta de armazenamento em que a fila de armazenamento está dentro.

    >[!NOTE]
    > Unline Event Hubs, Service Bus Queues e Service Bus Topics, a cadeia de ligação utilizada para filas de armazenamento não é específica da entidade. Em vez disso, deve apenas a cadeia de ligação para a Conta de Armazenamento.

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