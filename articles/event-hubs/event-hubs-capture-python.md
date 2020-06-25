---
title: 'Quickstart: Leia os dados capturados da aplicação Python - Azure Event Hubs'
description: 'Quickstart: Scripts que usam o Azure Python SDK para demonstrar a funcionalidade de Captura de Centros de Eventos.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8e6174970a6821f7541387f91b226cdebe555625
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323063"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Quickstart: Event Hubs Capture walkthrough: Python (azure-eventhub versão 1)

Capture é uma característica do Azure Event Hubs. Pode utilizar o Capture para entregar automaticamente os dados de streaming no seu centro de eventos a uma conta de armazenamento Azure Blob à sua escolha. Esta capacidade facilita o processamento de lotes em dados de streaming em tempo real. Este artigo descreve como usar o Event Hubs Capture with Python. Para obter mais informações sobre a captura de Centros de Eventos, consulte [eventos de captura através de Azure Event Hubs][Overview of Event Hubs Capture].

Este walkthrough usa o [Azure Python SDK](https://azure.microsoft.com/develop/python/) para demonstrar a funcionalidade Captura. O programa *sender.py* envia telemetria ambiental simulada para Centros de Eventos em formato JSON. O centro de eventos utiliza a função Captura para escrever estes dados para o armazenamento blob em lotes. A *aplicação capturereader.py* lê estas bolhas, cria um ficheiro de apêndice para cada um dos seus dispositivos e escreve os dados para *ficheiros .csv* em cada dispositivo.

> [!WARNING]
> Este quickstart é para a versão 1 do Azure Event Hubs Python SDK. Recomendamos que [emigre](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) o seu código para a [versão 5 do Python SDK](get-started-capture-python-v2.md).

Neste passeio, você: 

> [!div class="checklist"]
> * Crie uma conta de armazenamento Azure Blob e um recipiente no portal Azure.
> * Ativar a captura de Centros de Eventos e direcioná-lo para a sua conta de armazenamento.
> * Envie dados para o seu centro de eventos utilizando um script Python.
> * Leia e processe ficheiros da Event Hubs Capture utilizando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3.4 ou mais tarde, com `pip` instalado e atualizado.
  
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
  
- Um centro de nomes e eventos do Event Hubs ativo, criado seguindo as instruções no [Quickstart: Criar um centro de eventos utilizando o portal Azure](event-hubs-create.md). Tome nota dos nomes do seu espaço de nome e dos nomes do centro de eventos para usar mais tarde nesta passagem. 
  
  > [!NOTE]
  > Se já tiver um recipiente de armazenamento para utilizar, pode ativar o Capture e selecionar o recipiente de armazenamento quando criar o Centro de Eventos. 
  > 
  
- Os seus Centros de Eventos partilharam o nome da chave de acesso e o valor principal da chave. Encontre ou crie estes valores de acordo com **as políticas de acesso partilhado** na página do Event Hubs. O nome predefinido da chave de acesso é **RootManageSharedAccessKey**. Copie o nome da chave de acesso e o valor principal da chave para utilizar mais tarde nesta passagem. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Criar uma conta de armazenamento Azure Blob e recipiente

Crie uma conta de armazenamento e um recipiente para utilizar para a captura. 

1. Inicie sessão no [portal do Azure][Azure portal].
2. Na navegação à esquerda, selecione **contas de Armazenamento**, e no ecrã das contas de **Armazenamento,** selecione **Add**.
3. No ecrã de criação de conta de armazenamento, selecione um grupo de subscrição e recursos e dê um nome à conta de armazenamento. Pode deixar as outras seleções em incumprimento. Selecione **Rever + criar,** rever as definições e, em seguida, selecione **Criar**. 
   
   ![Criar conta de armazenamento][1]
   
4. Quando a implementação estiver concluída, selecione **Ir para**o recurso , e no ecrã **de visão geral** da conta de armazenamento, selecione **Recipientes**.
5. No ecrã **de Recipientes,** selecione **+ Recipiente.** 
6. No novo ecrã do **recipiente,** dê um nome ao recipiente e, em seguida, selecione **OK**. Tome nota do nome do recipiente para utilizar mais tarde na passagem. 
7. Na navegação à esquerda do ecrã **dos Recipientes,** selecione **as teclas de acesso**. Copie o **nome da conta de Armazenamento**, e o valor **chave** sob **a tecla1**, para utilizar mais tarde na passagem.
 
## <a name="enable-event-hubs-capture"></a>Ativar a captura de centros de eventos

1. No portal Azure, navegue para o seu centro de eventos selecionando o seu Espaço de Nomes de Eventos a partir de **todos os recursos,** selecionando centros de eventos na navegação esquerda e, em seguida, selecionando o seu centro de **eventos.** 
2. No ecrã do centro de **eventos Overview,** selecione **Eventos de Captura**.
3. No ecrã **Captura,** selecione **On**. Em seguida, sob **o Recipiente de Armazenamento Azure**, **selecione Select Container**. 
4. No ecrã **de Recipientes,** selecione o recipiente de armazenamento que pretende utilizar e, em seguida, selecione **Select**. 
5. No ecrã **Captura,** **selecione Guardar as alterações**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Crie um script Python para enviar eventos para o Centro de Eventos
Este script envia 200 eventos para o seu centro de eventos. Os eventos são leituras ambientais simples enviadas em JSON.

1. Abra o seu editor python favorito, como [Visual Studio Code.][Visual Studio Code]
2. Crie um novo ficheiro chamado *sender.py*. 
3. Cole o seguinte código em *sender.py*. Substitua os seus próprios valores pelos Centros de \<namespace> \<AccessKeyName> Eventos, e \<primary key value> \<eventhub> .
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Guarde o ficheiro.

## <a name="create-a-python-script-to-read-capture-files"></a>Crie um script Python para ler ficheiros de captura

Este script lê os ficheiros capturados e cria um ficheiro para cada um dos seus dispositivos escrever os dados apenas para esse dispositivo.

1. No seu editor Python, crie um novo ficheiro chamado *capturereader.py*. 
2. Cole o seguinte código em *capturereader.py*. Substitua os seus valores guardados pelo seu \<storageaccount> \<storage account access key> , e \<storagecontainer> .
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Executar os scripts Python

1. Abra um pedido de comando que tenha Python no seu caminho, e executar estes comandos para instalar os pacotes pré-requisitos Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Se tiver uma versão anterior de `azure-storage` `azure` ou, poderá ter de utilizar a `--upgrade` opção.
   
   Também pode ter de executar o seguinte comando. Executar este comando não é necessário na maioria dos sistemas. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Do diretório onde guardou *sender.py* e *capturereader.py,* executar este comando:
   
   ```cmd
   start python sender.py
   ```
   
   O comando inicia um novo processo python para executar o remetente.
   
3. Quando a captura terminar de funcionar, executa este comando:
   
   ```cmd
   python capturereader.py
   ```

   O processador de captura descarrega todas as bolhas não vazias do recipiente da conta de armazenamento e escreve os resultados como *ficheiros .csv* no diretório local. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Centros de Eventos, consulte: 

* [Visão geral da captura de centros de eventos][Overview of Event Hubs Capture]
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Descrição geral dos Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
