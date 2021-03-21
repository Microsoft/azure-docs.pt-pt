---
title: Enviar ou receber eventos de Azure Event Hubs usando JavaScript (mais recente)
description: Este artigo fornece uma passagem para a criação de uma aplicação JavaScript que envia/recebe eventos de/para Azure Event Hubs usando o mais recente pacote azure/event-hubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: c418c13346fb1ec8ba16965fa1020c676ddf3ac6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602558"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>Enviar eventos para ou receber eventos de centros de eventos utilizando o JavaScript (azure/event-hubs)
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando o pacote **azure/event-hubs** JavaScript. 


## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- Node.js versão 8.x ou posterior. Descarregue a mais recente [versão de suporte a longo prazo (LTS).](https://nodejs.org)  
- Código de Estúdio Visual (recomendado) ou qualquer outro ambiente de desenvolvimento integrado (IDE).  
- Um espaço de nomes e centro de eventos do Event Hubs ativo. Para criá-los, faça os seguintes passos: 

   1. No [portal Azure](https://portal.azure.com), crie um espaço de nome de tipo Event Hubs , e, em seguida, obtenha as credenciais de gestão de que a sua aplicação necessita para comunicar com o centro de *eventos.* 
   1. Para criar o espaço de nome e o centro de eventos, siga as instruções no [Quickstart: Crie um centro de eventos utilizando o portal Azure](event-hubs-create.md).
   1. Continue seguindo as instruções neste arranque rápido. 
   1. Para obter a cadeia de ligação para o seu espaço de nomes Event Hub, siga as instruções na [cadeia de ligação Get](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Grave o fio de ligação a utilizar mais tarde neste arranque rápido.
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de ligação para o espaço de nomes Do Event Hubs** seguindo as instruções do artigo: Obter a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a cadeia de ligação mais tarde neste arranque rápido.

### <a name="install-the-npm-package"></a>Instale o pacote npm
Para instalar o [pacote Node Package Manager (npm) para Os Centros de Eventos,](https://www.npmjs.com/package/@azure/event-hubs)abra um pedido de comando que tenha *npm* no seu caminho, altere o diretório para a pasta onde pretende guardar as suas amostras e, em seguida, execute este comando:

```shell
npm install @azure/event-hubs
```

Para o lado recetor, é necessário instalar mais dois pacotes. Neste arranque rápido, você usa o armazenamento Azure Blob para persistir pontos de verificação para que o programa não leia os eventos que já leu. Executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares numa bolha. Esta abordagem facilita a continuação de mensagens mais tarde de onde paraste.

Execute os seguintes comandos:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Enviar eventos

Nesta secção, cria-se uma aplicação JavaScript que envia eventos para um centro de eventos.

1. Abra o seu editor favorito, como [Visual Studio Code.](https://code.visualstudio.com)
1. Crie um ficheiro chamado *send.js*, e cole-o o seguinte código:

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
1. No código, utilize valores reais para substituir os seguintes valores:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Corra `node send.js` para executar este ficheiro. Este comando envia um lote de três eventos para o seu centro de eventos.
1. No portal Azure, verifique se o centro de eventos recebeu as mensagens. Na secção **Métricas,** mude para a visualização **de Mensagens.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para mostrar que as mensagens foram recebidas.

    [![Verifique se o centro de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informativos adicionais, aceda à [página sendEvents.js GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Parabéns! Agora enviaste eventos para um centro de eventos.


## <a name="receive-events"></a>Receber eventos
Nesta secção, você recebe eventos de um centro de eventos usando uma loja de ponto de verificação de armazenamento Azure Blob numa aplicação JavaScript. Executa pontos de verificação de metadados em mensagens recebidas em intervalos regulares numa bolha de armazenamento Azure. Esta abordagem facilita a continuação de mensagens mais tarde de onde paraste.

> [!WARNING]
> Se executar este código no Azure Stack Hub, sofrerá erros de tempo de execução a menos que tenha como alvo uma versão API de armazenamento específica. Isto porque o Event Hubs SDK utiliza a mais recente API de Armazenamento Azure disponível disponível no Azure que pode não estar disponível na sua plataforma Azure Stack Hub. O Azure Stack Hub pode suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Se estiver a utilizar o Azure Blog Storage como uma loja de checkpoint, verifique a [versão API suportada do Azure Storage para a sua construção do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e direcione essa versão no seu código. 
>
> Por exemplo, Se estiver a executar a versão Azure Stack Hub 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes Event Hubs SDK utiliza a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento API versão 2019-02-02. Para um exemplo sobre como direcionar uma versão API de armazenamento específica, consulte amostras [de JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) no GitHub. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Criar uma conta de armazenamento Azure e um recipiente blob
Para criar uma conta de armazenamento Azure e um recipiente de bolhas nele, faça as seguintes ações:

1. [Criar uma conta de armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Criar um contentor de blobs na conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Obtenha o fio de ligação para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

Certifique-se de que regista o fio de ligação e o nome do recipiente para posterior utilização no código de receção.

### <a name="write-code-to-receive-events"></a>Escrever código para receber eventos

1. Abra o seu editor favorito, como [Visual Studio Code.](https://code.visualstudio.com)
1. Crie um ficheiro chamado *receive.js*, e cole-o o seguinte código:

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
            if (events.length === 0) {
              console.log(`No events received within wait time. Waiting for next interval`);
              return;
            }
          
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
1. Execute `node receive.js` num pedido de comando para executar este ficheiro. A janela deve apresentar mensagens sobre eventos recebidos.

    > [!NOTE]
    > Para obter o código fonte completo, incluindo comentários informativos adicionais, aceda à [página receiveEventsUsingCheckpointStore.js GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Parabéns! Já recebeu eventos do seu centro de eventos. O programa recetor receberá eventos de todas as divisórias do grupo de consumidores predefinido no centro de eventos.

## <a name="next-steps"></a>Passos seguintes
Confira estas amostras no GitHub:

- [Exemplos de JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Amostras de TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
