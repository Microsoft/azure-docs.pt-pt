---
title: Envie ou receba eventos de Azure Event Hubs usando JavaScript (mais recente)
description: Este artigo fornece um walkthrough para criar uma aplicação JavaScript que envia/recebe eventos de/para Azure Event Hubs usando o mais recente pacote azure/event-hubs versão 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 71c50e8efdf26f2a7d3f270a774b08e49c92faa7
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159425"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Envie eventos ou receba eventos a partir de centros de eventos utilizando javaScript (versão azure/event-hubs 5)
Este quickstart mostra como enviar eventos e receber eventos de um hub de eventos usando o pacote 5 JavaScript **azure/event-hubs.** 

> [!IMPORTANT]
> Este quickstart utiliza o mais recente pacote da versão 5 azure/event-hubs. Para um início rápido que utilize o antigo pacote azure/event-hubs versão 2, consulte Enviar e receber eventos utilizando a versão 2 do [azure/event-hubs](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- Node.js versão 8.x ou mais tarde. Descarregue a versão mais recente [de suporte a longo prazo (LTS).](https://nodejs.org)  
- Código de Estúdio Visual (recomendado) ou qualquer outro ambiente de desenvolvimento integrado (IDE).  
- Um espaço de nome ative Event Hubs e centro de eventos. Para criá-los, faça os seguintes passos: 

   1. No [portal Azure, crie](https://portal.azure.com)um espaço de nome de hubs de *eventos*tipo, e depois obtenha as credenciais de gestão que a sua aplicação precisa para comunicar com o hub do evento. 
   1. Para criar o espaço de nome e o centro de eventos, siga as instruções na [Quickstart: Crie um hub de eventos utilizando o portal Azure](event-hubs-create.md).
   1. Continue seguindo as instruções neste arranque rápido. 
   1. Para obter a cadeia de ligação para o seu espaço de nome do Event Hub, siga as instruções na [cadeia de ligação Get](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Grave a cadeia de ligação para usar mais tarde neste arranque rápido.
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md) Em seguida, obtenha a cadeia de **ligação para o espaço** de nome do Event Hubs seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Usa a corda de ligação mais tarde neste arranque rápido.

### <a name="install-the-npm-package"></a>Instale o pacote npm
Para instalar o pacote De [node Package Manager (npm) para Centros de Eventos,](https://www.npmjs.com/package/@azure/event-hubs)abra um pedido de comando que tenha *npm* no seu caminho, mude o diretório para a pasta onde pretende guardar as suas amostras e, em seguida, executar este comando:

```shell
npm install @azure/event-hubs
```

Para o lado recetor, é necessário instalar mais dois pacotes. Neste arranque rápido, você usa o armazenamento Azure Blob para persistir pontos de verificação para que o programa não leia os eventos que já leu. Executa postos de controlo de metadados em mensagens recebidas em intervalos regulares numa bolha. Esta abordagem facilita a continuação de receber mensagens mais tarde de onde deixou de lado.

Execute os seguintes comandos:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Enviar eventos

Nesta secção, cria-se uma aplicação JavaScript que envia eventos para um centro de eventos.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com).
1. Crie um ficheiro chamado *send.js*e colhe o seguinte código nele:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. No código, utilize valores reais para substituir o seguinte:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Corra `node send.js` para executar este ficheiro. Este comando envia um lote de três eventos para o seu centro de eventos.
1. No portal Azure, verifique se o centro do evento recebeu as mensagens. Na secção **Métricas,** mude para a vista **Mensagens.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para mostrar que as mensagens foram recebidas.

    [![Verifique se o centro do evento recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informísticos adicionais, aceda à [página GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Parabéns! Agora enviaste eventos para um centro de eventos.


## <a name="receive-events"></a>Receber eventos
Nesta secção, você recebe eventos de um hub de eventos usando uma loja de controlo de armazenamento Azure Blob numa aplicação JavaScript. Executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares numa bolha de Armazenamento Azure. Esta abordagem facilita a continuação de receber mensagens mais tarde de onde deixou de lado.

> [!NOTE]
> Se estiver a funcionar no Azure Stack Hub, essa plataforma poderá suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Por exemplo, se estiver a correr [na versão Azure Stack Hub 2002,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a versão mais alta disponível para o serviço de Armazenamento é a versão 2017-11-09. Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento a versão API 2017-11-09. Para um exemplo sobre como direcionar uma versão API de Armazenamento específica, consulte amostras [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) no GitHub. Para obter mais informações sobre as versões de serviço de armazenamento Azure suportadas no Azure Stack Hub, consulte o [armazenamento Do Hub De Pilha Azure: Diferenças e considerações](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Crie uma conta de armazenamento Azure e um recipiente de bolhas
Para criar uma conta de armazenamento Azure e um recipiente de bolhas, faça as seguintes ações:

1. [Criar uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Criar um contentor de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Obtenha a cadeia de ligação à conta de armazenamento](../storage/common/storage-configure-connection-string.md)

Certifique-se de que regista o nome da cadeia de ligação e do recipiente para posterior utilização no código de receção.

### <a name="write-code-to-receive-events"></a>Escreva código para receber eventos

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com).
1. Crie um ficheiro chamado *receive.js*e colhe o seguinte código nele:

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. No código, utilize valores reais para substituir os seguintes valores:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Execute `node receive.js` um pedido de comando para executar este ficheiro. A janela deve exibir mensagens sobre eventos recebidos.

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informísticos adicionais, aceda à [página DeEventosUseCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Parabéns! Recebeu eventos do seu centro de eventos. O programa recetor receberá eventos de todas as divisórias do grupo de consumidores padrão no centro do evento.

## <a name="next-steps"></a>Passos seguintes
Confira estas amostras no GitHub:

- [Exemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Amostras de TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
