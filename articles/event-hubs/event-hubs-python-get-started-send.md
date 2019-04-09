---
title: Enviar eventos com Python - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece um passo a passo para criar uma aplicação de Python para enviar eventos para Hubs de eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/16/2018
ms.author: shvija
ms.openlocfilehash: 2168fc89134615ffb4e0e718cc0cc27b8c1a7839
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262776"
---
# <a name="send-events-to-event-hubs-using-python"></a>Enviar eventos para Hubs de eventos com Python

Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um hub de eventos de um aplicativo escrito em Python. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Python 3.4 ou posterior.


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um hub de eventos
O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md).

Obter o valor da chave de acesso para o hub de eventos ao seguir as instruções do artigo: [Obter cadeia de ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a chave de acesso no código escrito mais tarde neste tutorial. O nome da chave predefinida é: **RootManageSharedAccessKey**.

Agora, continue com os seguintes passos neste tutorial.

## <a name="install-python-package"></a>Instalar pacote do Python

Para instalar o pacote do Python para os Hubs de eventos, abra um prompt de comando que tem o Python no respetivo caminho e, em seguida, execute este comando: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Criar um script de Python para enviar eventos

Em seguida, crie uma aplicação de Python para enviar eventos para um hub de eventos:

1. Abra o seu editor favorito do Python, tal como [Visual Studio Code][Visual Studio Code].
2. Criar um script chamado **send.py**. Este script para enviar eventos de 100 para o hub de eventos.
3. Cole o seguinte código no send.py, substituindo os valores de endereço, utilizador e a chave com os valores que obteve a partir do portal do Azure na secção anterior: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

## <a name="run-application-to-send-events"></a>Executar aplicação para enviar eventos

Para executar o script, abra um prompt de comando que tem o Python no respetivo caminho e, em seguida, execute este comando:

```bash
start python send.py
```

Parabéns! Enviou agora mensagens para um hub de eventos.
 
## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, enviou mensagens para um hub de eventos com o Python. Para saber como receber eventos de um hub de eventos com o Python, veja [receber eventos do hub de eventos - Python](event-hubs-python-get-started-receive.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
