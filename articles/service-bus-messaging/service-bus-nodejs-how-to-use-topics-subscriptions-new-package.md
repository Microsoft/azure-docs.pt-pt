---
title: Use os tópicos e as assinaturas do Azure/Service Bus com o Node. js
description: 'Início rápido: saiba como usar os tópicos e as assinaturas do barramento de serviço no Azure de um aplicativo node. js.'
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
ms.openlocfilehash: 348a6a50583594d3e608bb16fcef65879b595e67
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263324"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Início rápido: como usar os tópicos e as assinaturas do barramento de serviço com o Node. js e o pacote do Azure/Service-Bus
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Neste tutorial, você aprenderá a escrever um programa node. js para enviar mensagens para um tópico do barramento de serviço e receber mensagens de uma assinatura do barramento de serviço usando o novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Esse pacote usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) mais antigo usava [APIs de tempo de execução REST do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Os exemplos são escritos em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure. Para concluir este tutorial, precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver um tópico e uma assinatura para trabalhar com o, siga as etapas no artigo [usar portal do Azure para criar tópicos e assinaturas do barramento de serviço](service-bus-quickstart-topics-subscriptions-portal.md) para criá-los. Anote a cadeia de conexão para sua instância do barramento de serviço e os nomes do tópico e da assinatura que você criou. Usaremos esses valores nos exemplos.

> [!NOTE]
> - Este tutorial funciona com exemplos que você pode copiar e executar usando o [NodeJS](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo node. js, consulte [criar e implantar um aplicativo node. js em um site do Azure](../app-service/app-service-web-get-started-nodejs.md)ou [serviço de nuvem do node. js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) não oferece suporte à criação de topcis e assinaturas ainda. Use o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se desejar criá-los programaticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote
Para instalar o pacote NPM para o barramento de serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta onde você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Interagir com um tópico do barramento de serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) . Depois de ter o tópico cliente, você pode criar um remetente e usar o método [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) nele para enviar mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `send.js` e cole o código abaixo nele. Esse código enviará 10 mensagens para o tópico.

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
3. Insira a cadeia de conexão e o nome do seu tópico no código acima.
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo. 

Parabéns! Você acabou de enviar mensagens para uma fila do barramento de serviço.

As mensagens têm algumas propriedades padrão como `label` e `messageId` que você pode definir ao enviar. Se você quiser definir qualquer propriedade personalizada, use o `userProperties`, que é um objeto JSON que pode conter pares de chave-valor de seus dados personalizados.

Os tópicos do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens mantidas em um tópico, mas há um limite no tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento de criação, com um limite superior de 5 GB. Para obter mais informações sobre cotas, consulte [cotas do barramento de serviço](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
Interagir com uma assinatura do barramento de serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) . Depois de ter o cliente de assinatura, você pode criar um receptor e usar o método [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `recieve.js` e cole o código abaixo nele. Esse código tentará receber 10 mensagens de sua assinatura. A contagem real que você recebe depende do número de mensagens na assinatura e na latência de rede.

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
3. Insira a cadeia de conexão e os nomes de seu tópico e assinatura no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de receber mensagens de uma assinatura do barramento de serviço.

O método [Createreceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) usa um `ReceiveMode` que é um enum com os valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [Peeklock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se usar o modo de `PeekLock` usando qualquer um dos métodos `complete()`, `abandon()`, `defer()`ou `deadletter()` na mensagem.

## <a name="subscription-filters-and-actions"></a>Filtros de assinatura e ações
O barramento de serviço dá suporte a [filtros e ações em assinaturas](topic-filters.md), o que permite filtrar as mensagens de entrada para uma assinatura e editar suas propriedades.

Depois de ter uma instância de um `SubscriptionClient` você pode usar os métodos abaixo para obter, adicionar e remover regras na assinatura para controlar os filtros e as ações.

- getRules
- addRule
- removeRule

Cada assinatura tem uma regra padrão que usa o filtro verdadeiro para permitir todas as mensagens de entrada. Ao adicionar uma nova regra, lembre-se de remover o filtro padrão para que o filtro em sua nova regra funcione. Se uma assinatura não tiver regras, ele não receberá mensagens.

> [!NOTE]
> Você pode gerenciar os recursos do barramento de serviço com o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador do barramento de serviço permite que os usuários se conectem a um namespace do barramento de serviço e administrem entidades de mensagens de maneira fácil. A ferramenta fornece recursos avançados como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximos Passos
Para saber mais, confira os recursos a seguir.

- [Filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md)
- Fazer check-out [de outros exemplos de NodeJS para o barramento de serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)


