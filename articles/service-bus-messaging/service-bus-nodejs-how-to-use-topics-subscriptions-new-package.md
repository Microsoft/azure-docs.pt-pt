---
title: Utilize tópicos e subscrições azure/service-bus com Node.js
description: 'Quickstart: Aprenda a usar tópicos e subscrições de ônibus de serviço em Azure a partir de uma aplicação Node.js.'
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 6088b4c54ed16c5ef46d2c0671e619884cad29d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330622"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Quickstart: Como utilizar tópicos e subscrições de ônibus de serviço com Node.js e o pacote azure/service-bus
Neste tutorial, aprende-se a escrever um programa Node.js para enviar mensagens para um tópico [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) de Ônibus de serviço e receber mensagens de uma subscrição de Ônibus de serviço utilizando o novo pacote. Este pacote utiliza o [protocolo AMQP 1.0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote mais antigo [azure-sb](https://www.npmjs.com/package/azure-sb) utilizou APIs de [tempo de execução do Bus REST](/rest/api/servicebus/service-bus-runtime-rest)de serviço . As amostras estão escritas no JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Pode ativar os benefícios do [seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se não tiver um tópico e subscrição para trabalhar, siga os passos no [portal Use Azure para criar um artigo](service-bus-quickstart-topics-subscriptions-portal.md) de ônibus de serviço e artigo de subscrições para criá-los. Anote a cadeia de ligação para a sua instância de Ônibus de serviço e os nomes do tópico e subscrição que criou. Usaremos estes valores nas amostras.

> [!NOTE]
> - Este tutorial funciona com amostras que pode copiar e executar usando [Nodejs](https://nodejs.org/). Para obter instruções sobre como criar uma aplicação Node.js, consulte [Criar e implementar uma aplicação Node.js para um Website Azure](../app-service/app-service-web-get-started-nodejs.md), ou [Node.js Cloud Service utilizando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) novo pacote ainda não suporta a criação de topcis e subscrições. Por favor, use o [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) pacote se quiser criá-los programáticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote npm para o Service `npm` Bus, abra um pedido de comando que tenha no seu caminho, mude o diretório para a pasta onde pretende ter as suas amostras e, em seguida, executar este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Interagir com um tópico de ônibus de serviço começa com instantaneamente a classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e usá-lo para instantaneamente a classe [TopicClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) Assim que tiver o cliente tópico, pode criar um remetente e utilizar o método [Enviar](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [enviar O Lote](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) para enviar mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um `send.js` ficheiro chamado e colá-lo o código abaixo nele. Este código enviará 10 mensagens para o seu tópico.

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
4. Em seguida, `node send.js` execute o comando num pedido de comando para executar este ficheiro. 

Parabéns! Acabaste de enviar mensagens para uma fila de autocarros de serviço.

As mensagens têm `label` `messageId` algumas propriedades padrão como e que pode definir ao enviar. Se quiser definir quaisquer propriedades `userProperties`personalizadas, use o , que é um objeto json que pode conter pares de valor chave dos seus dados personalizados.

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens guardadas num tópico, mas há um limite para o tamanho total das mensagens guardadas por um tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre quotas, consulte [quotas de ônibus de serviço.](service-bus-quotas.md)

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição
Interagir com uma subscrição de Service Bus começa por instantaneamente a classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e usá-la para instantaneamente a classe [SubscriptionClient.](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) Assim que tiver o cliente de subscrição, pode criar um recetor e utilizar [mensagens](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou registar o [métodoMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra o seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um `recieve.js` ficheiro chamado e colá-lo o código abaixo nele. Este código tentará receber 10 mensagens da sua subscrição. A contagem real que recebe depende do número de mensagens na subscrição e latência da rede.

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
4. Em seguida, `node receiveMessages.js` execute o comando num pedido de comando para executar este ficheiro.

Parabéns! Acabaste de receber mensagens de uma subscrição do Service Bus.

O método [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) `ReceiveMode` leva num enum com valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [acertar as suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se utilizar o `PeekLock` modo utilizando qualquer um `complete()`dos métodos `abandon()` `defer()`ou `deadletter()` métodos na mensagem.

## <a name="subscription-filters-and-actions"></a>Filtros e ações de subscrição
O Service Bus suporta [filtros e ações em subscrições,](topic-filters.md)o que lhe permite filtrar as mensagens de entrada para uma subscrição e editar as suas propriedades.

Uma vez que tenha `SubscriptionClient` uma instância de um, pode usar os métodos abaixo para obter, adicionar e remover regras sobre a subscrição para controlar os filtros e ações.

- obterRegras
- adicionarRule
- removerRule

Cada subscrição tem uma regra predefinida que utiliza o filtro verdadeiro para permitir todas as mensagens de entrada. Quando adicionar uma nova regra, lembre-se de remover o filtro predefinido para que o filtro da sua nova regra funcione. Se uma subscrição não tiver regras, então não receberá mensagens.

> [!NOTE]
> Você pode gerir recursos de ônibus de serviço com [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). O Service Bus Explorer permite que os utilizadores se conectem a um espaço de nome do Bus de Serviço e administram entidades de mensagens de forma fácil. A ferramenta fornece funcionalidades avançadas como funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, subscrições, serviços de retransmissão, centros de notificação e centros de eventos. 

## <a name="next-steps"></a>Passos Seguintes
Para saber mais, consulte os seguintes recursos.

- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Check-out [outras amostras de Nodejs para ônibus de serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)


