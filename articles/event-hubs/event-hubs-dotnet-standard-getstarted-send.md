---
title: Envie e receba eventos de Azure Event Hubs usando .NET (antigo)
description: Este artigo fornece um walkthrough para criar uma aplicação .NET Core que envia/recebe eventos de/para O Azure Event Hubs usando o antigo pacote Microsoft.Azure.EventHubs.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 1d399ffec2cd702dcfa39310803bc780b85bfde1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187468"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Envie eventos ou receba eventos de Azure Event Hubs usando .NET Core (Microsoft.Azure.EventHubs)
Este quickstart mostra como enviar eventos e receber eventos de um hub de eventos usando a biblioteca **Microsoft.Azure.EventHubs** .NET Core.

> [!WARNING]
> Este quickstart utiliza o antigo pacote **Microsoft.Azure.EventHubs.** Para um arranque rápido que usa a mais recente biblioteca **Azure.Messaging.EventHubs,** consulte [Enviar e receber eventos utilizando a biblioteca Azure.Messaging.EventHubs](get-started-dotnet-standard-send-v2.md). Para mover a sua aplicação de usar a antiga biblioteca para uma nova, consulte o [Guia para migrar de Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).

## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para concluir este guia de início rápido, você precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [Ferramentas do .NET Core Visual Studio 2015 ou 2017](https://www.microsoft.com/net/core). 
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md) Em seguida, obtenha a corda de **ligação para o espaço** de nome do centro do evento seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Usa a corda de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos 
Esta secção mostra-lhe como criar uma aplicação de consola .NET Core para enviar eventos para um centro de eventos. 

> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste arranque rápido para criar o seu próprio.


### <a name="create-a-console-application"></a>Criar uma aplicação de consola

Inicie o Visual Studio. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**. Criar uma aplicação de consola .NET Core.

![Novo projeto](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

Adicione o pacote [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) da biblioteca`Microsoft.Azure.EventHubs`.NET Core ao seu projeto seguindo estes passos: 

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar** e, em seguida, procure "Microsoft.Azure.EventHubs" e, em seguida, selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

1. Adicione as seguinte declarações `using` à parte superior do ficheiro Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` para a cadeia de ligação dos Hubs de Eventos e o caminho da entidade (nome do hub de eventos individual). Substitua os marcadores de posição entre parênteses retos pelos valores adequados que foram obtidos ao criar o hub de eventos. Certifique-se de que `{Event Hubs connection string}` é a cadeia de ligação ao nível do espaço de nomes e não é a cadeia de hub de eventos. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Adicione um método novo denominado `MainAsync` à classe `Program`, da seguinte forma:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adicione um método novo denominado `SendMessagesToEventHub` à classe `Program`, da seguinte forma:

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adicione o seguinte código ao método `Main` na classe `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   O ficheiro Program.cs deve ter o seguinte aspeto.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Execute o programa e certifique-se de que não existem erros.

## <a name="receive-events"></a>Receber eventos
Esta secção mostra como escrever uma aplicação de consola .NET Core que recebe mensagens de um hub de eventos usando o Anfitrião do Processador de [Eventos](event-hubs-event-processor-host.md). O [Anfitrião do Processador de Eventos](event-hubs-event-processor-host.md) é uma classe do .NET que simplifica a receção de eventos provenientes dos hubs de eventos ao gerir pontos de verificação persistentes e receções em paralelo desses hubs de eventos. Se utilizar o Anfitrião do Processador de Eventos, pode dividir eventos por múltiplos recetores, mesmo se estiverem alojados em nós diferentes. Este exemplo mostra como utilizar o Anfitrião do Processador de Eventos para um único recetor.
> [!NOTE]
> Pode transferir este início rápido como uma amostra a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), substituir as cadeias de carateres `EventHubConnectionString` e `EventHubName`, `StorageAccountName`, `StorageAccountKey`, e `StorageContainerName` pelos seus valores de hub de eventos e executá-la. Em alternativa, pode seguir os passos neste tutorial para criar a sua própria.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

Inicie o Visual Studio. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**. Criar uma aplicação de consola .NET Core.

![Novo projeto](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

Adicione os pacotes NuGet da biblioteca .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) ao projeto, através destes passos: 

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar**, procure **Microsoft.Azure.EventHubs** e, em seguida, selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.
3. Repita os passos 1 e 2 e instale o pacote **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. No Explorador de Soluções, clique com o botão direito do rato no projeto, clique em **Adicionar** e, em seguida, em **Classe**. Atribua um nome à nova classe **SimpleEventProcessor**.

2. Abra o ficheiro SimpleEventProcessor.cs e adicione as seguintes declarações `using` na parte superior do ficheiro.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implemente a interface `IEventProcessor`. Substitua os conteúdos integrais da classe `SimpleEventProcessor` pelo seguinte código:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Atualize o método Principal para utilizar SimpleEventProcessor

1. Adicione as seguinte declarações `using` à parte superior do ficheiro Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` para a cadeia de ligação do hub de eventos, o nome do hub de eventos, o nome do contentor da conta de armazenamento, o nome de conta de armazenamento e a chave da conta de armazenamento. Adicione o seguinte código, ao substituir os marcadores de posição pelos valores correspondentes:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Adicione um método novo denominado `MainAsync` à classe `Program`, da seguinte forma:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Adicione a seguinte linha de código ao método `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    O ficheiro Program.cs deve ter o seguinte aspeto:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Execute o programa e certifique-se de que não existem erros.


## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [Amostras de controlo de acesso baseadas em funções (RBAC).](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode facilmente atualizá-la para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar de Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).)
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funcionalidades e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)


