---
title: Enviar ou receber eventos de Azure Event Hubs usando Python (antigo)
description: Este walkthrough mostra-lhe como criar e executar scripts Python que enviam eventos ou recebem eventos de Azure Event Hubs usando o antigo pacote de versão 1 do azure-eventhub.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4ce53d2d82a00f98dbbd538bd7a61da9ba44e832
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314475"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Quickstart: Enviar e receber eventos com Centros de Eventos usando Python (versão azure-eventhub 1)
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando o pacote **azure-eventhub versão 1** Python. 

> [!WARNING]
> Este quickstart utiliza o antigo pacote azure-eventhub versão 1. Para um arranque rápido que utilize a **versão 5** mais recente do pacote, consulte [Enviar e receber eventos utilizando a versão 5 do azure-eventhub](get-started-python-send-v2.md). Para passar a sua aplicação de usar o pacote antigo para um novo, consulte o [Guia para migrar da versão 1 para a versão 5 do Azure-Eventhub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- Python 3.4 ou mais tarde, com `pip` instalado e atualizado.
- O pacote Python para Centros de Eventos. Para instalar o pacote, executar este comando num pedido de comando que tem Python no seu caminho: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Em seguida, obtenha o valor da chave de acesso para o centro do evento seguindo as instruções do artigo: Obtenha a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a chave de acesso no código que escreve mais tarde neste arranque rápido. O nome-chave predefinido é: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Enviar eventos

Para criar uma aplicação Python que envia eventos para um centro de eventos:

> [!NOTE]
> Em vez de trabalhar através do quickstart, você pode baixar e executar as [aplicações](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) de amostra do GitHub. Substitua as `EventHubConnectionString` cordas e `EventHubName` os valores do seu centro de eventos.

1. Abra o seu editor python favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um novo ficheiro chamado *send.py*. Este script envia 100 eventos para o seu centro de eventos.
3. Cole o seguinte código em *send.py,* substituindo os Centros de \<namespace> Eventos, \<eventhub> e pelos seus \<AccessKeyName> \<primary key value> valores: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Guarde o ficheiro. 

Para executar o guião, a partir do diretório onde guardou *send.py,* executar este comando:

```cmd
start python send.py
```

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="receive-events"></a>Receber eventos

Para criar uma aplicação Python que receba eventos a partir de um centro de eventos:

1. No seu editor python, crie um ficheiro chamado *recv.py*.
2. Cole o seguinte código em *recv.py,* substituindo os Centros de \<namespace> Eventos, \<eventhub> e pelos seus \<AccessKeyName> \<primary key value> valores: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Guarde o ficheiro.

Para executar o guião, a partir do diretório onde guardou *recv.py,* executar este comando:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os Centros de Eventos, consulte os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)

