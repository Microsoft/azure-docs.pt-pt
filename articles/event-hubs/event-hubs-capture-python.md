---
title: 'Início rápido: ler dados capturados do aplicativo Python – hubs de eventos do Azure'
description: 'Início rápido: scripts que usam o SDK do Python do Azure para demonstrar o recurso de captura de hubs de eventos.'
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
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187296"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Quickstart: Event Hubs Capture walkthrough: Python (versão azure-eventhub 1)

Captura é uma funcionalidade dos Hubs de eventos do Azure. Você pode usar a captura para entregar automaticamente os dados de streaming em seu hub de eventos para uma conta de armazenamento de BLOBs do Azure de sua escolha. Esse recurso facilita o processamento em lotes em dados de streaming em tempo real. Este artigo descreve como utilizar a captura de Hubs de eventos com o Python. Para mais informações sobre a Captura de Hubs de Eventos, consulte [os eventos de Captura através dos Hubs de Eventos Azure][Overview of Event Hubs Capture].

Este walkthrough usa o [Azure Python SDK](https://azure.microsoft.com/develop/python/) para demonstrar a funcionalidade Capture. O programa *sender.py* envia telemetria ambiental simulada para Centros de Eventos em formato JSON. O Hub de eventos usa o recurso de captura para gravar esses dados no armazenamento de BLOBs em lotes. A aplicação *capturereader.py* lê estas bolhas, cria um ficheiro de apêndice para cada um dos seus dispositivos e escreve os dados para *ficheiros .csv* em cada dispositivo.

> [!WARNING]
> Este quickstart é para a versão 1 do Azure Event Hubs Python SDK. Recomendamos que [emigra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) o seu código para a [versão 5 do Python SDK](get-started-capture-python-v2.md).

Neste tutorial, você: 

> [!div class="checklist"]
> * Crie uma conta de armazenamento de BLOBs do Azure e o contêiner no portal do Azure.
> * Habilite a captura de hubs de eventos e direcione-o para sua conta de armazenamento.
> * Envie dados para o Hub de eventos usando um script Python.
> * Ler e processar arquivos da captura de hubs de eventos usando outro script Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 3.4 ou mais tarde, com `pip` instalados e atualizados.
  
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
  
- Um espaço de nome ative Event Hubs e centro de eventos, criado seguindo as instruções na [Quickstart: Criar um hub de eventos utilizando](event-hubs-create.md)o portal Azure . Anote o namespace e os nomes do hub de eventos a serem usados mais adiante neste guia. 
  
  > [!NOTE]
  > Se você já tiver um contêiner de armazenamento para usar, poderá habilitar a captura e selecionar o contêiner de armazenamento ao criar o Hub de eventos. 
  > 
  
- O nome da chave de acesso compartilhado dos hubs de eventos e o valor da chave primária. Encontre ou crie estes valores ao abrigo de políticas de **acesso partilhado** na página do Event Hubs. O nome da chave de acesso predefinido é **RootManageSharedAccessKey**. Copie o nome da chave de acesso e o valor da chave primária para usar posteriormente neste passo a passos. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Criar uma conta de armazenamento de BLOBs do Azure e um contêiner

Crie uma conta de armazenamento e um contêiner para usar na captura. 

1. Inicie sessão no [Portal do Azure][Azure portal].
2. Na navegação à esquerda, selecione **contas de Armazenamento**, e no ecrã de contas de **Armazenamento,** selecione **Adicionar**.
3. Na tela de criação da conta de armazenamento, selecione uma assinatura e um grupo de recursos e dê um nome à conta de armazenamento. Você pode deixar as outras seleções no padrão. Selecione **Rever + criar,** rever as definições e, em seguida, selecionar **Criar**. 
   
   ![Criar conta de armazenamento][1]
   
4. Quando a implementação estiver concluída, selecione **Ir para o recurso**, e na conta de armazenamento Ecrã de visão **geral,** selecione **Recipientes**.
5. No ecrã dos **recipientes,** **selecione + Recipiente**. 
6. No ecrã do **recipiente Novo,** dê um nome ao recipiente e, em seguida, selecione **OK**. Anote o nome do contêiner a ser usado posteriormente no guia de explicação. 
7. Na navegação à esquerda do ecrã dos **contentores,** selecione **teclas de acesso**. Copie o nome da **conta de armazenamento**, e o valor **chave** em **chave1**, para utilizar mais tarde no walkthrough.
 
## <a name="enable-event-hubs-capture"></a>Habilitar captura de hubs de eventos

1. No portal Azure, navegue até ao seu centro de eventos selecionando o seu Espaço nome de Hubs de Eventos de **Todos os recursos,** selecionando **os hubs do Evento** na navegação à esquerda e, em seguida, selecionando o seu centro de eventos. 
2. No ecrã **de visão geral** do centro de eventos, selecione **eventos de captura**.
3. No ecrã **'Capturar',** selecione **On**. Em seguida, em baixo do recipiente de **armazenamento Azure,** selecione **Selecione recipiente**. 
4. No ecrã **dos Recipientes,** selecione o recipiente de armazenamento que pretende utilizar e, em seguida, selecione **Selecione**. 
5. No ecrã **'Capturar',** selecione **Guardar alterações**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Criar um script Python para enviar eventos ao Hub de eventos
Este script para enviar 200 eventos para o hub de eventos. Os eventos são leituras ambientais simples enviadas em JSON.

1. Abra o seu editor favorito da Python, como [Visual Studio Code.][Visual Studio Code]
2. Crie um novo ficheiro chamado *sender.py.* 
3. Colá-lo no seguinte código em *sender.py*. Substitua os seus próprios valores pelos Hubs de Eventos \<namespace>, \<AccessKeyName>, \<principal valor chave>e \<eventhub>.
   
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

1. No seu editor python, crie um novo ficheiro chamado *capturereader.py.* 
2. Colhe o seguinte código em *capturereader.py*. Substitua os seus valores guardados pela sua \<storageaccount>, \<de acesso à conta de armazenamento chave>e \<storagecontainer>.
   
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
   
   Se tiver uma versão anterior de `azure-storage` ou `azure`, poderá ter de utilizar a opção `--upgrade`.
   
   Talvez você também precise executar o comando a seguir. A execução desse comando não é necessária na maioria dos sistemas. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Do diretório onde guardou *sender.py* e *capturereader.py,* dirija este comando:
   
   ```cmd
   start python sender.py
   ```
   
   O comando inicia um novo processo do Python para executar o remetente.
   
3. Quando a captura terminar de ser executada, execute este comando:
   
   ```cmd
   python capturereader.py
   ```

   O processador de captura descarrega todas as bolhas não vazias do recipiente da conta de armazenamento e escreve os resultados como *ficheiros .csv* no diretório local. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os hubs de eventos, consulte: 

* [Visão geral da captura de hubs de eventos][Overview of Event Hubs Capture]
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
