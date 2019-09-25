---
title: Hubs de notificação-arquitetura de push empresarial
description: Orientação sobre como usar os hubs de notificação do Azure em um ambiente corporativo
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
ms.openlocfilehash: 5b65fe6acb1fdf7ba79b106c876527c9b6736c5f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211899"
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação de arquitetura das notificações push empresariais

Hoje, as empresas estão migrando gradualmente para a criação de aplicativos móveis para os usuários finais (externos) ou para os funcionários (internos). Eles têm sistemas de back-end existentes em vigor para mainframes ou alguns aplicativos LoB, que devem ser integrados à arquitetura do aplicativo móvel. Este guia fala sobre a melhor maneira de fazer essa integração recomendando a possível solução para cenários comuns.

Um requisito frequente é o envio de notificação por push aos usuários por meio de seu aplicativo móvel quando ocorre um evento de interesse nos sistemas de back-end. Por exemplo, um cliente bancário que tem o aplicativo bancário do banco em um iPhone deseja ser notificado quando um débito é feito acima de um determinado valor do cenário da conta ou da intranet em que um funcionário do departamento financeiro que tem um aplicativo de aprovação de orçamento em um Windows Phone deseja  ser notificado quando a solicitação de aprovação for recebida.

É provável que a conta bancária ou o processamento de aprovação seja feito em algum sistema de back-end, o que deve iniciar um envio por push ao usuário. Pode haver vários sistemas de back-end, que devem criar o mesmo tipo de lógica para enviar por push quando um evento disparar uma notificação. A complexidade aqui está na integração de vários sistemas de back-end, juntamente com um único sistema de envio por push, em que os usuários finais podem ter se inscrito em diferentes notificações e pode haver até mesmo vários aplicativos móveis. Por exemplo, aplicativos móveis de intranet em que um aplicativo móvel pode querer receber notificações de vários sistemas de back-end. Os sistemas de back-end não sabem nem precisam conhecer a semântica/tecnologia de push para que uma solução comum aqui tradicionalmente tenha sido introduzir um componente, que sonda os sistemas de back-end para todos os eventos de interesse e é responsável por enviar as mensagens de envio por push para o cliente.

Uma solução melhor é usar o modelo de tópico/assinatura do barramento de serviço do Azure, que reduz a complexidade e, ao mesmo tempo, torna a solução escalonável.

Aqui está a arquitetura geral da solução (generalizada com vários aplicativos móveis, mas igualmente aplicável quando há apenas um aplicativo móvel)

## <a name="architecture"></a>Arquitetura

![][1]

A parte fundamental deste diagrama arquitetônico é o barramento de serviço do Azure, que fornece um modelo de programação de tópicos/assinaturas (mais sobre isso na [Programação de pub/sub do barramento de serviço]). O receptor, que nesse caso, é o back-end móvel (normalmente, o [serviço móvel do Azure], que inicia um envio por push para os aplicativos móveis) não recebe mensagens diretamente dos sistemas de back-end, mas sim uma camada de abstração intermediária fornecida pelo [Azure Service Bus], que permite que o back-end móvel receba mensagens de um ou mais sistemas de back-end. Um tópico do barramento de serviço precisa ser criado para cada um dos sistemas de back-end, por exemplo, conta, RH, finanças, que é basicamente "tópicos" de interesse, que inicia as mensagens a serem enviadas como notificação por push. Os sistemas de back-end enviam mensagens para esses tópicos. Um back-end móvel pode assinar um ou mais tópicos, criando uma assinatura do barramento de serviço. Ele autoriza o back-end móvel a receber uma notificação do sistema de back-end correspondente. O back-end móvel continua a escutar mensagens em suas assinaturas e assim que uma mensagem chega, ela volta e a envia como notificação para seu hub de notificação. Os hubs de notificação eventualmente entregam a mensagem para o aplicativo móvel. Aqui está a lista de componentes principais:

1. Sistemas de back-end (LoB/sistemas herdados)
   * Cria o tópico do barramento de serviço
   * Envia mensagem
1. Back-end móvel
   * Cria a assinatura do serviço
   * Recebe mensagem (do sistema de back-end)
   * Envia notificação aos clientes (por meio do hub de notificação do Azure)
1. Aplicativo móvel
   * Recebe e exibe notificação

### <a name="benefits"></a>Benefícios

1. A desassociação entre o receptor (aplicativo móvel/serviço via hub de notificação) e o remetente (sistemas de back-end) permite que sistemas de back-end adicionais sejam integrados com alterações mínimas.
1. Ele também faz com que o cenário de vários aplicativos móveis seja capaz de receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Exemplo

### <a name="prerequisites"></a>Pré-requisitos

