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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162621"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Quickstart: Envie eventos ou receba eventos de Azure Event Hubs usando javaScript (@azure/event-hubs versão 2)
Este quickstart mostra como criar aplicações JavaScript para enviar eventos e receber eventos a partir de um hub de eventos usando o pacote JavaScript azure/event-hubs. 

> [!WARNING]
> Este quickstart utiliza o antigo pacote de versão 2 azure/event-hubs. Para um arranque rápido que utilize a versão mais recente **5** do pacote, consulte [Enviar e receber eventos utilizando a versão 5](get-started-node-send-v2.md). Para mover a sua aplicação da utilização do pacote antigo para um novo, consulte o [Guia para migrar da versão 1 do Azure/Eventhubs para a versão 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Pré-requisitos

Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para concluir este guia de início rápido, você precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- Versão node. js 8.x e superiores. Descarregue a versão LTS mais recente a partir de [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um hub de eventos, siga o procedimento [neste artigo,](event-hubs-create.md)em seguida, continue com os seguintes passos neste tutorial. Em seguida, obtenha a corda de ligação para o espaço de nome do centro do evento seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Irá utilizar a cadeia de ligação mais tarde neste tutorial.


### <a name="install-npm-package"></a>Instalar pacote NPM
Para instalar o pacote npm para Centros de [Eventos,](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)abra um pedido de comando que tenha `npm` no seu caminho, mude o diretório para a pasta onde pretende ter as suas amostras e, em seguida, executar este comando

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
2. Crie um ficheiro chamado `send.js` e colá-lo o código abaixo nele. Obtenha a corda de ligação para o espaço de nome do centro de eventos seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
4. Em seguida, execute o comando `node send.js` em um pedido de comando para executar este ficheiro. Isso enviará eventos 100 para o Hub de eventos

Parabéns! Agora você enviou eventos para um hub de eventos.


## <a name="receive-events"></a>Receber eventos

Esta secção mostra-lhe como criar uma aplicação JavaScript que recebe eventos de uma única divisão do grupo de consumidores padrão num centro de eventos. 

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um ficheiro chamado `receive.js` e colá-lo o código abaixo nele.
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
4. Em seguida, execute o comando `node receive.js` em um pedido de comando para executar este ficheiro. Isso receberá eventos de uma das partições do grupo de consumidores padrão em seu hub de eventos

Parabéns! Agora você recebeu eventos do hub de eventos.

## <a name="receive-events-using-event-processor-host"></a>Receber eventos com o Anfitrião de Processador de Eventos

Esta secção mostra como receber eventos a partir de um hub de eventos utilizando o Azure [EventProcessorHost](event-hubs-event-processor-host.md) numa aplicação JavaScript. O EventProcessorHost (EPH) ajuda-o com eficiência receber eventos de um hub de eventos através da criação de recetores todas as partições no grupo de consumidor de um hub de eventos. Ele metadados de pontos de verificação nas mensagens recebidas em intervalos regulares num Blob de armazenamento do Azure. Essa abordagem torna mais fácil continuar a receber mensagens a partir de onde parou num momento posterior.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um ficheiro chamado `receiveAll.js` e colá-lo o código abaixo nele.
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
4. Em seguida, execute o comando `node receiveAll.js` em um pedido de comando para executar este ficheiro.

Parabéns! Agora você recebeu eventos do hub de eventos usando o host do processador de eventos. Isso receberá eventos de todas as partições do grupo de consumidores padrão em seu hub de eventos

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)
- Confira outras amostras javaScript para [Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) de Eventos e Anfitrião de Processador de [Eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) no GitHub
