---
title: Como utilizar filas azure/service-bus em JavaScript
description: Saiba como escrever um programa JavaScript que utiliza a versão mais recente do @azure/service-bus pacote para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: ec5bb299bed5545c3935b2f0ae28a50de9d79c45
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808882"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Enviar mensagens para e receber mensagens das filas do Azure Service Bus (JavaScript)
Neste tutorial, aprende a usar o [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote num programa JavaScript para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila. Note a **cadeia de ligação** para o seu espaço de nomes de Service Bus e o nome da **fila** que criou.

> [!NOTE]
> - Este tutorial funciona com amostras que pode copiar e executar usando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js num Website Azure](../app-service/quickstart-nodejs.md), ouNode.js serviço de nuvem utilizando o Windows [ PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para Service Bus, abra um pedido de comando que tenha `npm` no seu caminho, altere o diretório para a pasta onde pretende ter as suas amostras e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
O seguinte código de amostra mostra como enviar uma mensagem para uma fila.

1. Abra o seu editor favorito, como [Visual Studio Code.](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `send.js` e cole o código abaixo nele. Este código enviará uma mensagem para a sua fila. A mensagem tem um rótulo (Cientista) e corpo (Einstein).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>`Substitua-a pelo fio de ligação ao seu espaço de nomes de Service Bus.
1. `<QUEUE NAME>`Substitua-o pelo nome da fila. 
1. Em seguida, execute o comando num pedido de comando para executar este ficheiro.

    ```console
    node send.js 
    ```
1. Deverá ver o seguinte resultado.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `receive.js` e cole-o o seguinte código.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>`Substitua-a pelo fio de ligação ao seu espaço de nomes de Service Bus.
1. `<QUEUE NAME>`Substitua-o pelo nome da fila. 
1. Em seguida, execute o comando num pedido de comando para executar este ficheiro.

    ```console
    node receive.js
    ```
1. Deverá ver o seguinte resultado.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Na página **'Visão Geral'** do espaço de nomes do Service Bus no portal Azure, pode ver a contagem de mensagens **recebidas** e **de saída.** Pode ter de esperar um minuto ou assim e, em seguida, refrescar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída":::

Selecione a fila nesta página **de visão geral** para navegar na página **de Fila de autocarros** de serviço. Vê a **mensagem de entrada** e **saída** contar também nesta página. Também vê outras informações, como o **tamanho atual** da fila, **o tamanho máximo,** **a contagem de mensagens ativas,** e assim por diante. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila":::
## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras: 

- [Biblioteca de clientes de autocarros de serviço Azure para Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Amostras.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples) A pasta **javascript** tem amostras javaScript e a **máquina de escrever** tem amostras TypeScript. 
- [documentação de referência azure-servicebus](https://docs.microsoft.com/javascript/api/overview/azure/service-bus)
