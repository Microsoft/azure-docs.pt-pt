---
title: Leia os Azure Event Hubs capturados dados de uma aplicação Python (mais recente)
description: Este artigo mostra-lhe como escrever código Python para capturar dados enviados para um centro de eventos e ler os dados do evento capturados a partir de uma conta de armazenamento Azure.
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883202"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Capture os dados do Event Hubs no Azure Storage e leia-os utilizando python (azure-eventhub)

Você pode configurar um centro de eventos para que os dados enviados para um centro de eventos seja capturado numa conta de armazenamento Azure ou Azure Data Lake Storage Gen 1 ou Gen 2. Este artigo mostra-lhe como escrever o código Python para enviar eventos para um centro de eventos e ler os dados capturados do **armazenamento Azure Blob**. Para obter mais informações sobre esta funcionalidade, consulte a [visão geral da funcionalidade De captura de Centros](event-hubs-capture-overview.md)de Eventos.

Este quickstart utiliza o [Azure Python SDK](https://azure.microsoft.com/develop/python/) para demonstrar a funcionalidade Captura. A aplicação *sender.py* envia telemetria ambiental simulada para centros de eventos em formato JSON. O centro de eventos está configurado para utilizar a funcionalidade Captura para escrever estes dados para o armazenamento blob em lotes. A *aplicação capturereader.py* lê estas bolhas e cria um ficheiro de apêndice para cada dispositivo. A aplicação escreve então os dados em ficheiros CSV.

Neste início rápido, irá: 

> [!div class="checklist"]
> * Crie uma conta de armazenamento Azure Blob e um recipiente no portal Azure.
> * Crie um espaço de nomes de Centros de Eventos utilizando o portal Azure.
> * Crie um centro de eventos com a funcionalidade Captura ativada e conecte-a à sua conta de armazenamento.
> * Envie dados para o seu centro de eventos utilizando um script Python.
> * Leia e processe ficheiros da Event Hubs Capture utilizando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python com PIP e os seguintes pacotes instalados. O código deste artigo foi testado contra estas versões. 
    - Python 3.7
    - azure-eventhub 5.2.0
    - azure-storage-blob 12.6.0
    - avro-python3 1.10.1
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.  
- Um espaço de nomes e centro de eventos do Event Hubs ativo.
[Crie um espaço de nomes de Event Hubs e um centro de eventos no espaço de nomes.](event-hubs-create.md) Grave o nome do espaço de nomes do Event Hubs, o nome do centro de eventos e a chave de acesso primária para o espaço de nome. Para obter a chave de acesso, consulte [obter uma cadeia de conexão Event Hubs](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome-chave predefinido é *RootManageSharedAccessKey*. Para este arranque rápido, só precisas da chave primária. Não precisas da corda de ligação.  
- Uma conta de armazenamento Azure, um recipiente blob na conta de armazenamento e um fio de ligação à conta de armazenamento. Se não tiver estes itens, faça o seguinte:  
    1. [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Criar um contentor de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Obtenha o fio de ligação para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Certifique-se de que grava o colar de ligação e o nome do recipiente para posterior utilização neste arranque rápido.  
- Ative a função Captura para o centro de eventos. Para tal, siga as instruções em [Enable Event Hubs Capture utilizando o portal Azure](event-hubs-capture-enable-through-portal.md). Selecione a conta de armazenamento e o recipiente blob que criou no passo anterior. Também pode ativar a funcionalidade quando criar um centro de eventos.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Crie um script Python para enviar eventos para o seu centro de eventos
Nesta secção, você cria um script Python que envia 200 eventos (10 dispositivos * 20 eventos) para um centro de eventos. Estes eventos são uma amostra de leitura ambiental que é enviada em formato JSON. 

1. Abra o seu editor python favorito, como [Visual Studio Code.][Visual Studio Code]
2. Criar um script chamado *sender.py*. 
3. Cole o seguinte código em *sender.py*. 
   
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
    * `EVENT HUBS NAMESPACE CONNECTION STRING`Substitua-a pela cadeia de ligação para o seu espaço de nomes 'Centros de Eventos'.  
    * `EVENT HUB NAME`Substitua-o pelo nome do seu centro de eventos.  
5. Execute o script para enviar eventos para o centro de eventos.  
6. No portal Azure, pode verificar se o centro de eventos recebeu as mensagens. Ver **mensagens** na secção **Métricas.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para a página mostrar que as mensagens foram recebidas. 

    [![Verifique se o centro de eventos recebeu as mensagens](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Crie um script Python para ler os seus ficheiros De captura
Neste exemplo, os dados capturados são armazenados no armazenamento da Azure Blob. O script desta secção lê os ficheiros de dados capturados da sua conta de armazenamento Azure e gera ficheiros CSV para que possa abrir e visualizar facilmente. Verá 10 ficheiros no atual diretório de trabalho da aplicação. Estes ficheiros conterão as leituras ambientais dos 10 dispositivos. 

1. No seu editor python, crie um script chamado *capturereader.py*. Este script lê os ficheiros capturados e cria um ficheiro para cada dispositivo escrever os dados apenas para esse dispositivo.
2. Cole o seguinte código em *capturereader.py*. 
   
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
3. `AZURE STORAGE CONNECTION STRING`Substitua-a pela cadeia de ligação da sua conta de armazenamento Azure. O nome do recipiente que criou neste arranque rápido é *captura.* Se utilizar um nome diferente para o recipiente, substitua a *captura* pelo nome do recipiente na conta de armazenamento. 

## <a name="run-the-scripts"></a>Executar os scripts
1. Abra um pedido de comando que tenha Python no seu caminho, e depois executar estes comandos para instalar pacotes pré-requisitos Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > O código deste artigo foi testado contra estas versões. 
    > - Python 3.7
    > - azure-eventhub 5.2.0
    > - azure-storage-blob 12.6.0
    > - avro-python3 1.10.1
2. Mude o seu diretório para o diretório onde guardou *sender.py* e *capturereader.py*, e executar este comando:
   
   ```
   python sender.py
   ```
   
   Este comando inicia um novo processo Python para executar o remetente.
3. Aguarde alguns minutos para que a captura corra e, em seguida, insira o seguinte comando na sua janela de comando original:
   
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
[Event Hubs overview]: ./event-hubs-about.md
