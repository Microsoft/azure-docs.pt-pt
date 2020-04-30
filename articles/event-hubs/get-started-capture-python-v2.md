---
title: Leia o Azure Event Hubs capturado saem dados de uma aplicação Python (mais recente)
description: Este artigo mostra-lhe como escrever código Python para capturar dados que são enviados para um centro de eventos e ler os dados do evento capturados a partir de uma conta de armazenamento Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 191a2246afdc60953d8c353f9ccdc2339130f910
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82159340"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Capture dados de Hubs de Eventos no Armazenamento Azure e leia-os usando Python (versão azure-eventhub 5)

Você pode configurar um hub de evento para que os dados enviados para um hub de eventos sejam capturados em uma conta de armazenamento Azure ou Azure Data Lake Storage Gen 1 ou Gen 2. Este artigo mostra-lhe como escrever código Python para enviar eventos para um centro de eventos e ler os dados capturados do **armazenamento de Azure Blob**. Para mais informações sobre esta funcionalidade, consulte a visão geral da funcionalidade De captura de Hubs de [Eventos](event-hubs-capture-overview.md).

Este quickstart usa o [Azure Python SDK](https://azure.microsoft.com/develop/python/) para demonstrar a função Capture. A aplicação *sender.py* envia telemetria ambiental simulada para centros de eventos em formato JSON. O centro de eventos está configurado para usar a funcionalidade Capture para escrever estes dados para o armazenamento blob em lotes. A aplicação *capturereader.py* lê estas bolhas e cria um ficheiro de apêndice para cada dispositivo. A aplicação escreve os dados em ficheiros CSV.

> [!IMPORTANT]
> Este quickstart utiliza a versão 5 do Azure Event Hubs Python SDK. Para um arranque rápido que utiliza a versão 1 do Python SDK, consulte [este artigo](event-hubs-capture-python.md). 

Neste início rápido, irá: 

> [!div class="checklist"]
> * Crie uma conta de armazenamento e recipiente Azure Blob no portal Azure.
> * Crie um espaço de nome sinuoso do Event Hubs utilizando o portal Azure.
> * Crie um centro de eventos com a funcionalidade Capture ativada e conecte-a à sua conta de armazenamento.
> * Envie dados para o seu centro de eventos usando um script Python.
> * Leia e processe ficheiros a partir de Hubs de Eventos Capture usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 2.7, e 3.5 ou mais tarde, com PIP instalado e atualizado.  
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.  
- Um espaço de nome ative Event Hubs e centro de eventos.
[Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos no espaço de nome.](event-hubs-create.md) Grave o nome do espaço de nome Sem Evento, o nome do centro do evento e a chave de acesso primária para o espaço de nome. Para obter a chave de acesso, consulte Obter uma cadeia de ligação De [Eventos Hubs](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave predefinida é *RootManageSharedAccessKey*. Para este arranque rápido, só precisa da chave principal. Não precisas da corda de ligação.  
- Uma conta de armazenamento Azure, um recipiente de bolhas na conta de armazenamento e uma cadeia de ligação à conta de armazenamento. Se não tiver estes itens, faça o seguinte:  
    1. [Criar uma conta de armazenamento Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Criar um contentor de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Obtenha a cadeia de ligação à conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Certifique-se de que regista o nome da corda de ligação e do recipiente para posterior utilização neste arranque rápido.  
- Ativar a função Capture para o centro do evento. Para isso, siga as instruções em [Ativação de Cubos de Eventos Capturando utilizando o portal Azure](event-hubs-capture-enable-through-portal.md). Selecione a conta de armazenamento e o recipiente de bolha que criou na etapa anterior. Também pode ativar a funcionalidade quando criar um centro de eventos.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Crie um roteiro Python para enviar eventos para o seu centro de eventos
Nesta secção, você cria um script Python que envia 200 eventos (10 dispositivos * 20 eventos) para um hub de eventos. Estes eventos são uma amostra de leitura ambiental que é enviada em formato JSON. 

1. Abra o seu editor favorito da Python, como [Visual Studio Code.][Visual Studio Code]
2. Crie um guião chamado *sender.py.* 
3. Colá-lo no seguinte código em *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Substitua os seguintes valores nos scripts:  
    * Substitua-a `EVENT HUBS NAMESPACE CONNECTION STRING` com a cadeia de ligação para o espaço de nome do Event Hubs.  
    * Substitua-o `EVENT HUB NAME` pelo nome do seu centro de eventos.  
5. Execute o guião para enviar eventos para o centro do evento.  
6. No portal Azure, pode verificar se o centro do evento recebeu as mensagens. Mude para a vista **Mensagens** na secção **Métricas.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para a página mostrar que as mensagens foram recebidas. 

    [![Verifique se o centro do evento recebeu as mensagens](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Crie um script Python para ler os seus ficheiros Capture
Neste exemplo, os dados capturados são armazenados no armazenamento da Blob Azure. O script nesta secção lê os ficheiros de dados capturados da sua conta de armazenamento Azure e gera ficheiros CSV para que possa facilmente abrir e visualizar. Verá 10 ficheiros no atual diretório de trabalho da aplicação. Estes ficheiros conterão as leituras ambientais dos 10 dispositivos. 

1. No seu editor python, crie um guião chamado *capturereader.py.* Este script lê os ficheiros capturados e cria um ficheiro para cada dispositivo escrever os dados apenas para esse dispositivo.
2. Colhe o seguinte código em *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Substitua-a `AZURE STORAGE CONNECTION STRING` com a cadeia de ligação para a sua conta de armazenamento Azure. O nome do recipiente que criou neste arranque rápido é *a captura.* Se usou um nome diferente para o recipiente, substitua a *captura* pelo nome do recipiente na conta de armazenamento. 

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra uma solicitação de comando que tenha Python no seu caminho, e, em seguida, executar estes comandos para instalar pacotes pré-requisito python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Mude o seu diretório para o diretório onde guardou *sender.py* e *capturereader.py,* e dirija este comando:
   
   ```
   python sender.py
   ```
   
   Este comando inicia um novo processo python para executar o remetente.
3. Aguarde alguns minutos para que a captura corra e, em seguida, introduza o seguinte comando na janela de comando original:
   
   ```
   python capturereader.py
   ```

   Este processador de captura utiliza o diretório local para descarregar todas as bolhas da conta de armazenamento e do contentor. Processa qualquer um que não esteja vazio, e escreve os resultados como ficheiros CSV no diretório local.

## <a name="next-steps"></a>Passos seguintes
Confira [as amostras de Python no GitHub.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
