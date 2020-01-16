---
title: Enviar e receber eventos usando node. js-hubs de eventos do Azure
description: Este artigo fornece um passo a passo para criar uma aplicação node. js para enviar eventos de Hubs de eventos do Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981618"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Enviar eventos para ou receber eventos de hubs de eventos do Azure usando o Node. js

Os hubs de eventos do Azure são uma plataforma de streaming de Big data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuídos. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análises em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicativos node. js para enviar eventos ou receber eventos de um hub de eventos.

> [!IMPORTANT]
> Este guia de início rápido usa a versão 5 do SDK de script Java dos hubs de eventos do Azure. Para um início rápido que usa a versão 2 antiga do SDK do script Java, consulte [Este artigo](event-hubs-node-get-started-send.md). Se você estiver usando a versão 2 do SDK, recomendamos que migre seu código para a versão mais recente. Para obter detalhes, consulte o [Guia de migração](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Versão node. js 8.x e superiores. Transferir a versão mais recente do LTS partir [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Crie um namespace de hubs de eventos e um hub de eventos**. O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento neste [artigo](event-hubs-create.md)e continue com as etapas a seguir neste tutorial. Em seguida, obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Irá utilizar a cadeia de ligação mais tarde neste tutorial.


### <a name="install-npm-packages"></a>Instalar pacotes do NPM
Para instalar o [pacote NPM para os hubs de eventos](https://www.npmjs.com/package/@azure/event-hubs), abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta onde você deseja ter seus exemplos e, em seguida, execute este comando

```shell
npm install @azure/event-hubs
```

Para o lado de recebimento, você precisa instalar mais dois pacotes. Neste guia de início rápido, você usará o armazenamento de BLOBs do Azure para persistir pontos de verificação para que o programa não leia os eventos já lidos. Ele verifica os metadados em mensagens recebidas em intervalos regulares em um blob. Essa abordagem torna mais fácil continuar a receber mensagens a partir de onde parou num momento posterior.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Enviar eventos

Esta seção mostra como criar um aplicativo node. js que envia eventos para um hub de eventos.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com).
2. Crie um arquivo chamado `send.js` e cole o código a seguir nele.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Não se esqueça de substituir a **cadeia de conexão** e os valores de nome do hub de **eventos** no código.
5. Execute o comando `node send.js` para executar esse arquivo. Isso enviará um lote de três eventos para o Hub de eventos
6. No portal do Azure, você pode verificar se o Hub de eventos recebeu as mensagens. Alterne para a exibição **mensagens** na seção **métricas** . Atualize a página para atualizar o gráfico. Pode levar alguns segundos para que ele mostre que as mensagens foram recebidas.

    [![verificar se o Hub de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código-fonte completo com comentários mais informativos, consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Parabéns! Agora você enviou eventos para um hub de eventos.


## <a name="receive-events"></a>Receber eventos
Esta seção mostra como receber eventos de um hub de eventos usando um repositório de pontos de verificação de blob do Azure em um aplicativo node. js. Ele metadados de pontos de verificação nas mensagens recebidas em intervalos regulares num Blob de armazenamento do Azure. Essa abordagem torna mais fácil continuar a receber mensagens a partir de onde parou num momento posterior.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento do Azure e um contêiner de BLOBs
Siga estas etapas para criar uma conta de armazenamento do Azure em um contêiner de BLOBs.

1. [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obter a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anote a cadeia de conexão e o nome do contêiner. Você os usará no código de recebimento.

### <a name="write-code-to-receive-events"></a>Escrever código para receber eventos

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com).
2. Crie um arquivo chamado `receive.js` e cole o código a seguir nele. Consulte os comentários de código para obter detalhes.
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. Não se esqueça de especificar os **seguintes valores** no código:
    - Cadeia de conexão para o namespace de hubs de eventos
    - Nome do hub de eventos
    - Cadeia de conexão para a conta de armazenamento do Azure
    - Nome do contêiner de BLOBs
5. Em seguida, execute o comando `node receive.js` em um prompt de comando para executar esse arquivo. Você deve ver as mensagens sobre eventos recebidos na janela.

    > [!NOTE]
    > Para obter o código-fonte completo com comentários mais informativos, consulte [este arquivo no GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Parabéns! Agora você recebeu eventos do hub de eventos. O programa receptor receberá eventos de todas as partições do grupo de consumidores padrão em seu hub de eventos

## <a name="next-steps"></a>Passos seguintes
Confira estes exemplos no GitHub:

- [Exemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Amostras do TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
