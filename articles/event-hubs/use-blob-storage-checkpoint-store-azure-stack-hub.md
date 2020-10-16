---
title: Use o Blob Storage como loja de ponto de verificação no Azure Stack Hub (pré-visualização)
description: Este artigo descreve como usar o Blob Storage como uma loja de checkpoint em Event Hubs no Azure Stack Hub (pré-visualização).
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1c876ed36be6aec9395a1acba3a1deb25a47de2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039230"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Use o Blob Storage como loja de ponto de verificação - Centros de eventos no Azure Stack Hub (pré-visualização)
Se estiver a utilizar o Azure Blob Storage como loja de checkpoint num ambiente que suporta uma versão diferente do Storage Blob SDK do que os que estão normalmente disponíveis no Azure, terá de usar o código para alterar a versão API do serviço de armazenamento para a versão específica suportada por esse ambiente. Por exemplo, se estiver a executar Os Centros de [Eventos numa versão Azure Stack Hub 2002](/azure-stack/user/event-hubs-overview), a versão mais alta disponível para o serviço de Armazenamento é a versão 2017-11-09. Neste caso, é necessário utilizar o código para direcionar a versão API do serviço de armazenamento para 2017-11-09. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte estas amostras no GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java) 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) ou  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [Sincronizado,](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) [Assíncronos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Os Centros de Eventos no Azure Stack Hub estão atualmente em [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) e são gratuitos. 

Se executar o recetor Event Hubs que utiliza o Blob Storage como loja de pontos de verificação sem direcionar a versão que o Azure Stack Hub suporta, receberá a seguinte mensagem de erro:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Mensagem de erro da amostra em Python
Para Python, um erro `azure.core.exceptions.HttpResponseError` de é passado para o manipulador de `on_error(partition_context, error)` erros de `EventHubConsumerClient.receive()` . Mas o método `receive()` não levanta uma exceção. `print(error)` Imprimirá as seguintes informações de exceção:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

O madeireiro registará dois avisos como os seguintes:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte artigo aprender sobre partição e checkpointing: Equilibrar a [carga de partição em vários casos da sua aplicação](event-processor-balance-partition-load.md)
