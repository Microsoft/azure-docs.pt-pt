---
title: Arquitetura push empresa de Centros de Notificação
description: Saiba mais sobre a utilização de Hubs de Notificação Azure em ambiente empresarial
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76264038"
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação de arquitetura das notificações push empresariais

As empresas de hoje caminham gradualmente para a criação de aplicações móveis para os seus utilizadores finais (externos) ou para os colaboradores (internos). Têm sistemas de backend existentes, sejam eles os principais quadros ou algumas aplicações LoB, que devem ser integrados na arquitetura de aplicações móveis. Este guia fala sobre a melhor forma de fazer esta integração recomendando uma possível solução para cenários comuns.

Um requisito frequente é o envio de notificação push aos utilizadores através da sua aplicação móvel quando um evento de interesse ocorre nos sistemas de backend. Por exemplo, um cliente bancário que tenha a app bancária do banco num iPhone quer ser notificado quando um débito é feito acima de um determinado valor da conta ou um cenário intranet em que um funcionário do departamento de finanças que tem uma app de aprovação de orçamento num Windows Phone quer ser notificado quando o pedido de aprovação é recebido.

É provável que a conta bancária ou o processamento de aprovação sejam feitos em algum sistema de backend, que deve iniciar um impulso ao utilizador. Pode haver vários sistemas de backend, que devem todos construir o mesmo tipo de lógica para empurrar quando um evento desencadeia uma notificação. A complexidade aqui reside na integração de vários sistemas de backend juntamente com um único sistema de push onde os utilizadores finais podem ter subscrito notificações diferentes e pode até haver várias aplicações móveis. Por exemplo, aplicações móveis intranet onde uma aplicação móvel pode querer receber notificações de vários desses sistemas de backend. Os sistemas de backend não sabem ou precisam de saber sobre a semântica/tecnologia de impulso, pelo que uma solução comum aqui tradicionalmente tem sido introduzir uma componente, que sondagem os sistemas de backend para quaisquer eventos de interesse e é responsável pelo envio das mensagens push ao cliente.

Uma solução melhor é utilizar o Azure Service Bus - Modelo tópico/subscrição, o que reduz a complexidade ao mesmo tempo que tornará a solução escalável.

Aqui está a arquitetura geral da solução (generalizada com várias aplicações móveis mas igualmente aplicável quando há apenas uma aplicação móvel)

## <a name="architecture"></a>Arquitetura

![][1]

A peça-chave neste diagrama arquitetónico é o Azure Service Bus, que fornece um modelo de programação de tópicos/subscrições (mais sobre ele no [Service Bus Pub/Sub programação).] O recetor, que neste caso, é o backend Mobile (tipicamente [Azure Mobile Service], que inicia um impulso para as aplicações móveis) não recebe mensagens diretamente dos sistemas de backend, mas sim uma camada de abstração intermédia fornecida pela [Azure Service Bus,]que permite que o backend móvel receba mensagens de um ou mais sistemas de backend. É necessário criar um Tópico de Autocarrode Serviço para cada um dos sistemas de backend, por exemplo, Conta, RH, Finanças, que são basicamente "tópicos" de interesse, que iniciam mensagens a serem enviadas como notificação push. Os sistemas de backend enviam mensagens para estes tópicos. Um Backend Móvel pode subscrever um ou mais tópicos deste tipo criando uma subscrição de Ônibus de serviço. Dá direito ao backend móvel para receber uma notificação do sistema de backend correspondente. O backend móvel continua a ouvir mensagens nas suas subscrições e assim que uma mensagem chega, volta e envia-a como notificação para o seu centro de notificação. Os centros de notificação acabam por entregar a mensagem à aplicação móvel. Aqui está a lista de componentes-chave:

1. Sistemas backend (sistemas LoB/Legacy)
   * Cria tópico de ônibus de serviço
   * Envia mensagem
1. Backend móvel
   * Cria subscrição de serviço
   * Recebe mensagem (do sistema Backend)
   * Envia notificação aos clientes (via Azure Notification Hub)
1. Aplicação Móvel
   * Recebe e exibe notificação

### <a name="benefits"></a>Benefícios

1. A dissociação entre o recetor (aplicação/serviço móvel via Notification Hub) e o remetente (sistemas de backend) permite a integração de sistemas de backend adicionais com alterações mínimas.
1. Também faz com que o cenário de várias aplicações móveis possam receber eventos de um ou mais sistemas de backend.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Pré-requisitos

