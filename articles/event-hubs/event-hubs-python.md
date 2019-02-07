---
title: Utilizar Python para enviar e receber mensagens - Event Hubs do Azure | Documentos da Microsoft
description: Saiba como enviar eventos para receber eventos de Internet e capturar a eventos de transmissão em fluxo através dos Hubs de eventos com o Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 88fdaec9e19c082a6fe981dc4d9a0e015335f1e2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818623"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Como os Hubs de eventos do Azure através de uma aplicação Python
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para obter mais informações, consulte [introdução aos Hubs de eventos](event-hubs-what-is-event-hubs.md). 

Este artigo fornece ligações para artigos que mostram como realizar as seguintes tarefas a partir de um aplicativo escrito em **Python**:

- [Enviar eventos para um hub de eventos](#send-events-to-event-hubs)
- [Receber eventos de um hub de eventos](#receive-events-from-event-hubs)
- Leitura capturados dados de eventos de um armazenamento do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- Crie um hub de eventos ao seguir um dos seguintes inícios rápidos: [Portal do Azure](event-hubs-create.md), [CLI do Azure](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [modelo Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 ou posterior instalado no seu computador.

## <a name="install-python-package"></a>Instalar pacote do Python

Para instalar o pacote do Python para os Hubs de eventos, abra um prompt de comando que tem o Python no respetivo caminho e, em seguida, execute este comando: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Enviar eventos para os Hubs de Eventos
O código a seguir mostra como enviar eventos para um hub de eventos a partir de uma aplicação de Python: 

1. Crie variáveis para conter o URL para o hub de eventos, o nome da chave e o valor da chave. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Criar um cliente dos Hubs de eventos, adicionar um remetente, executam o cliente, enviar o evento usando o remetente e, em seguida, pare o cliente quando tiver terminado. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Para obter um tutorial completo sobre como enviar eventos para um hub de eventos de um aplicativo escrito em Python, veja [este artigo](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Receber eventos dos Hubs de eventos
O código seguinte mostra como receber eventos de um hub de eventos a partir de uma aplicação de Python: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Para obter um tutorial completo sobre como receber eventos de um hub de eventos a partir de um aplicativo escrito em Python, consulte [neste artigo](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>Dados de eventos de captura de leitura do armazenamento do Azure
O código seguinte mostra como ler dados de eventos capturados que são armazenados num **armazenamento de Blobs do Azure** partir de uma aplicação de Python: Ativar **capturar** funcionalidade para o hub de eventos ao seguir as instruções de: [Ativar a captura de Hubs de eventos com o portal do Azure](event-hubs-capture-enable-through-portal.md). Em seguida, envie alguns eventos para o hub de eventos antes de testar o código. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

Para obter um tutorial completo sobre como ler dados de Hubs de eventos capturados num armazenamento de Blobs do Azure a partir de um aplicativo escrito em Python, consulte [neste artigo](event-hubs-capture-python.md)

## <a name="github-samples"></a>Exemplos do GitHub
Pode encontrar mais amostras de Python no [repositório de Git do azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Passos Seguintes
Leia os artigos na secção conceitos a partir [visão geral dos recursos de Hubs de eventos](event-hubs-features.md).
