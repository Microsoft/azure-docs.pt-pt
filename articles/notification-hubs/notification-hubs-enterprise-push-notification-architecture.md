---
title: Hubs de notificação - arquitetura empresarial de Push
description: Documentação de orientação sobre como utilizar Notification Hubs do Azure num ambiente empresarial
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: c934a3b16f5cdd2b4f703b1be15ce16ddc6d8746
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338484"
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação de arquitetura das notificações push empresariais

As empresas hoje em dia são gradualmente movendo em direção à criação de aplicativos móveis para qualquer um que os utilizadores finais (externo) ou para os funcionários (internos). Eles têm existentes sistemas back-end no local seja mainframes ou alguns aplicativos de LoB, que devem ser integrados a arquitetura de aplicações móveis. Este guia discute a melhor maneira de fazer esta integração recomendando solução possível para cenários comuns.

É um requisito frequente para o envio de notificação push para os usuários pelas suas aplicações móveis, quando ocorre um evento de interesse nos sistemas back-end. Por exemplo, um cliente bancário que tenha a aplicação de banca o banco num iPhone quiser ser notificado quando um débito é feito acima de um determinado período de um cenário de intranet onde quer que um funcionário do departamento financeiro que tem uma aplicação de aprovação de orçamento num Windows Phone ou a conta do utilizador  para ser notificado quando é recebido o pedido de aprovação.

O processamento de aprovação ou a conta bancária, é provável que ser feita em algum sistema de back-end, que tem de iniciar um push para o usuário. Pode haver vários desses sistemas de back-end, que tem de criar todas o mesmo tipo de lógica para enviar por push quando um evento aciona uma notificação. A complexidade aqui reside na integração de vários sistemas de back-end, juntamente com um sistema de push único onde os utilizadores finais podem no qual se inscreveu para diferentes notificações e poderá até haver vários aplicativos móveis. Por exemplo, intranet aplicações móveis em que um aplicativo móvel poderá querer receber notificações de vários desses sistemas de back-end. Os sistemas de back-end não conhecer ou se precisar de push semântica/tecnologia, para uma solução comum aqui tem estado a introdução de um componente, que consulta os sistemas de back-end para todos os eventos de interesse e é responsável por enviar as mensagens de push para o cliente.

Uma solução melhor é usar o Azure Service Bus - modelo do tópico/subscrição, o que reduz a complexidade ao efetuar a solução dimensionável.

Eis a arquitetura geral da solução (generalizada com várias aplicações móveis, mas igualmente aplicáveis quando existe apenas uma aplicação móvel)

## <a name="architecture"></a>Arquitetura

![][1]

A parte principal neste diagrama da arquitetura é o Azure Service Bus, que fornece um modelo de programação tópicos/subscrições (mais informações sobre ele na [Programação do Service Bus Pub/Sub]). O receptor, que neste caso, é o back-end móvel (normalmente [Serviço móvel do Azure], que inicia um push para aplicações móveis) não recebe mensagens diretamente a partir de sistemas de back-end, mas em vez disso, uma abstração intermédia camada fornecida pela [Azure Service Bus], que permite que o back-end móvel receber mensagens de um ou mais sistemas de back-end. Um tópico do Service Bus tem de ser criada para cada um dos sistemas de back-end, por exemplo, conta, RH, Finanças, que é, basicamente, "tópicos" de interesse, que inicia as mensagens sejam enviadas como notificação push. Os sistemas de back-end enviam mensagens para esses tópicos. Um back-end do Mobile, pode subscrever uma ou mais tópicos através da criação de uma subscrição do Service Bus. Ele dá direito o back-end móvel para receber uma notificação do sistema de back-end correspondente. Back-end móvel continua a escuta de mensagens em suas assinaturas e assim que uma mensagem chega, volte a ativar e envia-os como notificação ao seu hub de notificação. Os hubs de notificação, em seguida, eventualmente entregar a mensagem para a aplicação móvel. Eis a lista de componentes principais:

1. Sistemas de back-end (sistemas de LoB/legado)
   * Cria o tópico do Service Bus
   * Envia a mensagem
1. Back-end móvel
   * Cria a subscrição do serviço
   * Receber mensagem (de sistema de back-end)
   * Envia a notificação para os clientes (através do Hub de notificação do Azure)
1. Aplicações móveis
   * Recebe e apresentar a notificação

### <a name="benefits"></a>Benefícios

1. A separação entre o destinatário (aplicação/Serviço móvel através do Hub de notificação) e o remetente (sistemas de back-end) permite que os sistemas de back-end adicional a ser integrados com o mínimo de alterações.
1. Ele também torna o cenário de várias aplicações móveis que está a ser capaz de receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Pré-requisitos

