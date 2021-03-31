---
title: Empresa de Centros de Notificação empurra arquitetura
description: Saiba mais sobre a utilização de Hubs de Notificação Azure em um ambiente empresarial
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 018315b7ed468e24fb922337848d14703ffdcd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89003631"
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação de arquitetura das notificações push empresariais

As empresas de hoje estão a avançar gradualmente para a criação de aplicações móveis para os seus utilizadores finais (externos) ou para os colaboradores (internos). Dispõem de sistemas de backend existentes, sejam os mainframes ou algumas aplicações LoB, que devem ser integradas na arquitetura das aplicações móveis. Este guia fala sobre a melhor forma de fazer esta integração recomendando uma possível solução para cenários comuns.

Um requisito frequente é o envio de notificações push para os utilizadores através da sua aplicação móvel quando ocorre um evento de interesse nos sistemas backend. Por exemplo, um cliente bancário que tenha a aplicação bancária do banco num iPhone quer ser notificado quando um débito é feito acima de um determinado valor da conta ou um cenário intranet onde um funcionário do departamento de finanças que tem uma aplicação de aprovação de orçamento num Windows Phone quer ser notificado quando o pedido de aprovação é recebido.

É provável que a conta bancária ou o processamento de aprovação sejam feitos em algum sistema de backend, o que deve dar início a um empurrão para o utilizador. Pode haver vários sistemas de backend deste tipo, que devem todos construir o mesmo tipo de lógica para empurrar quando um evento desencadeia uma notificação. A complexidade aqui reside na integração de vários sistemas de backend, juntamente com um sistema de push único, onde os utilizadores finais podem ter subscrito diferentes notificações e pode até existir várias aplicações móveis. Por exemplo, aplicações móveis intranet onde uma aplicação móvel pode querer receber notificações de vários sistemas de backend. Os sistemas backend não sabem ou precisam de saber da semântica/tecnologia de pressão, pelo que uma solução comum aqui tradicionalmente tem sido introduzir um componente, que sonda os sistemas de backend para quaisquer eventos de interesse e é responsável pelo envio das mensagens push para o cliente.

Uma solução melhor é utilizar o modelo Azure Service Bus - Tópico/Subscrição, que reduz a complexidade ao mesmo tempo que torna a solução escalável.

Aqui está a arquitetura geral da solução (generalizada com múltiplas aplicações móveis, mas igualmente aplicável quando há apenas uma aplicação móvel)

## <a name="architecture"></a>Arquitetura

![Diagrama da arquitetura empresarial mostrando o fluxo através de Eventos, Subscrições e Mensagens Push.][1]

A peça-chave neste diagrama arquitetónico é a Azure Service Bus, que fornece um modelo de programação de tópicos/subscrições (mais sobre ele na [programação Service Bus Pub/Sub).] O recetor, que neste caso é o backend Mobile (normalmente [Azure Mobile Service], que inicia um push para as aplicações móveis) não recebe mensagens diretamente dos sistemas de backend, mas sim, uma camada de abstração intermédia fornecida pela [Azure Service Bus,]que permite que o backend móvel receba mensagens de um ou mais sistemas de backend. Um tópico de autocarro de serviço precisa de ser criado para cada um dos sistemas de backend, por exemplo, Conta, RH, Finanças, que é basicamente "tópicos" de interesse, que inicia mensagens a serem enviadas como notificação push. Os sistemas de backend enviam mensagens para estes tópicos. Um Backend Móvel pode subscrever um ou mais tópicos deste tipo criando uma subscrição de Service Bus. Dá direito ao backend móvel para receber uma notificação do sistema de backend correspondente. O backend móvel continua a ouvir mensagens nas suas subscrições e assim que uma mensagem chega, volta-se e envia-as como notificação para o seu centro de notificação. Os centros de notificação acabam por entregar a mensagem à aplicação móvel. Aqui está a lista de componentes-chave:

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

1. A dissociação entre o recetor (aplicação/serviço móvel via Notification Hub) e o remetente (sistemas backend) permite a integração de sistemas de backend adicionais com alterações mínimas.
1. Também torna o cenário de várias aplicações móveis poderem receber eventos de um ou mais sistemas de backend.  

## <a name="sample"></a>Sample

### <a name="prerequisites"></a>Pré-requisitos

Complete os seguintes tutoriais para familiarizar com os conceitos, bem como a criação comum & passos de configuração:

1. [Service Bus Pub/Sub programming] - Este tutorial explica os detalhes de trabalhar com os Tópicos/Subscrições do Service Bus, como criar um espaço de nome para conter tópicos/subscrições, como enviar & receber mensagens deles.
2. [Centros de Notificação - Tutorial Universal do Windows] - Este tutorial explica como configurar uma aplicação windows Store e utilizar os Centros de Notificação para registar e depois receber notificações.

