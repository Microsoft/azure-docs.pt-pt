---
title: Enviar ou receber eventos de Azure Event Hubs usando JavaScript (legado)
description: Este artigo fornece uma passagem para a criação de uma aplicação JavaScript que envia/recebe eventos de/para Azure Event Hubs usando o antigo pacote azure/event-hubs versão 2.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: ffa6b821d617731bd6ee5cbb9bec8bd6d856a7a6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414127"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Quickstart: Enviar eventos para ou receber eventos de Azure Event Hubs usando JavaScript @azure/event-hubs (versão 2)
Este quickstart mostra como criar aplicações JavaScript para enviar eventos e receber eventos de um centro de eventos usando o pacote JavaScript da versão azure/event-hubs. 

> [!WARNING]
> Este quickstart utiliza o antigo pacote azure/event-hubs versão 2. Para um arranque rápido que utilize a **versão 5** mais recente do pacote, consulte [Enviar e receber eventos utilizando a versão 5 do azure/eventhubs](get-started-node-send-v2.md). Para passar a sua aplicação da utilização do pacote antigo para um novo, consulte o [Guia para migrar da versão 1 do azul/eventhubs para a versão 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Pré-requisitos

Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- Node.js versão 8.x e superior. Descarregue a versão LTS mais recente a partir de [https://nodejs.org](https://nodejs.org) .
- Código visual do estúdio (recomendado) ou qualquer outro IDE
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo,](event-hubs-create.md)em seguida, continue com os seguintes passos neste tutorial. Em seguida, obtenha a cadeia de ligação para o espaço de nome do centro do evento seguindo as instruções do artigo: Obter a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Use a cadeia de ligação mais tarde neste tutorial.


### <a name="install-npm-package"></a>Instalar pacote npm
Para instalar o [pacote npm para Os Centros de Eventos,](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)abra um pedido de comando que tem `npm` no seu caminho, altere o diretório para a pasta onde pretende ter as suas amostras e, em seguida, executar este comando

```shell
npm install @azure/event-hubs@2
```

Para instalar o [pacote npm para anfitrião do processador de eventos,](https://www.npmjs.com/package/@azure/event-processor-host)em vez disso, executar o comando abaixo

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Enviar eventos

Esta secção mostra-lhe como criar uma aplicação JavaScript que envia eventos para um centro de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

1. Abra o seu editor favorito, como [Visual Studio Code.](https://code.visualstudio.com) 
2. Crie um ficheiro chamado `send.js` e cole o código abaixo nele. Obtenha a cadeia de ligação para o espaço de nome do centro de eventos seguindo as instruções do artigo: Obtenha a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Introduza a cadeia de ligação e o nome do seu Centro de Eventos no código acima
4. Em seguida, execute o comando `node send.js` num pedido de comando para executar este ficheiro. Isto irá enviar 100 eventos para o seu Centro de Eventos

Parabéns! Agora enviaste eventos para um centro de eventos.


## <a name="receive-events"></a>Receber eventos

Esta secção mostra-lhe como criar uma aplicação JavaScript que recebe eventos de uma única divisão do grupo de consumidores padrão num centro de eventos. 

1. Abra o seu editor favorito, como [Visual Studio Code.](https://code.visualstudio.com) 
2. Crie um ficheiro chamado `receive.js` e cole o código abaixo nele.
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
3. Introduza a cadeia de ligação e o nome do seu Centro de Eventos no código acima.
4. Em seguida, execute o comando `node receive.js` num pedido de comando para executar este ficheiro. Isto receberá eventos de uma das divisórias do grupo de consumidores padrão no seu Event Hub

Parabéns! Já recebeu eventos do centro de eventos.

## <a name="receive-events-using-event-processor-host"></a>Receber eventos com o Anfitrião de Processador de Eventos

Esta secção mostra como receber eventos de um centro de eventos utilizando o Azure [EventProcessorHost](event-hubs-event-processor-host.md) numa aplicação JavaScript. O EventProcessorHost (EPH) ajuda-o a receber eventos eficientemente a partir de um centro de eventos, criando recetores em todas as divisórias do grupo de consumidores de um centro de eventos. Verifica metadados em mensagens recebidas em intervalos regulares numa Bolha de Armazenamento Azure. Esta abordagem facilita a continuação de mensagens de onde paraste mais tarde.

1. Abra o seu editor favorito, como [Visual Studio Code.](https://code.visualstudio.com) 
2. Crie um ficheiro chamado `receiveAll.js` e cole o código abaixo nele.
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
3. Introduza a cadeia de ligação e o nome do seu Centro de Eventos no código acima, juntamente com a cadeia de ligação para um armazenamento de blob Azure
4. Em seguida, execute o comando `node receiveAll.js` num pedido de comando para executar este ficheiro.

Parabéns! Já recebeu eventos do centro de eventos usando o Host do Processador de Eventos. Isto receberá eventos de todas as divisórias do grupo de consumidores padrão no seu Event Hub

## <a name="next-steps"></a>Próximos passos
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)
- Confira outras amostras javaScript para [centros de eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e [anfitrião do processador de eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) no GitHub
