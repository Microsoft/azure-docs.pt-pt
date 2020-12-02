---
title: Utilize pré-visualização JavaScript azure/service-bus com tópicos e subscrições
description: Saiba como escrever um programa JavaScript que utiliza a versão mais recente do @azure/service-bus pacote para enviar mensagens para um tópico de Service Bus e receber mensagens de uma subscrição do tópico.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: aac3b6339c318c76e9b0c9abd0bc3778f2563a6f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498698"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Quickstart: Tópicos e subscrições de Service Bus com Node.js e o pacote de pré-visualização azure/service-bus
Neste tutorial, aprende a usar o [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote num programa JavaScript para enviar mensagens para um tópico de Service Bus e receber mensagens de uma subscrição de Service Bus para esse tópico.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Siga os passos no [Quickstart: Use o portal Azure para criar um tópico de Service Bus e subscrições do tema](service-bus-quickstart-topics-subscriptions-portal.md). Note a cadeia de ligação, o nome do tópico e um nome de subscrição. Utilizará apenas uma subscrição para este arranque rápido. 

> [!NOTE]
> - Este tutorial funciona com amostras que pode copiar e executar usando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js num Website Azure](../app-service/quickstart-nodejs.md), ouNode.js Cloud Service utilizando o Windows [ PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para Service Bus, abra um pedido de comando que tenha `npm` no seu caminho, altere o diretório para a pasta onde pretende ter as suas amostras e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
O seguinte código de amostra mostra-lhe como enviar um lote de mensagens para um tópico de Service Bus. Consulte os comentários de código para mais detalhes. 

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `sendtotopic.js` e cole o código abaixo nele. Este código enviará uma mensagem para o seu tópico.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>`Substitua-a pelo fio de ligação ao seu espaço de nomes de Service Bus.
1. `<TOPIC NAME>`Substitua-o pelo nome do tópico. 
1. Em seguida, execute o comando num pedido de comando para executar este ficheiro.

    ```console
    node sendtotopic.js 
    ```
1. Deverá ver o seguinte resultado.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado **receivefromsubscription.js** e cole-o o seguinte código. Consulte os comentários de código para mais detalhes. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>`Substitua-a pelo fio de ligação ao espaço de nomes. 
1. `<TOPIC NAME>`Substitua-o pelo nome do tópico. 
1. `<SUBSCRIPTION NAME>`Substitua-o pelo nome da subscrição do tópico. 
1. Em seguida, execute o comando num pedido de comando para executar este ficheiro.

    ```console
    node receivefromsubscription.js
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

No portal Azure, navegue no seu espaço de nomes de Service Bus e selecione o tópico no painel inferior para ver a página **De Tópico de Autocarro** de Serviço para o seu tópico. Nesta página, deverá ver três mensagens recebidas e três de saída na tabela **Mensagens.** 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Mensagens de entrada e saída":::

Se executar a única aplicação de envio da próxima vez, na página **De Tópico de Autocarro de Serviço,** vê seis mensagens recebidas (3 novas) mas três mensagens de saída. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Página de tópico atualizada":::

Nesta página, se selecionar uma subscrição, você chega à página **de Subscrição de Serviço Bus.** Pode ver a contagem de mensagens ativas, a contagem de mensagens mortas e muito mais nesta página. Neste exemplo, existem três mensagens ativas que ainda não foram recebidas por um recetor. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Contagem de mensagens ativas":::

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte documentação e amostras: 

- [Biblioteca de clientes de autocarros de serviço Azure para Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Amostras.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples) A pasta **javascript** tem amostras javaScript e a **máquina de escrever** tem amostras TypeScript. 
- [documentação de referência azure-servicebus](/javascript/api/overview/azure/service-bus)