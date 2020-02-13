---
title: Envie ou receba eventos de Azure Event Hubs usando .NET (mais recente)
description: Este artigo fornece um walkthrough para criar uma aplicação .NET Core que envia/recebe eventos de/para Azure Event Hubs usando o mais recente pacote Azure.Messaging.EventHubs.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 022af5ce0774ff106a29a2ef0bcf3fe11acfda15
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187204"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Envie eventos e receba eventos de Azure Event Hubs - .NET Core (Azure.Messaging.EventHubs) 
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando a biblioteca **Azure.Messaging.EventHubs** .NET Core. 

> [!IMPORTANT]
> Este quickstart utiliza a nova biblioteca **Azure.Messaging.EventHubs.** Para um arranque rápido que utiliza a antiga biblioteca **Microsoft.Azure.EventHubs,** consulte [Enviar e receber eventos utilizando a biblioteca Microsoft.Azure.EventHubs](event-hubs-dotnet-standard-getstarted-send.md). 



## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte a visão geral do [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para concluir este guia de início rápido, você precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure.** Para utilizar os serviços Azure, incluindo o Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os seus benefícios de subscrição MSDN quando [criar uma conta](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. A biblioteca de clientes do Azure Event Hubs C# utiliza novas funcionalidades que foram introduzidas em 8.0.  Ainda pode utilizar a biblioteca com C#versões mais antigas, mas algumas das suas funcionalidades não estarão disponíveis.  Para ativar estas funcionalidades, deve [visar o .NET Core 3.0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) ou [especificar a versão linguística](/dotnet/csharp/language-reference/configure-language-version#override-a-default) que pretende utilizar (8.0 ou superior). Se estiver a usar o Visual Studio, as versões anteriores ao Visual Studio C# 2019 não são compatíveis com as ferramentas necessárias para construir projetos 8.0. O Visual Studio 2019, incluindo a edição gratuita da Comunidade, pode ser descarregado [aqui](https://visualstudio.microsoft.com/vs/)
- **Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo.](event-hubs-create.md) Em seguida, obtenha a cadeia de **ligação para o espaço** de nome do Event Hubs seguindo as instruções do artigo: Obtenha a corda de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Usa a corda de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos 
Esta secção mostra-lhe como criar uma aplicação de consola .NET Core para enviar eventos para um centro de eventos. 

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Estúdio Visual 2019. 
1. Selecione **Criar um novo projeto.** 
1. Na caixa de diálogo Criar uma nova caixa de diálogo de **projeto,** faça os seguintes passos: Se não vir esta caixa de diálogo, selecione **File** no menu, selecione **New**, e, em seguida, selecione **Project**. 
    1. Selecione **C#** para a linguagem de programação.
    1. Selecione **Consola** para o tipo de aplicação. 
    1. Selecione **App de Consola (.NET Core)** na lista de resultados. 
    1. Em seguida, selecione **Seguinte**. 

        ![Caixa de diálogo Novo Projeto](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Introduza **eventHubsSender** para o nome do projeto, **EventHubsQuickStart** para o nome da solução e, em seguida, selecione **OK** para criar o projeto. 

    ![C#> App consola](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. Selecione **ferramentas** > **NuGet Package Manager** > Consola de Gestor de **Pacotes** do menu. 
1. Executar o seguinte comando para instalar o pacote **NuGet Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

1. Adicione as seguintes declarações `using` ao topo do ficheiro **Program.cs:**

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adicione constantes à `Program` classe para a cadeia de ligação Event Hubs e o nome do centro do evento. Substitua os espaços reservados nos suportes com os valores adequados que obteve ao criar o centro do evento. Certifique-se de que `{Event Hubs namespace connection string}` é a cadeia de ligação ao nível do espaço de nomes e não é a cadeia de hub de eventos. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Substitua o método `Main` pelo método `async Main` seguinte. Consulte os comentários de código para obter detalhes. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Construa o projeto e garanta que não há erros.
6. Executar o programa e esperar pela mensagem de confirmação. 
7. No portal do Azure, você pode verificar se o Hub de eventos recebeu as mensagens. Mude para a vista **Mensagens** na secção **Métricas.** Atualize a página para atualizar o gráfico. Pode levar alguns segundos para que ele mostre que as mensagens foram recebidas. 

    [![Verifique se o centro do evento recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código fonte completo com comentários mais informísticos, consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Receber eventos
Esta secção mostra como escrever uma aplicação de consola .NET Core que recebe mensagens de um hub de eventos usando um processador de eventos. O processador de eventos simplifica a receção de eventos a partir de centros de eventos, gerindo pontos de verificação persistentes e recebeções paralelas desses centros de eventos. Um processador de eventos está associado a um evento específico Hub e a um grupo de consumidores. Recebe eventos de várias divisórias no centro do evento, passando-os a um delegado manipulador para processamento usando código que fornece. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento do Azure e um contêiner de BLOBs
Neste arranque rápido, utiliza o Armazenamento Azure como loja de controlo. Siga estes passos para criar uma conta de Armazenamento Azure. 

1. [Criar uma conta de Armazenamento Azure](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Criar um recipiente de bolhas](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha a cadeia de ligação à conta de armazenamento](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anote a corda de ligação e o nome do recipiente. Você os usará no código de recebimento. 


### <a name="create-a-project-for-the-receiver"></a>Criar um projeto para o recetor

1. Na janela Solution Explorer, clique à direita na solução **EventHubQuickStart,** aponte para **Adicionar**e selecione **New Project**. 
1. Selecione **App consola (.NET Core)** e selecione **Next**. 
1. **Event EventHubsReceiver** para o **nome do Projeto**, e selecione **Criar**. 

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. Selecione **ferramentas** > **NuGet Package Manager** > Consola de Gestor de **Pacotes** do menu. 
1. Executar o seguinte comando para instalar o pacote **NuGet Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Executar o seguinte comando para instalar o pacote **Azure.Messaging.EventHubs.Processor** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Atualizar o método Principal 

1. Adicione as seguintes declarações `using` no topo do ficheiro **Program.cs.**

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Adicione constantes à `Program` classe para a cadeia de ligação Event Hubs e o nome do centro do evento. Substitua os espaços reservados nos suportes com os valores adequados que obteve ao criar o centro do evento. Substitua os espaços reservados nos suportes pelos valores adequados que obteve ao criar o centro do evento e a conta de armazenamento (teclas de acesso - fio de ligação primária). Certifique-se de que `{Event Hubs namespace connection string}` é a cadeia de ligação ao nível do espaço de nomes e não é a cadeia de hub de eventos.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Substitua o método `Main` pelo método `async Main` seguinte. Consulte os comentários de código para obter detalhes. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Agora, adicione os seguintes métodos de manipulador de eventos e erros à classe. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
            return Task.CompletedTask; 
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Construa o projeto e garanta que não há erros.

    > [!NOTE]
    > Para obter o código fonte completo com mais comentários informísticos, consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Executar a aplicação recetora. 
1. Devia ver uma mensagem de que o evento foi recebido. 

    ![Evento recebido](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Estes eventos são os três eventos que enviou para o centro de eventos mais cedo, executando o programa de remetentes. 


## <a name="next-steps"></a>Passos seguintes
Vê as amostras do GitHub. 

- [Amostras de Hubs de Eventos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Amostras de processador de eventos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Amostra de controlo de acesso baseada em funções (RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
