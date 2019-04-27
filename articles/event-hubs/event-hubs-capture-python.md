---
title: Leitura capturados dados da aplicação do Python - Event Hubs do Azure | Documentos da Microsoft
description: Exemplo que utiliza o SDK Python para demonstrar a utilização da funcionalidade de captura de Hubs de eventos.
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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822392"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Instruções da captura dos Hubs de eventos: Python

Captura é uma funcionalidade dos Hubs de eventos do Azure. Pode usá-lo para fornecer automaticamente os dados de transmissão em fluxo no seu hub de eventos para uma conta de armazenamento de Blobs do Azure à sua escolha. Esta capacidade torna mais fácil realizar o processamento em lotes nas dados de transmissão em fluxo em tempo real. Este artigo descreve como utilizar a captura de Hubs de eventos com o Python. Para obter mais informações sobre a captura de Hubs de eventos, consulte a [artigo de descrição geral](event-hubs-capture-overview.md).

Este exemplo utiliza a [Azure Python SDK](https://azure.microsoft.com/develop/python/) para demonstrar a funcionalidade de captura. O programa de sender.py envia telemetria ambiental simulada para os Hubs de eventos no formato JSON. O hub de eventos está configurado para utilizar a funcionalidade de captura para escrever dados no armazenamento de BLOBs em lotes. A aplicação de capturereader.py lê nestes blobs e cria um ficheiro de acréscimo por dispositivo. A aplicação, em seguida, escreve os dados em ficheiros. csv.

## <a name="what-youll-accomplish"></a>O que irá realizar

1. Crie uma conta de armazenamento de Blobs do Azure e um contentor de BLOBs dentro da mesma, utilizando o portal do Azure.
2. Crie um espaço de nomes de Hubs de eventos, com o portal do Azure.
3. Crie um hub de eventos com a funcionalidade de captura ativada, ao utilizar o portal do Azure.
4. Envie dados para o hub de eventos utilizando um script de Python.
5. Ler os ficheiros a captura e processá-las através de outro script de Python.

## <a name="prerequisites"></a>Pré-requisitos

- Python 2.7.x
- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um ativo [hub de eventos e de espaço de nomes de Hubs de eventos](event-hubs-create.md). 
- Ativar **capturar** funcionalidade para o hub de eventos ao seguir as instruções de: [Ativar a captura de Hubs de eventos com o portal do Azure](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure
1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel esquerdo do portal, selecione **New** > **armazenamento** > **conta de armazenamento**.
3. Concluir as seleções na **criar conta de armazenamento** painel e, em seguida, selecione **criar**.
   
   ![Painel "Criar a conta de armazenamento"][1]
4. Depois de confirmar a **implementações bem-sucedidas** da mensagem, selecione o nome da nova conta de armazenamento e, no **Essentials** painel e, em seguida, selecione **Blobs**. Quando o **serviço Blob** painel abre, selecione **+ contentor** na parte superior. Nome do contentor **capturar**e, em seguida, feche a **serviço Blob** painel.
5. Selecione **chaves de acesso** no painel esquerdo e copie o nome da conta de armazenamento e o valor de **chave1**. Guarde estes valores no Bloco de Notas ou noutra localização temporária.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script de Python para enviar eventos para o seu hub de eventos
1. Abra o seu editor favorito do Python, tal como [Visual Studio Code][Visual Studio Code].
2. Criar um script chamado **sender.py**. Este script para enviar 200 eventos para o hub de eventos. Eles são simples leituras ambientais enviadas em JSON.
3. Cole o seguinte código no sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Atualize o código anterior para utilizar o seu espaço de nomes, o valor da chave e o nome do hub de eventos que obteve quando criou o espaço de nomes de Hubs de eventos.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Criar um script de Python para ler os ficheiros de captura

1. Preencha o painel e selecione **criar**.
2. Criar um script chamado **capturereader.py**. Este script lê os arquivos capturados e cria um ficheiro por dispositivo para escrever os dados apenas para esse dispositivo.
3. Cole o seguinte código no capturereader.py:
   
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
4. Cole os valores adequados para o nome da conta de armazenamento e a chave na chamada para `startProcessing`.

## <a name="run-the-scripts"></a>Execute os scripts
1. Abra um prompt de comando que tem o Python no respetivo caminho e, em seguida, execute estes comandos para instalar pacotes Python de pré-requisitos:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Se tiver uma versão anterior do **azure-storage** ou **azure**, poderá ter de utilizar o **– atualizar** opção.
   
   Poderá também ter de executar o seguinte comando (não necessário na maioria dos sistemas):
   
   ```
   pip install cryptography
   ```
2. Altere o diretório para onde salvou sender.py e capturereader.py e execute este comando:
   
   ```
   start python sender.py
   ```
   
   Este comando inicia um novo processo de Python para executar o remetente.
3. Aguarde alguns minutos para que a captura executar. Em seguida, escreva o seguinte comando na sua janela de comando original:
   
   ```
   python capturereader.py
   ```

   Este processador de captura utiliza o diretório de local para transferir todos os blobs da conta/contentor de armazenamento. Processa qualquer um que não estão vazias e escreve os resultados como ficheiros. csv para o diretório local.

## <a name="next-steps"></a>Passos Seguintes

Pode saber mais sobre os Hubs de eventos, utilizando as ligações seguintes:

* [Captura de descrição geral dos Hubs de eventos][Overview of Event Hubs Capture]
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