Conclua os tutoriais a seguir para se familiarizar com os conceitos, bem como a criação comum & etapas de configuração:

1. [Programação de pub/sub do barramento de serviço] – este tutorial explica os detalhes de como trabalhar com tópicos/assinaturas do barramento de serviço, como criar um namespace para conter tópicos/assinaturas, como enviar & receber mensagens deles.
2. [Hubs de notificação-tutorial universal do Windows] – este tutorial explica como configurar um aplicativo da Windows Store e usar os hubs de notificação para registrar e receber notificações.

### <a name="sample-code"></a>Código de exemplo

O código de exemplo completo está disponível em [Exemplos de Hub de notificação]. Ele é dividido em três componentes:

1. **EnterprisePushBackendSystem**

    a. Este projeto usa o pacote NuGet **WindowsAzure. ServiceBus** e baseia-se na [Programação de pub/sub do barramento de serviço].

    b. Esse aplicativo é um aplicativo C# de console simples para simular um sistema LOB, que inicia a mensagem a ser entregue ao aplicativo móvel.

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

    c. `CreateTopic`é usado para criar o tópico do barramento de serviço.

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

    d. `SendMessage`é usado para enviar as mensagens para este tópico do barramento de serviço. Esse código simplesmente envia um conjunto de mensagens aleatórias para o tópico periodicamente para fins de exemplo. Normalmente, há um sistema de back-end, que envia mensagens quando um evento ocorre.

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
2. **ReceiveAndSendNotification**

    a. Este projeto usa os pacotes NuGet *WindowsAzure. ServiceBus* e **Microsoft. Web. webjobs. publish** e baseia-se na [Programação de pub/sub do barramento de serviço].

    b. O aplicativo de console a seguir é executado como um [WebJob do Azure] , pois ele precisa ser executado continuamente para escutar mensagens dos sistemas LOB/back-end. Este aplicativo faz parte do seu back-end móvel.

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

    c. `CreateSubscription`é usado para criar uma assinatura do barramento de serviço para o tópico em que o sistema de back-end envia mensagens. Dependendo do cenário de negócios, esse componente cria uma ou mais assinaturas para tópicos correspondentes (por exemplo, algumas podem estar recebendo mensagens do sistema de RH, algumas do sistema financeiro e assim por diante)

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

    d. `ReceiveMessageAndSendNotification`é usado para ler a mensagem do tópico usando sua assinatura e se a leitura for bem-sucedida e, em seguida, criar uma notificação (no cenário de exemplo, uma notificação do sistema nativo do Windows) a ser enviada ao aplicativo móvel usando os hubs de notificação do Azure.

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

    e. Para publicar esse aplicativo como um **WebJob**, clique com o botão direito do mouse na solução no Visual Studio e selecione **publicar como webjob**

    ![][2]

    f. Selecione seu perfil de publicação e crie um novo site do Azure se ele ainda não existir, que hospeda esse WebJob e assim que o site é **publicado**.

    ![][3]

    g. Configure o trabalho para ser "executado continuamente" para que, quando você fizer logon no [Azure portal] você deverá ver algo semelhante ao seguinte:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Esse aplicativo é um aplicativo da Windows Store, que recebe notificações do sistema de trabalho Web em execução como parte do seu back-end móvel e a exibe. Esse código é baseado em [Hubs de notificação-tutorial universal do Windows].  

    b. Verifique se seu aplicativo está habilitado para receber notificações do sistema.

    c. Verifique se o código de registro de hubs de notificação a seguir está sendo chamado na inicialização do aplicativo ( `HubName` depois `DefaultListenSharedAccessSignature` de substituir os valores e:

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

1. Verifique se o WebJob está sendo executado com êxito e agendado para execução contínua.
2. Execute o **EnterprisePushMobileApp**, que inicia o aplicativo da Windows Store.
3. Execute o aplicativo de console **EnterprisePushBackendSystem** , que simula o back-end LOB e começa a enviar mensagens e você deve ver que as notificações do sistema aparecem como a imagem a seguir:

    ![][5]

4. As mensagens foram enviadas originalmente para tópicos do barramento de serviço, que estava sendo monitorado por assinaturas do barramento de serviço em seu trabalho Web. Depois que uma mensagem foi recebida, uma notificação foi criada e enviada para o aplicativo móvel. Você pode examinar os logs do WebJob para confirmar o processamento ao acessar o link logs em [Azure portal] para seu trabalho Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemplos de Hub de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço móvel do Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação de pub/sub do barramento de serviço]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[WebJob do Azure]: ../app-service/webjobs-create.md
[Hubs de notificação-tutorial universal do Windows]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure portal]: https://portal.azure.com/
