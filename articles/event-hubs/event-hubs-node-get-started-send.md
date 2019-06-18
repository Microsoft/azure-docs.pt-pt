---
title: Enviar e receber eventos com o node. js - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece um passo a passo para criar uma aplicação node. js para enviar eventos de Hubs de eventos do Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539333"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Enviar eventos para ou receber eventos dos Hubs de eventos do Azure com node. js

Os Hubs de eventos do Azure é um grandes volumes de dados de transmissão em fluxo plataforma e o serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicações node. js para enviar eventos para ou receber eventos de um hub de eventos.

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Versão node. js 8.x e superiores. Transferir a versão mais recente do LTS partir [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Criar um espaço de nomes de Hubs de eventos e um hub de eventos**. O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [este artigo](event-hubs-create.md), em seguida, prossiga com os seguintes passos neste tutorial. Em seguida, obter a cadeia de ligação para o espaço de nomes do hub de eventos ao seguir as instruções do artigo: [Obter cadeia de ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Irá utilizar a cadeia de ligação mais tarde neste tutorial.


### <a name="install-npm-package"></a>Instalar o pacote npm
Para instalar o [pacote npm dos Hubs de eventos](https://www.npmjs.com/package/@azure/event-hubs), abra uma linha de comandos que tenha `npm` no seu caminho, altere o diretório para a pasta onde pretende ter seus exemplos e, em seguida, execute este comando

```shell
npm install @azure/event-hubs
```

Para instalar o [pacote de npm para o anfitrião do processador de eventos](https://www.npmjs.com/package/@azure/event-processor-host), execute o comando abaixo em vez disso

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Enviar eventos

Esta secção mostra-lhe como criar uma aplicação node. js para enviar eventos para um hub de eventos. 

1. Abra o seu editor favorito, tal como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um ficheiro chamado `send.js` e cole o abaixo código nele.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
3. Introduza a cadeia de ligação e o nome do seu Hub de eventos no código anterior
4. Em seguida, execute o comando `node send.js` numa linha de comandos para executar este ficheiro. Isso enviará 100 eventos ao seu Hub de eventos

Parabéns! Enviou agora eventos para um hub de eventos.


## <a name="receive-events"></a>Receber eventos

Esta secção mostra-lhe como criar uma aplicação de node. js que recebe eventos de uma única partição do grupo de consumidores predefinido num hub de eventos. 

1. Abra o seu editor favorito, tal como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um ficheiro chamado `receive.js` e cole o abaixo código nele.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
3. Introduza a cadeia de ligação e o nome do seu Hub de eventos no código anterior.
4. Em seguida, execute o comando `node receive.js` numa linha de comandos para executar este ficheiro. Isto vai receber eventos a partir de uma das partições do grupo de consumidores predefinido, no seu Hub de eventos

Parabéns! Recebeu agora eventos do hub de eventos.

## <a name="receive-events-using-event-processor-host"></a>Receber eventos com o anfitrião do processador de eventos

Esta secção mostra como receber eventos de um hub de eventos ao utilizar o Azure [EventProcessorHost](event-hubs-event-processor-host.md) numa aplicação node. js. O EventProcessorHost (EPH) ajuda-o com eficiência receber eventos de um hub de eventos através da criação de recetores todas as partições no grupo de consumidor de um hub de eventos. Ele metadados de pontos de verificação nas mensagens recebidas em intervalos regulares num Blob de armazenamento do Azure. Essa abordagem torna mais fácil continuar a receber mensagens a partir de onde parou num momento posterior.

1. Abra o seu editor favorito, tal como [Visual Studio Code](https://code.visualstudio.com). 
2. Crie um ficheiro chamado `receiveAll.js` e cole o abaixo código nele.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Introduza a cadeia de ligação e o nome do seu Hub de eventos no código acima, juntamente com a cadeia de ligação para um armazenamento de Blobs do Azure
4. Em seguida, execute o comando `node receiveAll.js` numa linha de comandos para executar este ficheiro.

Parabéns! Recebeu agora eventos do hub de eventos com o anfitrião do processador de eventos. Isto vai receber eventos de todas as partições do grupo de consumidores predefinido no seu Hub de eventos

## <a name="next-steps"></a>Passos Seguintes
Leia os artigos seguintes:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e a terminologia nos Hubs de eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)
- Veja outros exemplos de node. js para [os Hubs de eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) e [anfitrião de processador de eventos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) no GitHub
