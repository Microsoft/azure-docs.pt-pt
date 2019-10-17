---
title: Ler dados capturados do aplicativo Python – hubs de eventos do Azure | Microsoft Docs
description: Scripts que usam o SDK do Python do Azure para demonstrar o recurso de captura de hubs de eventos.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 10/10/2019
ms.author: shvija
ms.openlocfilehash: 354964e1b66b55dcccd9b5674f011f8c5a38a1c5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428944"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Instruções de captura de hubs de eventos: Python

A captura é um recurso dos hubs de eventos do Azure. Você pode usar a captura para entregar automaticamente os dados de streaming em seu hub de eventos para uma conta de armazenamento de BLOBs do Azure de sua escolha. Esse recurso facilita o processamento em lotes em dados de streaming em tempo real. Este artigo descreve como usar a captura de hubs de eventos com o Python. Para obter mais informações sobre a captura de hubs de eventos, consulte [capturar eventos por meio dos hubs de eventos do Azure][Overview of Event Hubs Capture].

Este tutorial usa o [SDK do Python do Azure](https://azure.microsoft.com/develop/python/) para demonstrar o recurso de captura. O programa *Sender.py* envia telemetria de ambiente simulada para os hubs de eventos no formato JSON. O Hub de eventos usa o recurso de captura para gravar esses dados no armazenamento de BLOBs em lotes. O aplicativo *capturereader.py* lê esses BLOBs, cria um arquivo de acréscimo para cada um dos dispositivos e grava os dados em arquivos *. csv* em cada dispositivo.

Neste tutorial, você: 

> [!div class="checklist"]
> * Crie uma conta de armazenamento de BLOBs do Azure e o contêiner no portal do Azure.
> * Habilite a captura de hubs de eventos e direcione-o para sua conta de armazenamento.
> * Envie dados para o Hub de eventos usando um script Python.
> * Ler e processar arquivos da captura de hubs de eventos usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3,4 ou posterior, com `pip` instalado e atualizado.
  
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
  
- Um namespace de hubs de eventos ativo e um hub de eventos, criados seguindo as instruções em [início rápido: criar um hub de eventos usando portal do Azure](event-hubs-create.md). Anote o namespace e os nomes do hub de eventos a serem usados mais adiante neste guia. 
  
  > [!NOTE]
  > Se você já tiver um contêiner de armazenamento para usar, poderá habilitar a captura e selecionar o contêiner de armazenamento ao criar o Hub de eventos. 
  > 
  
- O nome da chave de acesso compartilhado dos hubs de eventos e o valor da chave primária. Localize ou crie esses valores em **políticas de acesso compartilhado** em sua página de hubs de eventos. O nome da chave de acesso padrão é **RootManageSharedAccessKey**. Copie o nome da chave de acesso e o valor da chave primária para usar posteriormente neste passo a passos. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Criar uma conta de armazenamento de BLOBs do Azure e um contêiner

Crie uma conta de armazenamento e um contêiner para usar na captura. 

1. Inicie sessão no [portal do Azure][Azure portal].
2. No painel de navegação esquerdo, selecione **contas de armazenamento**e, na tela contas de **armazenamento** , selecione **Adicionar**.
3. Na tela de criação da conta de armazenamento, selecione uma assinatura e um grupo de recursos e dê um nome à conta de armazenamento. Você pode deixar as outras seleções no padrão. Selecione **revisão + criar**, examine as configurações e, em seguida, selecione **criar**. 
   
   ![Criar conta de armazenamento][1]
   
4. Quando a implantação for concluída, selecione **ir para recurso**e, na tela **visão geral** da conta de armazenamento, selecione **contêineres**.
5. Na tela **contêineres** , selecione **+ contêiner**. 
6. Na tela **novo contêiner** , dê um nome ao contêiner e selecione **OK**. Anote o nome do contêiner a ser usado posteriormente no guia de explicação. 
7. No painel de navegação à esquerda da tela **contêineres** , selecione **chaves de acesso**. Copie o **nome da conta de armazenamento**e o valor da **chave** em **key1**, para usar posteriormente no passo a passo.
 
## <a name="enable-event-hubs-capture"></a>Habilitar captura de hubs de eventos

1. No portal do Azure, navegue até o Hub de eventos selecionando seu namespace de hubs de eventos de **todos os recursos**, selecionando **hubs de eventos** no painel de navegação esquerdo e, em seguida, selecionando o Hub de eventos. 
2. Na tela **visão geral** do hub de eventos, selecione **capturar eventos**.
3. Na tela **capturar** , selecione **ativado**. Em seguida, em **contêiner de armazenamento do Azure**, selecione **selecionar contêiner**. 
4. Na tela **contêineres** , selecione o contêiner de armazenamento que você deseja usar e selecione **selecionar**. 
5. Na tela **capturar** , selecione **salvar alterações**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Criar um script Python para enviar eventos ao Hub de eventos
Esse script envia eventos 200 para o Hub de eventos. Os eventos são leituras ambientais simples enviadas em JSON.

1. Abra seu editor de Python favorito, como [Visual Studio Code][Visual Studio Code].
2. Crie um novo arquivo chamado *Sender.py*. 
3. Cole o código a seguir em *Sender.py*. Substitua seus próprios valores para os hubs de eventos \<namespace >, \<AccessKeyName >, \<primary valor da chave > e \<eventhub >.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Criar um script Python para ler arquivos de captura

Esse script lê os arquivos capturados e cria um arquivo para cada um de seus dispositivos para gravar os dados somente para esse dispositivo.

1. No seu editor de Python, crie um novo arquivo chamado *capturereader.py*. 
2. Cole o código a seguir em *capturereader.py*. Substitua os valores salvos por sua > \<storageaccount, @no__t chave de acesso da conta do 1storage > e @no__t >-2storagecontainer.
   
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

## <a name="run-the-python-scripts"></a>Executar os scripts do Python

1. Abra um prompt de comando que tenha o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Se você tiver uma versão anterior do `azure-storage` ou `azure`, talvez seja necessário usar a opção `--upgrade`.
   
   Talvez você também precise executar o comando a seguir. A execução desse comando não é necessária na maioria dos sistemas. 
   
   ```cmd
   pip install cryptography
   ```
   
2. No diretório em que você salvou *Sender.py* e *capturereader.py*, execute este comando:
   
   ```cmd
   start python sender.py
   ```
   
   O comando inicia um novo processo do Python para executar o remetente.
   
3. Quando a captura terminar de ser executada, execute este comando:
   
   ```cmd
   python capturereader.py
   ```

   O processador de captura baixa todos os BLOBs não vazios do contêiner da conta de armazenamento e grava os resultados como arquivos *. csv* no diretório local. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os hubs de eventos, consulte: 

* [Visão geral da captura de hubs de eventos][Overview of Event Hubs Capture]
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