Complete os seguintes tutoriais para familiarizar com os conceitos, bem como criação comum & passos de configuração:

1. [Service Bus/Sub programming] - Este tutorial explica os detalhes de trabalhar com tópicos/subscrições de ônibus de serviço, como criar um espaço de nome para conter tópicos/subscrições, como enviar & receber mensagens deles.
2. Centros de [Notificação - Tutorial Do Windows Universal] - Este tutorial explica como configurar uma aplicação da Windows Store e utilizar centros de notificação para registar e, em seguida, receber notificações.

### <a name="sample-code"></a>Código de exemplo

O código completo da amostra está disponível nas Amostras do Centro de [Notificação.] É dividido em três componentes:

1. **EnterprisePushBackendSystem**

    a. Este projeto utiliza o pacote **WindowsAzure.ServiceBus** NuGet e baseia-se na [programação Service Bus Pub/Sub].

    b. Esta aplicação é uma simples aplicação de consola C# para simular um sistema LoB, que inicia a mensagem a ser entregue na aplicação móvel.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic`é usado para criar o tópico do Ônibus de serviço.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage`é usado para enviar as mensagens para este Tópico de Ônibus de Serviço. Este código simplesmente envia um conjunto de mensagens aleatórias para o tópico periodicamente para efeitos da amostra. Normalmente existe um sistema de backend, que envia mensagens quando ocorre um evento.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **Notificação receberandsend**

    a. Este projeto utiliza o *WindowsAzure.ServiceBus* e **microsoft.Web.WebJobs.Publish** NuGet pacotes e baseia-se na [programação Service Bus Pub/Sub].

    b. A seguinte aplicação de consola funciona como um [WebJob Azure,] uma vez que tem de ser executada continuamente para ouvir mensagens dos sistemas LoB/backend. Esta aplicação faz parte do seu backend Móvel.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription`é usado para criar uma subscrição de Ônibus de serviço para o tópico onde o sistema de backend envia mensagens. Dependendo do cenário de negócio, este componente cria uma ou mais subscrições para tópicos correspondentes (por exemplo, alguns podem estar a receber mensagens do sistema de RH, alguns do sistema Financeiro, e assim por diante)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`é utilizado para ler a mensagem a partir do tópico utilizando a sua subscrição e se a leitura for bem sucedida, então ecraftuma notificação (no cenário da amostra uma notificação de torradanativa do Windows) a ser enviada para a aplicação móvel utilizando hubs de notificação Azure.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Para publicar esta aplicação como **WebJob,** clique na solução no Estúdio Visual e **selecione Publicar como WebJob**

    ![][2]

    f. Selecione o seu perfil de publicação e crie um novo WebSite Azure se já não existir, que acolhe este WebJob e uma vez que tenha o WebSite e, em seguida, **publique**.

    ![][3]

    g. Configure o trabalho para ser "Run Continuamente" para que quando iniciar sessão no [portal Azure] veja algo como o seguinte:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Esta aplicação é uma aplicação da Windows Store, que recebe notificações de torradas do WebJob que funciona como parte do seu backend Móvel e a exibe. Este código baseia-se em Centros de [Notificação - tutorial Windows Universal].  

    b. Certifique-se de que a sua aplicação está ativada para receber notificações de torradas.

    c. Certifique-se de que o seguinte código de registo dos Centros `HubName` de `DefaultListenSharedAccessSignature` Notificação está a ser chamado no arranque da App (após a substituição dos valores e valores:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Executar o exemplo

1. Certifique-se de que o seu WebJob está a funcionar com sucesso e programado para funcionar continuamente.
2. Executar o **EnterprisePushMobileApp**, que inicia a aplicação Da Loja do Windows.
3. Executar a aplicação de consola **EnterprisePushBackendSystem,** que simula o backend loB e começa a enviar mensagens e deve ver notificações de torradas aparecendo como a seguinte imagem:

    ![][5]

4. As mensagens foram originalmente enviadas para os tópicos do Service Bus, que estava a ser monitorizado por subscrições de Service Bus no seu Web Job. Uma vez recebida uma mensagem, foi criada uma notificação e enviada para a aplicação móvel. Pode ver os registos webJob para confirmar o processamento quando for ao link DeRegistos no [portal Azure] para o seu Trabalho Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Amostras de hub de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço Móvel Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Service Bus do Azure]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação de bus de serviço/sub]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Centros de Notificação - Tutorial Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal do Azure]: https://portal.azure.com/
