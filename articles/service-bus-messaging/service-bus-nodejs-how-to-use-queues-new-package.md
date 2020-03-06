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
ms.openlocfilehash: c2e24e9dea2c8463294c85f04c9e4d7d2da17261
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330656"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Como utilizar as filas de ônibus de serviço com node.js e o pacote azure/service-bus
Neste tutorial, aprende-se a escrever um programa Nodejs para enviar mensagens e receber mensagens de uma fila de ônibus de serviço utilizando o [pacote](https://www.npmjs.com/package/@azure/service-bus) de@azure/service-busnovo. Este pacote utiliza o [protocolo AMQP 1.0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote mais antigo [azure-sb](https://www.npmjs.com/package/azure-sb) utilizou APIs de [tempo de execução do Bus REST](/rest/api/servicebus/service-bus-runtime-rest)de serviço . As amostras estão escritas no JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se não tiver uma fila para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-portal.md) artigo de fila de ônibus de serviço para criar uma fila. Anote a cadeia de ligação para a sua instância de Ônibus de serviço e o nome da fila que criou. Usaremos estes valores nas amostras.

> [!NOTE]
> - Este tutorial funciona com amostras que pode copiar e executar usando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js para um Website Azure](../app-service/app-service-web-get-started-nodejs.md), ou serviço de nuvem [Node.js utilizando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) ainda não apoia a criação de filas. Utilize o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se quiser criá-los programáticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para o Service Bus, abra um pedido de comando que tenha `npm` no seu caminho, mude o diretório para a pasta onde pretende ter as suas amostras e, em seguida, executar este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Interagir com uma fila de ônibus de serviço começa com instantaneamente a classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e usá-lo para instantaneamente a classe [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Assim que tiver o cliente da fila, pode criar um remetente e utilizar o método [Enviar](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [enviar O Lote](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `send.js` e colá-lo o código abaixo nele. Este código enviará 10 mensagens para a sua fila.

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
4. Em seguida, execute o comando `node send.js` em um pedido de comando para executar este ficheiro.

Parabéns! Acabaste de enviar mensagens para uma fila de autocarros de serviço.

As mensagens têm algumas propriedades padrão como `label` e `messageId` que pode definir ao enviar. Se quiser definir quaisquer propriedades personalizadas, utilize o `userProperties`, que é um objeto json que pode conter pares de valor-chave dos seus dados personalizados.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens guardadas numa fila, mas há um limite no tamanho total das mensagens guardadas por uma fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre quotas, consulte [quotas de ônibus de serviço.](service-bus-quotas.md)

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Interagir com uma fila de ônibus de serviço começa com instantaneamente a classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e usá-lo para instantaneamente a classe [QueueClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) Assim que tiver o cliente da fila, pode criar um recetor e utilizar [mensagens](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou registar o [métodoMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `recieve.js` e colá-lo o código abaixo nele. Este código tentará receber 10 mensagens da sua fila. A contagem real que recebe depende do número de mensagens na fila e latência da rede.

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
4. Em seguida, execute o comando `node receiveMessages.js` em um pedido de comando para executar este ficheiro.

Parabéns! Acabaste de receber mensagens de uma fila de autocarros de serviço.

O método [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) leva uma `ReceiveMode` que é um enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [acertar as suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se utilizar o modo `PeekLock` utilizando qualquer um dos métodos `complete()`, `abandon()`, `defer()`ou `deadletter()` na mensagem.

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos seguintes
Para saber mais, consulte os seguintes recursos.
- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Check-out [outras amostras de Nodejs para ônibus de serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