### <a name="sample-code"></a>Código de exemplo

O código de amostra completo está disponível nas [Amostras do Hub de Notificação.] Divide-se em três componentes:

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

    c. `CreateTopic` é usado para criar o tópico de Service Bus.

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

    d. `SendMessage` é usado para enviar as mensagens para este Tópico de Autocarro de Serviço. Este código simplesmente envia um conjunto de mensagens aleatórias para o tópico periodicamente para efeitos da amostra. Normalmente existe um sistema de backend, que envia mensagens quando ocorre um evento.

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
2. **Receber ESendNotificação**

    a. Este projeto utiliza os pacotes *WindowsAzure.ServiceBus* e **Microsoft.Web.WebJobs.Publish** NuGet e baseia-se na [programação Service Bus Pub/Sub].

    b. A aplicação seguinte para consola funciona como [um Azure WebJob,] uma vez que tem de ser executada continuamente para ouvir mensagens dos sistemas LoB/backend. Esta aplicação faz parte do seu backend Mobile.

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

    c. `CreateSubscription` é usado para criar uma subscrição de Service Bus para o tópico onde o sistema de backend envia mensagens. Dependendo do cenário de negócio, este componente cria uma ou mais subscrições para tópicos correspondentes (por exemplo, alguns podem estar a receber mensagens do sistema de RH, algumas do sistema financeiro, e assim por diante)

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

    d. `ReceiveMessageAndSendNotification` é utilizado para ler a mensagem a partir do tópico usando a sua subscrição e se a leitura for bem sucedida, então criar uma notificação (no cenário da amostra uma notificação de torrada nativa do Windows) para ser enviada para a aplicação móvel usando Azure Notification Hubs.

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

    e. Para publicar esta aplicação como **WebJob,** clique na solução em Visual Studio e **selecione Publicar como WebJob**

    ![Screenshot das opções de clique direito a ser exibido com Publish como Azure WebJob delineado a vermelho.][2]

    f. Selecione o seu perfil de publicação e crie um novo Azure WebSite se já não existir, que acolhe este WebJob e uma vez que tenha o WebSite **então Publique**.

    :::image type="complex" source="./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png" alt-text="Screenshot mostrando o fluxo de trabalho para criar um site em Azure.":::
    Screenshot da caixa de diálogo Web Publish com a opção Microsoft Azure Websites selecionada, uma seta verde apontando para a caixa de diálogo do site Select existente com a nova opção delineada a vermelho, e uma seta verde apontando para o site Create na caixa de diálogo Microsoft Azure com o nome do Site e Criar opções delineadas a vermelho.
    :::image-end:::

    exemplo, Configure o trabalho para ser "Executado continuamente" para que quando iniciar sessão no [portal Azure] veja algo como o seguinte:

    ![Screenshot do Portal Azure com os trabalhos web de backend da empresa apresentados e os valores de Nome, Agenda e Logs delineados a vermelho.][4]

3. **EnterprisePushMobileApp**

    a. Esta aplicação é uma aplicação windows Store, que recebe notificações torradas do WebJob em execução como parte do seu backend Mobile e exibi-la. Este código baseia-se em [Centros de Notificação - Tutorial Universal do Windows.]  

    b. Certifique-se de que a sua aplicação está ativada para receber notificações de torradas.

    c. Certifique-se de que o seguinte código de registo do Centro de Notificação está a ser chamado no arranque da App (após a substituição dos `HubName` `DefaultListenSharedAccessSignature` valores e valores:

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
2. Gere a **EnterprisePushMobileApp,** que inicia a aplicação Windows Store.
3. Execute a aplicação de consola **EnterprisePushBackendSystem,** que simula o backend LoB e começa a enviar mensagens e deve ver notificações de torradas aparecendo como a seguinte imagem:

    ![Screenshot de uma consola que executa a aplicação Enterprise Push Backend System e a mensagem que é enviada pela aplicação.][5]

4. As mensagens foram originalmente enviadas para os tópicos da Service Bus, que estavam a ser monitorizadas pelas subscrições da Service Bus no seu Trabalho Web. Uma vez recebida uma mensagem, foi criada uma notificação e enviada para a aplicação móvel. Pode ver através dos registos WebJob para confirmar o processamento quando vai ao link 'Logs' no [portal Azure] para o seu Trabalho Web:

    ![Screenshot da caixa de diálogo Continuous WebJob Details com a mensagem enviada delineada a vermelho.][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Amostras do hub de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço Móvel Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: ../service-bus-messaging/service-bus-messaging-overview.md
[Programação de autocarros de serviço/sub]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[Azure WebJob]: ../app-service/webjobs-create.md
[Centros de Notificação - Tutorial Universal do Windows]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Portal do Azure]: https://portal.azure.com/
