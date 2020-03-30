---
title: 'Quickstart: Leia os dados capturados da app Python - Azure Event Hubs'
description: 'Quickstart: Scripts que usam o SDK Azure Python para demonstrar a funcionalidade de captura de Hubs de Eventos.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187296"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Quickstart: Event Hubs Capture walkthrough: Python (versão azure-eventhub 1)

Capture é uma característica do Azure Event Hubs. Pode utilizar o Capture para entregar automaticamente os dados de streaming no seu centro de eventos a uma conta de armazenamento Azure Blob à sua escolha. Esta capacidade facilita o processamento de lotes em dados de streaming em tempo real. Este artigo descreve como usar os Hubs de Evento scapture com Python. Para mais informações sobre a Captura de Hubs de Eventos, consulte [os eventos de Captura através dos Hubs de Eventos Azure][Overview of Event Hubs Capture].

Este walkthrough usa o [Azure Python SDK](https://azure.microsoft.com/develop/python/) para demonstrar a funcionalidade Capture. O programa *sender.py* envia telemetria ambiental simulada para Centros de Eventos em formato JSON. O hub do evento utiliza a funcionalidade Capture para escrever estes dados para o armazenamento blob em lotes. A aplicação *capturereader.py* lê estas bolhas, cria um ficheiro de apêndice para cada um dos seus dispositivos e escreve os dados para *ficheiros .csv* em cada dispositivo.

> [!WARNING]
> Este quickstart é para a versão 1 do Azure Event Hubs Python SDK. Recomendamos que [emigra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) o seu código para a [versão 5 do Python SDK](get-started-capture-python-v2.md).

Nesta passagem, tu: 

> [!div class="checklist"]
> * Crie uma conta de armazenamento e recipiente Azure Blob no portal Azure.
> * Ative a Captura de Centros de Eventos e direcione-o para a sua conta de armazenamento.
> * Envie dados para o seu centro de eventos usando um script Python.
> * Leia e processe ficheiros a partir de Hubs de Eventos Capture usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3.4 ou `pip` posterior, com instalado e atualizado.
  
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
  
- Um espaço de nome ative Event Hubs e centro de eventos, criado seguindo as instruções na [Quickstart: Criar um hub de eventos utilizando](event-hubs-create.md)o portal Azure . Tome nota do seu espaço de nome e nomes do centro de eventos para usar mais tarde neste walkthrough. 
  
  > [!NOTE]
  > Se já tem um recipiente de armazenamento para utilizar, pode ativar a Captura e selecionar o recipiente de armazenamento quando criar o Centro de Eventos. 
  > 
  
- O seu Nome chave de acesso partilhado do Seu Event Hubs e o principal valor-chave. Encontre ou crie estes valores ao abrigo de políticas de **acesso partilhado** na página do Event Hubs. O nome da chave de acesso predefinido é **RootManageSharedAccessKey**. Copie o nome da chave de acesso e o valor-chave principal para usar mais tarde neste walkthrough. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Criar uma conta de armazenamento e recipiente Azure Blob

Crie uma conta de armazenamento e um recipiente para a captura. 

1. Inicie sessão no [Portal do Azure][Azure portal].
2. Na navegação à esquerda, selecione **contas de Armazenamento**, e no ecrã de contas de **Armazenamento,** selecione **Adicionar**.
3. No ecrã de criação da conta de armazenamento, selecione um grupo de subscrição e recursos e dê um nome à conta de armazenamento. Pode deixar as outras seleções em incumprimento. Selecione **Rever + criar,** rever as definições e, em seguida, selecionar **Criar**. 
   
   ![Criar conta de armazenamento][1]
   
4. Quando a implementação estiver concluída, selecione **Ir para o recurso**, e na conta de armazenamento Ecrã de visão **geral,** selecione **Recipientes**.
5. No ecrã dos **recipientes,** **selecione + Recipiente**. 
6. No ecrã do **recipiente Novo,** dê um nome ao recipiente e, em seguida, selecione **OK**. Tome nota do nome do recipiente para utilizar mais tarde no walkthrough. 
7. Na navegação à esquerda do ecrã dos **contentores,** selecione **teclas de acesso**. Copie o nome da **conta de armazenamento**, e o valor **chave** em **chave1**, para utilizar mais tarde no walkthrough.
 
## <a name="enable-event-hubs-capture"></a>Ativar a captura de centros de eventos

1. No portal Azure, navegue até ao seu centro de eventos selecionando o seu Espaço nome de Hubs de Eventos de **Todos os recursos,** selecionando **os hubs do Evento** na navegação à esquerda e, em seguida, selecionando o seu centro de eventos. 
2. No ecrã **de visão geral** do centro de eventos, selecione **eventos de captura**.
3. No ecrã **'Capturar',** selecione **On**. Em seguida, em baixo do recipiente de **armazenamento Azure,** selecione **Selecione recipiente**. 
4. No ecrã **dos Recipientes,** selecione o recipiente de armazenamento que pretende utilizar e, em seguida, selecione **Selecione**. 
5. No ecrã **'Capturar',** selecione **Guardar alterações**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Crie um roteiro Python para enviar eventos para o Event Hub
Este guião envia 200 eventos para o seu centro de eventos. Os eventos são leituras ambientais simples enviadas na JSON.

1. Abra o seu editor favorito da Python, como [Visual Studio Code.][Visual Studio Code]
2. Crie um novo ficheiro chamado *sender.py.* 
3. Colá-lo no seguinte código em *sender.py*. Substitua os seus próprios \<valores \<pelo espaço de \<nome saque \<de Eventos>, AccessKeyName>,> de valor chave primário e> do eventhub.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Crie um roteiro Python para ler ficheiros de captura

Este script lê os ficheiros capturados e cria um ficheiro para cada um dos seus dispositivos escrever os dados apenas para esse dispositivo.

1. No seu editor python, crie um novo ficheiro chamado *capturereader.py.* 
2. Colhe o seguinte código em *capturereader.py*. Substitua os valores guardados pela sua \<> de armazenagem, \<chave de acesso à conta de armazenamento> e \<> de armazenamento.
   
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

1. Abra uma solicitação de comando que tenha python no seu caminho, e executar estes comandos para instalar os pacotes pré-requisito python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Se tiver uma versão `azure-storage` anterior `azure`ou, poderá ter `--upgrade` de utilizar a opção.
   
   Também pode sê-lo. Dirigir este comando não é necessário na maioria dos sistemas. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Do diretório onde guardou *sender.py* e *capturereader.py,* dirija este comando:
   
   ```cmd
   start python sender.py
   ```
   
   O comando inicia um novo processo python para executar o remetente.
   
3. Quando a captura terminar de correr, execute este comando:
   
   ```cmd
   python capturereader.py
   ```

   O processador de captura descarrega todas as bolhas não vazias do recipiente da conta de armazenamento e escreve os resultados como *ficheiros .csv* no diretório local. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Centros de Eventos, consulte: 

* [Visão geral da captura de hubs de eventos][Overview of Event Hubs Capture]
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Descrição geral dos Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
