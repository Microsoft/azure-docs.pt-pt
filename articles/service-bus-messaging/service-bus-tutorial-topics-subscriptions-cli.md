---
title: Tutorial – Atualizar variedade de inventário de comércio a retalho utilizando canais de publicação/subscrição e filtros de tópico com a CLI do Azure | Microsoft Docs
description: Neste tutorial, aprende a enviar e receber mensagens de um tópico e subscrição e como adicionar e utilizar regras de filtro utilizando a CLI do Azure
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: a75f3f2b1cc6d352afdf366d17c78e95420184cd
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571240"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Tutorial: Atualizar inventário utilizando a CLI e tópicos/subscrições

O Microsoft Azure Service Bus é um serviço de mensagens na cloud multi-inquilino que envia informações entre aplicações e serviços. As operações assíncronas permitem o envio flexível de mensagens mediadas, juntamente com mensagens FIFO (first in, first out) e funcionalidades de publicação/subscrição. Este tutorial mostra como utilizar tópicos e subscrições do Service Bus num cenário de inventário de comércio a retalho, com canais de publicação/subscrição utilizando a CLI do Azure e Java.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um tópico e uma ou mais subscrições do Service Bus para esse tópico utilizando a CLI do Azure
> * Adicionar filtros de tópico utilizando a CLI do Azure
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar que chegaram nas subscrições previstas
> * Receber mensagens das subscrições

Um exemplo deste cenário é uma atualização da variedade de inventário para várias lojas de comércio a retalho. Neste cenário, cada loja ou grupo de lojas recebe mensagens dirigidas a elas para atualizar a respetiva variedade. Este tutorial mostra como implementar este cenário com subscrições e filtros. Primeiro, cria um tópico com três subscrições, adiciona algumas regras e filtros e, em seguida, envia e recebe mensagens do tópico e subscrições.

![tópico](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver uma aplicação Service Bus com Java, tem de ter o seguinte instalado:

- [Kit de Desenvolvimento de Java](https://aka.ms/azure-jdks), versão mais recente.
- [CLI do Azure](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos e subscrições do Service Bus

Cada [subscrição de um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos são totalmente compatíveis no que diz respeito a protocolo e semântica com as filas do Service Bus. Os tópicos do Service Bus suportam uma vasta gama de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam propriedades de mensagem. Sempre que uma regra tem correspondência, é criada uma mensagem. Para saber mais sobre regras, filtros e ações, clique nesta [hiperligação](topic-filters.md).

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

Assim que a CLI estiver instalada, abra uma linha de comandos e indique os seguintes comandos para iniciar sessão no Azure. Estes passos não serão necessários se estiver a utilizar a Cloud Shell:

1. Se estiver a utilizar a CLI do Azure localmente, execute o seguinte comando para iniciar sessão no Azure. Este passo de início de sessão não é necessário estiver a executar estes comandos na Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. Defina o contexto da subscrição atual da subscrição do Azure que pretende utilizar:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Utilizar a CLI para aprovisionar recursos

Indique os seguintes comandos para aprovisionar recursos do Service Bus. Não se esqueça de substituir todos os marcadores de posição pelos valores apropriados:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Depois da execução do último comando, copie e cole a cadeia de ligação e o nome de fila selecionado para uma localização temporária como o Bloco de notas. Vai precisar dele no próximo passo.

## <a name="create-filter-rules-on-subscriptions"></a>Criar regras de filtro nas subscrições

Depois do aprovisionamento do espaço de nomes e tópico/subscrições e de ter as credenciais necessárias, está pronto para criar regras de filtro nas subscrições e enviar e receber mensagens. Pode examinar o código nesta [pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

1. Certifique-se de que a Cloud Shell está aberta e apresenta a linha de comandos do Bash.

2. Clone o [repositório do GitHub do Service Bus](https://github.com/Azure/azure-service-bus/) ao emitir o seguinte comando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navegue para a pasta de exemplo `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Tenha em atenção que na Bash shell, os comandos são sensíveis a maiúsculas e minúsculas e os separadores de caminho têm de ser barras.

3. Indique o seguinte comando para criar a aplicação:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Para executar o programa, indique o seguinte comando. Certifique-se de que substitui os marcadores de posição pela cadeia de ligação e nome do tópico obtido no passo anterior:

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Observe o envio de 10 mensagens para o tópico e subsequentemente recebidas das subscrições individuais:

   ![saída do programa](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Limpar recursos

Execute o seguinte comando para remover o grupo de recursos, o espaço de nomes e todos os recursos relacionados:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Esta secção contém mais detalhes sobre o que faz o código de exemplo.

### <a name="get-connection-string-and-queue"></a>Obter a cadeia de ligação e a fila

Primeiro, o código declara um conjunto de variáveis, que aciona a execução restante do programa:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

A cadeia de ligação e o nome do tópico são os únicos valores adicionados através da linha de comandos e transmitidos a `main()`. A execução do código atual é acionada no método `run()` e envia e recebe mensagens do tópico:

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Criar cliente de tópico para enviar mensagens

Para enviar e receber mensagens, o método `sendMessagesToTopic()` cria uma instância de cliente de tópico, que utiliza a cadeia de ligação e o nome do tópico e invoca outro método que envia as mensagens:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Receber mensagens das subscrições individuais

O método `receiveAllMessages()` invoca o método `receiveAllMessageFromSubscription()`, que cria um cliente de subscrição por chamada e recebe mensagens das subscrições individuais:

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprovisionou recursos utilizando a CLI do Azure e enviou e recebeu mensagens de um tópico do Service Bus e respetivas subscrições. Aprendeu a:

> [!div class="checklist"]
> * Criar um tópico e uma ou mais subscrições do Service Bus para esse tópico com o portal do Azure
> * Adicionar filtros de tópico com o código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar que chegaram nas subscrições previstas
> * Receber mensagens das subscrições

Para ver mais exemplos sobre o envio e receção de mensagens, comece com os [exemplos do Service Bus do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre a utilização de capacidades de publicação/subscrição do Service Bus.

> [!div class="nextstepaction"]
> [Atualizar inventário com o PowerShell e tópicos/subscrições](service-bus-tutorial-topics-subscriptions-portal.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
