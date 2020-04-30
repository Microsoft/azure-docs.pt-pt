---
title: Envie ou receba eventos de Azure Event Hubs usando JavaScript (legado)
description: Este artigo fornece um walkthrough para criar uma aplicação JavaScript que envia/recebe eventos de/para Azure Event Hubs usando o antigo pacote azure/event-hubs versão 2.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162621"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Quickstart: Envie eventos ou receba eventos de Azure Event Hubs usando javaScript (versão@azure/event-hubs 2)
Este quickstart mostra como criar aplicações JavaScript para enviar eventos e receber eventos a partir de um hub de eventos usando o pacote JavaScript azure/event-hubs. 

> [!WARNING]
> Este quickstart utiliza o antigo pacote de versão 2 azure/event-hubs. Para um arranque rápido que utilize a versão mais recente **5** do pacote, consulte [Enviar e receber eventos utilizando a versão 5](get-started-node-send-v2.md). Para mover a sua aplicação da utilização do pacote antigo para um novo, consulte o [Guia para migrar da versão 1 do Azure/Eventhubs para a versão 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Pré-requisitos

Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- Node.js versão 8.x e superior. Descarregue a versão [https://nodejs.org](https://nodejs.org)LTS mais recente a partir de .
- Código de Estúdio Visual (recomendado) ou qualquer outro IDE
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um hub de eventos, siga o procedimento [neste artigo,](event-hubs-create.md)em seguida, continue com os seguintes passos neste tutorial. Em seguida, obtenha a corda de ligação para o espaço de nome do centro do evento seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Usa a corda de ligação mais tarde neste tutorial.


### <a name="install-npm-package"></a>Instale pacote npm
Para instalar o pacote npm para Centros de `npm` [Eventos,](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)abra um pedido de comando que tenha no seu caminho, mude o diretório para a pasta onde pretende ter as suas amostras e, em seguida, executar este comando

```shell
npm install @azure/event-hubs@2
```

Para instalar o [pacote npm para anfitrião](https://www.npmjs.com/package/@azure/event-processor-host)do processador de eventos, executar o comando abaixo

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Enviar eventos

Esta secção mostra-lhe como criar uma aplicação JavaScript que envia eventos para um centro de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um `send.js` ficheiro chamado e colá-lo o código abaixo nele. Obtenha a corda de ligação para o espaço de nome do centro de eventos seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Introduza a cadeia de ligação e o nome do seu Event Hub no código acima
4. Em seguida, `node send.js` execute o comando num pedido de comando para executar este ficheiro. Isto enviará 100 eventos para o seu Hub de Eventos

Parabéns! Agora enviaste eventos para um centro de eventos.


## <a name="receive-events"></a>Receber eventos

Esta secção mostra-lhe como criar uma aplicação JavaScript que recebe eventos de uma única divisão do grupo de consumidores padrão num centro de eventos. 

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um `receive.js` ficheiro chamado e colá-lo o código abaixo nele.
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
3. Introduza a cadeia de ligação e o nome do seu Event Hub no código acima.
4. Em seguida, `node receive.js` execute o comando num pedido de comando para executar este ficheiro. Isto receberá eventos de uma das divisórias do grupo de consumidores padrão no seu Event Hub

Parabéns! Recebeu eventos do centro de eventos.

## <a name="receive-events-using-event-processor-host"></a>Receber eventos com o Anfitrião de Processador de Eventos

Esta secção mostra como receber eventos a partir de um hub de eventos utilizando o Azure [EventProcessorHost](event-hubs-event-processor-host.md) numa aplicação JavaScript. O EventProcessorHost (EPH) ajuda-o a receber eventos de forma eficiente a partir de um hub de eventos, criando recetores em todas as divisórias do grupo de consumidores de um hub de eventos. Verifica metadados em mensagens recebidas em intervalos regulares numa Blob de Armazenamento Azure. Esta abordagem facilita a continuação de receber mensagens de onde deixou de lado mais tarde.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um `receiveAll.js` ficheiro chamado e colá-lo o código abaixo nele.
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
3. Introduza a cadeia de ligação e o nome do seu Event Hub no código acima, juntamente com a cadeia de ligação para um Armazenamento De Blob Azure
4. Em seguida, `node receiveAll.js` execute o comando num pedido de comando para executar este ficheiro.

Parabéns! Já recebeu eventos do centro de eventos usando o Anfitrião do Processador de Eventos. Isto receberá eventos de todas as divisórias do grupo de consumidores padrão no seu Event Hub

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)
- Confira outras amostras javaScript para [Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) de Eventos e Anfitrião de Processador de [Eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) no GitHub
