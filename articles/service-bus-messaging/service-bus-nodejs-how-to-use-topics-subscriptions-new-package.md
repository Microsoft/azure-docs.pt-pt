---
title: Use tópicos e subscrições azure/service-bus com Node.js
description: 'Quickstart: Aprenda a usar tópicos e subscrições do Service Bus em Azure a partir de uma aplicação Node.js.'
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 72120f6937c65ca6f7a94d34122e41ae3b4a4ef5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746436"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Como usar tópicos e subscrições do Service Bus com Node.js e o pacote azure/service-bus
Neste tutorial, aprende-se a escrever um programa Node.js para enviar mensagens para um tópico de Service Bus e receber mensagens de uma assinatura de Service Bus utilizando o novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote. Este pacote utiliza o [protocolo AMQP 1.0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [azure-sb](https://www.npmjs.com/package/azure-sb) mais antigo utilizou [APIs de tempo de funcionamento do Service Bus REST](/rest/api/servicebus/service-bus-runtime-rest). As amostras estão escritas em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Se não tiver um tópico e subscrição para trabalhar, siga os passos no [portal Use Azure para criar um](service-bus-quickstart-topics-subscriptions-portal.md) artigo de Serviço Bus e subscrições para criá-los. Note a cadeia de ligação para a sua instância de Service Bus e os nomes do tópico e subscrição que criou. Usaremos estes valores nas amostras.

> [!NOTE]
> - Este tutorial funciona com amostras que pode copiar e executar usando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js num Website Azure](../app-service/quickstart-nodejs.md), ouNode.js Cloud Service utilizando o Windows [ PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) pacote ainda não suporta a criação de tópicos e subscrições. Por favor, use o [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) pacote se quiser criá-los programáticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para Service Bus, abra um pedido de comando que tenha `npm` no seu caminho, altere o diretório para a pasta onde pretende ter as suas amostras e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Interagir com um tópico de Service Bus começa com instantaneamente a classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e usá-lo para instantaneaizar a classe [TopicClient.](/javascript/api/@azure/service-bus/topicclient) Uma vez que tenha o cliente tópico, pode criar um remetente e usar o método [enviar](/javascript/api/@azure/service-bus/sender#send-sendablemessageinfo-) ou enviar o método [Despachá-lo](/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `send.js` e cole o código abaixo nele. Este código enviará 10 mensagens para o seu tópico.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Introduza a cadeia de ligação e o nome do seu tópico no código acima.
4. Em seguida, execute o comando `node send.js` num pedido de comando para executar este ficheiro. 

Parabéns! Acabaste de enviar mensagens para uma fila de autocarros de serviço.

As mensagens têm algumas propriedades padrão como `label` e que pode definir ao `messageId` enviar. Se quiser definir quaisquer propriedades personalizadas, utilize o `userProperties` , que é um objeto json que pode conter pares de valores-chave dos seus dados personalizados.

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens mantidas num tópico, mas há um limite para o tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre quotas, consulte [as quotas de Service Bus.](service-bus-quotas.md)

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
Interagir com uma subscrição de Service Bus começa com instantaneamente a classe [ServiceBusClient](/javascript/api/@azure/service-bus/servicebusclient) e usá-la para instantaneaizar a classe [SubscriptionClient.](/javascript/api/@azure/service-bus/subscriptionclient) Uma vez que tenha o cliente de subscrição, pode criar um recetor e utilizar [ou receber Mensagens](/javascript/api/@azure/service-bus/receiver#receivemessages-number--undefined---number-) ou registar o método [MessageHandler](/javascript/api/@azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) nele para receber mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um ficheiro chamado `receive.js` e cole o código abaixo nele. Este código tentará receber 10 mensagens da sua subscrição. A contagem real que recebe depende do número de mensagens na subscrição e latência da rede.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Introduza a cadeia de ligação e os nomes do seu tópico e subscrição no código acima.
4. Em seguida, execute o comando `node receive.js` num pedido de comando para executar este ficheiro.

Parabéns! Acabou de receber mensagens de uma assinatura do Service Bus.

O método [createReceiver](/javascript/api/@azure/service-bus/subscriptionclient#createreceiver-receivemode-) acolhe um `ReceiveMode` enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [regularizar as suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se utilizar o `PeekLock` modo utilizando qualquer um dos , ou `complete()` `abandon()` `defer()` `deadletter()` métodos na mensagem.

## <a name="subscription-filters-and-actions"></a>Filtros e ações de assinatura
O Service Bus suporta [filtros e ações em subscrições,](topic-filters.md)o que permite filtrar as mensagens recebidas para uma subscrição e editar as suas propriedades.

Uma vez que tenha um exemplo de `SubscriptionClient` um, pode utilizar os métodos abaixo para obter, adicionar e remover as regras da subscrição para controlar os filtros e ações.

- obterrules
- addRule
- remover Barrar

Cada subscrição tem uma regra predefinida que usa o filtro verdadeiro para permitir todas as mensagens recebidas. Quando adicionar uma nova regra, lembre-se de remover o filtro predefinido para que o filtro da sua nova regra funcione. Se uma subscrição não tiver regras, então não receberá mensagens.

> [!NOTE]
> Você pode gerir os recursos de Service Bus com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nomes de Service Bus e administram as entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos Seguintes
Para saber mais, consulte os seguintes recursos.

- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Check-out [outras amostras de Nodejs para Service Bus em GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)
