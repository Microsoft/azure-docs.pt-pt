---
title: 'Início rápido: como usar as filas do Azure/Service-Bus no node. js'
description: 'Início rápido: saiba como usar as filas do barramento de serviço no Azure de um aplicativo node. js usando o pacote do Azure/Service-Bus.'
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
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 9901ccd6bb1abf27bb1141c618d0bfde167b9cc3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721679"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Início rápido: como usar as filas do barramento de serviço com o Node. js e o pacote do Azure/Service-Bus
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Neste tutorial, você aprende a escrever um programa NodeJS para enviar e receber mensagens de uma fila do barramento de serviço usando o novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Esse pacote usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) mais antigo usava [APIs de tempo de execução REST do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Os exemplos são escritos em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a cadeia de conexão para sua instância do barramento de serviço e o nome da fila que você criou. Usaremos esses valores nos exemplos.

> [!NOTE]
> - Este tutorial funciona com exemplos que você pode copiar e executar usando o [NodeJS](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo node. js, consulte [criar e implantar um aplicativo node. js em um site do Azure](../app-service/app-service-web-get-started-nodejs.md)ou [serviço de nuvem do node. js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) não dá suporte à criação de filas ainda. Use o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se desejar criá-los programaticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote NPM para o barramento de serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta onde você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Interagir com uma fila do barramento de serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Depois de ter o cliente de fila, você pode criar um remetente e usar o método [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) nele para enviar mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `send.js` e cole o código abaixo nele. Esse código enviará 10 mensagens para sua fila.

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
3. Insira a cadeia de conexão e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de enviar mensagens para uma fila do barramento de serviço.

As mensagens têm algumas propriedades padrão como `label` e `messageId` que você pode definir ao enviar. Se você quiser definir qualquer propriedade personalizada, use o `userProperties`, que é um objeto JSON que pode conter pares de chave-valor de seus dados personalizados.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens mantidas em uma fila, mas há uma limitação no tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Interagir com uma fila do barramento de serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Depois de ter o cliente de fila, você pode criar um receptor e usar o método [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `recieve.js` e cole o código abaixo nele. Esse código tentará receber 10 mensagens da fila. A contagem real que você recebe depende do número de mensagens na fila e latência de rede.

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
3. Insira a cadeia de conexão e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de receber mensagens de uma fila do barramento de serviço.

O método [Createreceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) usa um `ReceiveMode` que é um enum com os valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [Peeklock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se usar o modo de `PeekLock` usando qualquer um dos métodos `complete()`, `abandon()`, `defer()`ou `deadletter()` na mensagem.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais, confira os recursos a seguir.
- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Fazer check-out [de outros exemplos de NodeJS para o barramento de serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

