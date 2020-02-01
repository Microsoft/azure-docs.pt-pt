---
title: Envie ou receba eventos de Azure Event Hubs usando Node.js (legado)
description: Este artigo fornece um walkthrough para criar uma aplicação Node.js que envia/recebe eventos de/para Azure Event Hubs usando o antigo pacote azure/event-hubs versão 2.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 9aa2418657c2d3bcab9ef8883e5bd57422ce5e29
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899888"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs-azureevent-hubs-version-2"></a>Quickstart: Envie eventos ou receba eventos de Azure Event Hubs usando o Node.js (versão@azure/event-hubs 2)

Os hubs de eventos do Azure são uma plataforma de streaming de Big data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuídos. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análises em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicativos node. js para enviar eventos ou receber eventos de um hub de eventos.

> [!WARNING]
> Este quickstart é para a versão 2 do Azure Event Hubs Java Script SDK. Recomendamos que [emigra](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md) o seu código para a [versão 5 do Java Script SDK](get-started-node-send-v2.md). 



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Versão node. js 8.x e superiores. Transferir a versão mais recente do LTS partir [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Crie um namespace de hubs de eventos e um hub de eventos**. O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento neste [artigo](event-hubs-create.md)e continue com as etapas a seguir neste tutorial. Em seguida, obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Irá utilizar a cadeia de ligação mais tarde neste tutorial.


### <a name="install-npm-package"></a>Instalar pacote NPM
Para instalar o [pacote NPM para os hubs de eventos](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0), abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta onde você deseja ter seus exemplos e, em seguida, execute este comando

```shell
npm install @azure/event-hubs@2
```

Para instalar o [pacote NPM para o host do processador de eventos](https://www.npmjs.com/package/@azure/event-processor-host), execute o comando abaixo em vez disso

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Enviar eventos

Esta seção mostra como criar um aplicativo node. js que envia eventos para um hub de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `send.js` e cole o código abaixo nele. Obter a cadeia de ligação para o espaço de nomes do hub de eventos ao seguir as instruções do artigo: [obter cadeia de ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome do seu hub de eventos no código acima
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo. Isso enviará eventos 100 para o Hub de eventos

Parabéns! Agora você enviou eventos para um hub de eventos.


## <a name="receive-events"></a>Receber eventos

Esta seção mostra como criar um aplicativo node. js que recebe eventos de uma única partição do grupo de consumidores padrão em um hub de eventos. 

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `receive.js` e cole o código abaixo nele.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome do seu hub de eventos no código acima.
4. Em seguida, execute o comando `node receive.js` em um prompt de comando para executar esse arquivo. Isso receberá eventos de uma das partições do grupo de consumidores padrão em seu hub de eventos

Parabéns! Agora você recebeu eventos do hub de eventos.

## <a name="receive-events-using-event-processor-host"></a>Receber eventos com o Anfitrião de Processador de Eventos

Esta seção mostra como receber eventos de um hub de eventos usando o Azure [EventProcessorHost](event-hubs-event-processor-host.md) em um aplicativo node. js. O EventProcessorHost (EPH) ajuda-o com eficiência receber eventos de um hub de eventos através da criação de recetores todas as partições no grupo de consumidor de um hub de eventos. Ele metadados de pontos de verificação nas mensagens recebidas em intervalos regulares num Blob de armazenamento do Azure. Essa abordagem torna mais fácil continuar a receber mensagens a partir de onde parou num momento posterior.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um arquivo chamado `receiveAll.js` e cole o código abaixo nele.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Insira a cadeia de conexão e o nome do seu hub de eventos no código acima junto com a cadeia de conexão para um armazenamento de BLOBs do Azure
4. Em seguida, execute o comando `node receiveAll.js` em um prompt de comando para executar esse arquivo.

Parabéns! Agora você recebeu eventos do hub de eventos usando o host do processador de eventos. Isso receberá eventos de todas as partições do grupo de consumidores padrão em seu hub de eventos

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)
- Confira outros exemplos de Node. js para [hubs de eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e host de processador de [eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) no github
