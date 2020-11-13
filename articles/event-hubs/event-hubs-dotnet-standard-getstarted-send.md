---
title: Enviar ou receber eventos de Azure Event Hubs usando .NET (mais recente)
description: Este artigo fornece uma passagem para criar uma aplicação .NET Core que envia/recebe eventos de/a partir de Azure Event Hubs usando o mais recente pacote Azure.Messaging.EventHubs.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c0f5a5fb45ee45cb9b7e399dc39ad29406b15dc
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578999"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Enviar eventos para e receber eventos dos Azure Event Hubs - .NET (Azure.Messaging.EventHubs) 
Este quickstart mostra como enviar eventos e receber eventos de um centro de eventos usando a biblioteca **Azure.Messaging.EventHubs** .NET. 

> [!IMPORTANT]
> Este quickstart utiliza a nova biblioteca **Azure.Messaging.EventHubs.** Para obter um arranque rápido que utilize a antiga biblioteca **Microsoft.Azure.EventHubs,** consulte [Enviar e receber eventos utilizando a biblioteca Microsoft.Azure.EventHubs](event-hubs-dotnet-standard-get-started-send-legacy.md). 



## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte o [Event Hubs](event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. A biblioteca de clientes Azure Event Hubs faz uso de novas funcionalidades que foram introduzidas em C# 8.0.  Ainda pode utilizar a biblioteca com versões linguísticas C# anteriores, mas a nova sintaxe não estará disponível. Para utilizar a sintaxe completa, recomenda-se que compile com a versão [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 ou superior e [a versão linguística](/dotnet/csharp/language-reference/configure-language-version#override-a-default) definida para `latest` . Se estiver a utilizar o Visual Studio, as versões antes do Visual Studio 2019 não são compatíveis com as ferramentas necessárias para construir projetos C# 8.0. O Visual Studio 2019, incluindo a edição comunitária gratuita, pode ser descarregado [aqui.](https://visualstudio.microsoft.com/vs/)
- **Crie um espaço de nomes de Centros de Eventos e um centro de eventos.** O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o hub de eventos. Para criar um espaço de nome e um centro de eventos, siga o procedimento [neste artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de ligação para o espaço de nomes Do Event Hubs** seguindo as instruções do artigo: Obter a cadeia de [ligação](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Utilize a cadeia de ligação mais tarde neste arranque rápido.

## <a name="send-events"></a>Enviar eventos 
Esta secção mostra-lhe como criar uma aplicação de consola .NET Core para enviar eventos para um centro de eventos. 

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Visual Studio 2019. 
1. **Selecione Criar um novo projeto.** 
1. Na caixa de diálogo do **projeto,** faça os seguintes passos: Se não vir esta caixa de diálogo, selecione **Ficheiro** no menu, selecione **Novo** , e, em seguida, selecione **Project**. 
    1. Selecione **C#** para a linguagem de programação.
    1. Selecione **Consola** para o tipo de aplicação. 
    1. Selecione **App de Consola (.NET Core)** da lista de resultados. 
    1. Em seguida, selecione **Seguinte**. 

        ![Caixa de diálogo Novo Projeto](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. **Insira o EventHubsSender** para o nome do projeto, **EventHubsQuickStart** para o nome da solução e, em seguida, selecione **OK** para criar o projeto. 

    ![C# > App consola](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. Selecione **ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola** do menu. 
1. Executar o seguinte comando para instalar o pacote **Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

1. Adicione as `using` seguintes declarações ao topo do ficheiro **Program.cs:**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adicione constantes à `Program` classe para a cadeia de ligação Event Hubs e o nome do centro do evento. Substitua os espaços reservados nos parênteses pelos valores adequados que obteve ao criar o centro de eventos. Certifique-se de que `{Event Hubs namespace connection string}` é a cadeia de ligação ao nível do espaço de nomes e não é a cadeia de hub de eventos. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Substitua o `Main` método pelo seguinte `async Main` método. Consulte os comentários de código para mais detalhes. 

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
6. Executar o programa e aguardar a mensagem de confirmação. 
7. No portal Azure, pode verificar se o centro de eventos recebeu as mensagens. Ver **mensagens** na secção **Métricas.** Refresque a página para atualizar o gráfico. Pode levar alguns segundos para mostrar que as mensagens foram recebidas. 

    [![Verifique se o centro de eventos recebeu as mensagens](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Para obter o código fonte completo com mais comentários informativos, consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>Receber eventos
Esta secção mostra como escrever uma aplicação de consola .NET Core que recebe mensagens de um centro de eventos utilizando um processador de eventos. O processador do evento simplifica a receção de eventos a partir de centros de eventos, gerindo pontos de verificação persistentes e receções paralelas desses centros de eventos. Um processador de eventos está associado a um hub de evento específico e a um grupo de consumidores. Recebe eventos de múltiplas divisórias no centro do evento, passando-as a um delegado de manipulador para processamento usando o código que fornece. 


> [!WARNING]
> Se executar este código no Azure Stack Hub, sofrerá erros de tempo de execução a menos que tenha como alvo uma versão API de armazenamento específica. Isto porque o Event Hubs SDK utiliza a mais recente API de Armazenamento Azure disponível disponível no Azure que pode não estar disponível na sua plataforma Azure Stack Hub. O Azure Stack Hub pode suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Se estiver a utilizar o Azure Blog Storage como uma loja de checkpoint, verifique a [versão API suportada do Azure Storage para a sua construção do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e direcione essa versão no seu código. 
>
> Por exemplo, Se estiver a executar a versão Azure Stack Hub 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes Event Hubs SDK utiliza a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento API versão 2019-02-02. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte [esta amostra no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento Azure e um recipiente blob
Neste arranque rápido, utiliza-se o Azure Storage como loja de pontos de verificação. Siga estes passos para criar uma conta de Armazenamento Azure. 

1. [Criar uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contentor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha o fio de ligação para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Observe o fio de ligação e o nome do recipiente. Vai usá-los no código de receção. 


### <a name="create-a-project-for-the-receiver"></a>Criar um projeto para o recetor

1. Na janela Solution Explorer, clique à direita na solução **EventHubQuickStart,** aponte para **Adicionar,** e selecione **Novo Projeto**. 
1. Selecione **App de Consola (.NET Core)** e selecione **Seguinte**. 
1. **Insira o EventHubsReceiver** para o **nome do projeto** e selecione **Create**. 

### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. Selecione **ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola** do menu. 
1. Executar o seguinte comando para instalar o pacote **Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Executar o seguinte comando para instalar o pacote **Azure.Messaging.EventHubs.Processor** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Atualizar o método principal 

1. Adicione as `using` seguintes declarações no topo do ficheiro **Program.cs.**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Adicione constantes à `Program` classe para a cadeia de ligação Event Hubs e o nome do centro do evento. Substitua os espaços reservados nos parênteses pelos valores adequados que obteve ao criar o centro de eventos. Substitua os espaços reservados nos suportes pelos valores adequados que obteve ao criar o centro de eventos e a conta de armazenamento (teclas de acesso - cadeia de ligação primária). Certifique-se de que `{Event Hubs namespace connection string}` é a cadeia de ligação ao nível do espaço de nomes e não é a cadeia de hub de eventos.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Substitua o `Main` método pelo seguinte `async Main` método. Consulte os comentários de código para mais detalhes. 

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
1. Agora, adicione os seguintes métodos de manipulação de eventos e erros à classe. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
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
    > Para obter o código fonte completo com mais comentários informativos, consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Executar a aplicação do recetor. 
1. Deve ver uma mensagem de que o evento foi recebido. 

    ![Evento recebido](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Estes eventos são os três eventos que enviou para o centro de eventos mais cedo, executando o programa remetente. 


## <a name="next-steps"></a>Passos seguintes
Vê as amostras do GitHub. 

- [Amostras de Centros de Eventos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Amostras de processador de eventos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Amostra de controlo de acesso baseado em função Azure (Azure RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
