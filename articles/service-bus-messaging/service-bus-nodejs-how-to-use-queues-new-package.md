---
title: Como utilizar filas azure/service-bus em Node.js
description: Saiba escrever um programa Nodejs para enviar mensagens e receber mensagens de uma fila de ônibus de serviço usando o pacote de @azure/service-bus novo.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 9db359d8331859e3eebf345d91fe5dc6e66c0d4c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774580"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Como utilizar as filas de ônibus de serviço com node.js e o pacote azure/service-bus
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Neste tutorial, aprende-se a escrever um programa Nodejs para enviar mensagens e receber mensagens de uma fila de ônibus de serviço utilizando o [pacote](https://www.npmjs.com/package/@azure/service-bus) de@azure/service-busnovo. Esse pacote usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) mais antigo usava [APIs de tempo de execução REST do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Os exemplos são escritos em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a cadeia de ligação para a sua instância de Ônibus de serviço e o nome da fila que criou. Usaremos esses valores nos exemplos.

> [!NOTE]
> - Este tutorial funciona com exemplos que você pode copiar e executar usando o [NodeJS](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js para um Website Azure](../app-service/app-service-web-get-started-nodejs.md), ou serviço de nuvem [Node.js utilizando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) ainda não apoia a criação de filas. Use o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se desejar criá-los programaticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote NPM para o barramento de serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta onde você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Interagir com uma fila de ônibus de serviço começa com instantaneamente a classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e usá-lo para instantaneamente a classe [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Assim que tiver o cliente da fila, pode criar um remetente e utilizar o método [Enviar](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [enviar O Lote](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `send.js` e cole o código abaixo nele. Este código enviará 10 mensagens para a sua fila.

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
3. Introduza a corda de ligação e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de enviar mensagens para uma fila do barramento de serviço.

As mensagens têm algumas propriedades padrão como `label` e `messageId` que você pode definir ao enviar. Se você quiser definir qualquer propriedade personalizada, use o `userProperties`, que é um objeto JSON que pode conter pares de chave-valor de seus dados personalizados.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens mantidas em uma fila, mas há uma limitação no tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Interagir com uma fila de ônibus de serviço começa com instantaneamente a classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e usá-lo para instantaneamente a classe [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Assim que tiver o cliente da fila, pode criar um recetor e utilizar [mensagens](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou registar o [métodoMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `recieve.js` e cole o código abaixo nele. Este código tentará receber 10 mensagens da sua fila. A contagem real que recebe depende do número de mensagens na fila e latência da rede.

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
3. Introduza a corda de ligação e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` em um prompt de comando para executar esse arquivo.

Parabéns! Acabaste de receber mensagens de uma fila de autocarros de serviço.

O método [Createreceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) usa um `ReceiveMode` que é um enum com os valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [Peeklock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se usar o modo de `PeekLock` usando qualquer um dos métodos `complete()`, `abandon()`, `defer()`ou `deadletter()` na mensagem.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais, confira os recursos a seguir.
- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Fazer check-out [de outros exemplos de NodeJS para o barramento de serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

