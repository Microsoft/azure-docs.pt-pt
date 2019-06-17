---
title: Como utilizar as filas do Service bus do Azure em node. js - / service-bus do azure | Documentos da Microsoft
description: Saiba como utilizar filas do Service Bus no Azure a partir de uma aplicação node. js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988355"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Como utilizar filas do Service Bus com o node. js e o pacote/service-bus do azure
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote de node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Neste tutorial, irá aprender a escrever um programa de Nodejs para enviar mensagens e receber mensagens de uma fila do Service Bus com o novo [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pacote. Esse pacote usa o mais rápido [protocolo AMQP 1.0](service-bus-amqp-overview.md) ao passo que o antigo [azure sb](https://www.npmjs.com/package/azure-sb) pacote utilizado [APIs de tempo de execução de REST do Service Bus](/rest/api/servicebus/service-bus-runtime-rest). Os exemplos são escritos em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar sua [benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se não tiver uma fila para trabalhar com, siga os passos no [portal do Azure de utilização para criar uma fila do Service Bus](service-bus-quickstart-portal.md) artigo para criar uma fila. Aponte a cadeia de ligação para a instância do Service Bus e o nome da fila que criou. Iremos utilizar estes valores nos exemplos.

> [!NOTE]
> - Este tutorial funciona com exemplos que pode copiar e executados utilizando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação node. js, consulte [criar e implementar uma aplicação node. js para um Web site do Azure](../app-service/app-service-web-get-started-nodejs.md), ou [serviço cloud de node. js com o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - A nova [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) pacote não suporta a criação de filas ainda. Utilize o [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) se quiser criar programaticamente o pacote.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para o Service Bus, abra um prompt de comando que tem `npm` no seu caminho, altere o diretório para a pasta onde pretende ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Interagir com um barramento de serviço de fila é iniciado com instanciar o [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe e seu uso para instanciar o [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) classe. Assim que tiver o cliente de fila, pode criar um remetente e a utilização de uma [enviar](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) método nele para enviar mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `send.js` e cole o abaixo código nele. Esse código irá enviar 10 mensagens para a fila.

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
3. Introduza a cadeia de ligação e o nome da sua fila no código anterior.
4. Em seguida, execute o comando `node send.js` numa linha de comandos para executar este ficheiro.

Parabéns! Que acabou de enviar mensagens a uma fila do Service Bus.

As mensagens de algumas propriedades padrão como ter `label` e `messageId` que pode definir quando enviar. Se quiser definir quaisquer propriedades personalizadas, utilize o `userProperties`, que é um objeto json que pode armazenar pares chave-valor dos seus dados personalizados.

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não tem qualquer limite no número de mensagens contidas numa fila, mas existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre as quotas, consulte [quotas do Service Bus](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Interagir com um barramento de serviço de fila é iniciado com instanciar o [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) classe e seu uso para instanciar o [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) classe. Assim que tiver o cliente de fila, pode criar um receptor e a utilização de uma [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) método no mesmo para receber mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `recieve.js` e cole o abaixo código nele. Esse código tentará-se receber 10 mensagens da sua fila. A contagem real que receberá depende o número de mensagens na fila e de rede de latência.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
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
3. Introduza a cadeia de ligação e o nome da sua fila no código anterior.
4. Em seguida, execute o comando `node receiveMessages.js` numa linha de comandos para executar este ficheiro.

Parabéns! Acabou de receber mensagens de uma fila do Service Bus.

O [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) método usa um `ReceiveMode` que é uma enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se utilizar o `PeekLock` modo utilizando qualquer um dos `complete()`, `abandon()`, `defer()`, ou `deadletter()` métodos na mensagem.

> [!NOTE]
> Pode gerir recursos do Service Bus com [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). O Explorador do Service Bus permite aos utilizadores ligar a um espaço de nomes do Service Bus e administrar as entidades de mensagens de uma forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de teste tópico, filas, subscrições, serviços de reencaminhamento, os hubs de notificação e os hubs de eventos. 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte os seguintes recursos.
- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Veja outros [Nodejs exemplos do Service Bus no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