Conclua os seguintes tutoriais para se familiarizar com os conceitos, bem como passos de criação e configuração comuns:

1. [Programação do Service Bus Pub/Sub] -este tutorial explica os detalhes do trabalho com o Service Bus tópicos/subscrições, como criar um espaço de nomes para conter tópicos/subscrições, como enviar e receber mensagens a partir deles.
2. [Hubs de notificação - tutorial Windows Universal] -este tutorial explica como configurar uma aplicação da Windows Store e utilizar os Hubs de notificação para se registrar e, em seguida, receber notificações.

### <a name="sample-code"></a>Código de exemplo

O código de exemplo completo está disponível em [Exemplos do Hub de notificação]. Ele é dividido em três componentes:

1. **EnterprisePushBackendSystem**

    a. Esse projeto usa o **windowsazure. Servicebus** pacote NuGet e se baseia na [programação do Service Bus Pub/Sub].

    b. Esse aplicativo é uma simple c# aplicação de consola para simular um sistema LoB, que inicia a mensagem ser entregue para a aplicação móvel.

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

    c. `CreateTopic` é utilizado para criar o tópico do Service Bus.

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

    d. `SendMessage` é utilizado para enviar as mensagens para este tópico do Service Bus. Esse código simplesmente envia um conjunto de mensagens aleatórios para o tópico periodicamente para fins de exemplo. Normalmente, é um sistema de back-end, que envia mensagens quando ocorre um evento.

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

    a. Esse projeto usa o *windowsazure. Servicebus* e **Microsoft.Web.WebJobs.Publish** NuGet pacotes e baseia-se no [programação do Service Bus Pub/Sub].

    b. A aplicação de consola seguinte é executado como um [Azure WebJob] , uma vez que ele precisa ser executado continuamente para escutar as mensagens dos sistemas LoB/back-end. Esta aplicação faz parte do seu back-end móvel.

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

    c. `CreateSubscription` é utilizado para criar uma subscrição do Service Bus para o tópico, onde o sistema de back-end envia mensagens. Dependendo do cenário de negócio, este componente cria uma ou mais subscrições para tópicos correspondentes (por exemplo, alguns podem receber mensagens do sistema de RH, alguns do sistema de finanças e assim por diante)

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

    d. `ReceiveMessageAndSendNotification` é utilizada para ler a mensagem de tópico com a sua subscrição e se a leitura for bem sucedida, em seguida, crie uma notificação (no cenário de amostra uma notificação de alerta nativo do Windows) a serem enviados para o aplicativo móvel com os Hubs de notificação do Azure.

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

    e. Para publicar esta aplicação como uma **WebJob**, clique com o botão direito do rato na solução no Visual Studio e selecione **publicar como WebJob**

    ![][2]

    f. Selecione o perfil de publicação e crie um novo Web site do Azure, caso ainda não exista, que aloja este WebJob e assim que tiver o Web site, em seguida, **publicar**.

    ![][3]

    g. Configurar a tarefa de ser "Executar continuamente", de modo que quando iniciar sessão para o [portal do Azure] deverá ver algo semelhante ao seguinte:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Esse aplicativo é um aplicativo da Windows Store, que recebe notificações de alerta do WebJob em execução como parte do seu back-end móvel e apresentá-lo. Este código é baseado num [Hubs de notificação - tutorial Windows Universal].  

    b. Certifique-se de que a sua aplicação está ativada para receber notificações de alerta.

    c. Certifique-se de que os Hubs de notificação seguinte código de registo está a ser chamado a aplicação iniciar (depois de substituir a `HubName` e `DefaultListenSharedAccessSignature` valores:

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

1. Certifique-se de que o WebJob está em execução com êxito e agendada para ser executada continuamente.
2. Executar o **EnterprisePushMobileApp**, que inicia a aplicação da Windows Store.
3. Executar o **EnterprisePushBackendSystem** aplicativo de console, que simula o back-end de LoB e começa a enviar mensagens e deverá ver as notificações de alerta que aparecem semelhante à imagem seguinte:

    ![][5]

4. As mensagens foram originalmente enviadas para tópicos do Service Bus, o que estava a ser monitorizado por subscrições do Service Bus na sua tarefa de Web. Uma vez que foi recebida uma mensagem, uma notificação foi criada e enviada para a aplicação móvel. Pode consultar os registos WebJob para confirmar o processamento quando vai para a ligação de registos no [portal do Azure] para a sua tarefa Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemplos do Hub de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço móvel do Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação do Service Bus Pub/Sub]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Hubs de notificação - tutorial Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal do Azure]: https://portal.azure.com/
