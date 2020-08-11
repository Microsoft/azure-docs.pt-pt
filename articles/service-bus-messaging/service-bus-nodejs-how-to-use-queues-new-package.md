---
title: Como utilizar filas de azure/serviço-autocarro em Node.js
description: Saiba como escrever um programa Nodejs para enviar mensagens e receber mensagens de uma fila de autocarros de serviço usando o novo @azure/service-bus pacote.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: c082eff4f70b0255b7701340ac6e8b377223c0b5
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88076868"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Como utilizar as filas de Service Bus com Node.js e o pacote azure/service-bus
Neste tutorial, aprende-se a escrever um programa Nodejs para enviar mensagens e receber mensagens de uma fila de Autocarros de Serviço utilizando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote. Este pacote utiliza o [protocolo AMQP 1.0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [azure-sb](https://www.npmjs.com/package/azure-sb) mais antigo utilizou [APIs de tempo de funcionamento do Service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). As amostras estão escritas em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de Service Bus para criar uma fila. Note a cadeia de ligação para a sua instância de Service Bus e o nome da fila que criou. Usaremos estes valores nas amostras.

> [!NOTE]
> - Este tutorial funciona com amostras que pode copiar e executar usando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js num Website Azure](../app-service/quickstart-nodejs.md), ouNode.js serviço de nuvem utilizando o Windows [PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote ainda não apoia a criação de filas. Por favor, use o [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) pacote se quiser criá-los programáticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para Service Bus, abra um pedido de comando que tenha `npm` no seu caminho, altere o diretório para a pasta onde pretende ter as suas amostras e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Interagir com uma fila de Autocarros de Serviço começa com instantaneamente a classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e usá-la para instantaneaizar a classe [QueueClient.](/javascript/api/@azure/service-bus/queueclient) Uma vez que tenha o cliente da fila, pode criar um remetente e usar o método [enviar](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) ou enviar o método [Despachá-lo](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `send.js` e cole o código abaixo nele. Este código enviará 10 mensagens para a sua fila.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Introduza a cadeia de ligação e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node send.js` num pedido de comando para executar este ficheiro.

Parabéns! Acabaste de enviar mensagens para uma fila de autocarros de serviço.

As mensagens têm algumas propriedades padrão como `label` e que pode definir ao `messageId` enviar. Se quiser definir quaisquer propriedades personalizadas, utilize o `userProperties` , que é um objeto json que pode conter pares de valores-chave dos seus dados personalizados.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens guardadas numa fila, mas há um limite para o tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre quotas, consulte [as quotas de Service Bus.](service-bus-quotas.md)

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Interagir com uma fila de Autocarros de Serviço começa com instantaneamente a classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e usá-la para instantaneaizar a classe [QueueClient.](/javascript/api/@azure/service-bus/queueclient) Uma vez que tenha o cliente da fila, pode criar um recetor e utilizar [ou receber Mensagens](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) ou registar o método [MessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) nele para receber mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `recieve.js` e cole o código abaixo nele. Este código tentará receber 10 mensagens da sua fila. A contagem real que recebe depende do número de mensagens na fila e latência da rede.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Introduza a cadeia de ligação e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` num pedido de comando para executar este ficheiro.

Parabéns! Acabou de receber mensagens de uma fila de autocarros de serviço.

O método [createReceiver](/javascript/api/@azure/service-bus/queueclient#createreceiver-receivemode-) acolhe um `ReceiveMode` enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [regularizar as suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se utilizar o `PeekLock` modo utilizando qualquer um dos , ou `complete()` `abandon()` `defer()` `deadletter()` métodos na mensagem.

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais, consulte os seguintes recursos.
- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Check-out [outras amostras de Nodejs para Service Bus em GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)