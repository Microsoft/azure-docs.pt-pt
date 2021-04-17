---
title: Publique mensagens para e processar mensagens dos tópicos Atlas Kafka da Azure Purview através de Centros de Eventos usando .NET
description: Este artigo fornece uma passagem para criar uma aplicação .NET Core que envia/recebe eventos de/para os tópicos Apache Atlas Kafka da Purview utilizando o mais recente pacote Azure.Messaging.EventHubs.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590438"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Publique mensagens para e processar mensagens dos tópicos Atlas Kafka da Azure Purview através de Centros de Eventos usando .NET 
Este quickstart mostra como enviar eventos e receber eventos dos tópicos Atlas Kafka da Azure Purview através do centro de eventos usando a biblioteca **Azure.Messaging.EventHubs** .NET. 

> [!IMPORTANT]
> Um centro de eventos gerido é criado como parte da criação de conta Purview, ver [criação de conta Purview](create-catalog-portal.md). Pode publicar mensagens para o tema do centro de eventos kafka ATLAS_HOOK e a Purview irá consumi-la e processá-la. A Visão notificará as alterações da entidade ao tópico de kafka do centro de eventos ATLAS_ENTITIES e o utilizador pode consumi-lo e processá-lo. Este quickstart utiliza a nova biblioteca **Azure.Messaging.EventHubs.** 


## <a name="prerequisites"></a>Pré-requisitos
Se você é novo em Azure Event Hubs, consulte o [Event Hubs](../event-hubs/event-hubs-about.md) antes de fazer este quickstart. 

Para completar este arranque rápido, precisa dos seguintes pré-requisitos:

- **Subscrição do Microsoft Azure**. Para utilizar os serviços Azure, incluindo os Azure Event Hubs, precisa de uma subscrição.  Se não tiver uma conta Azure existente, pode inscrever-se para um [teste gratuito](https://azure.microsoft.com/free/) ou utilizar os benefícios do seu assinante MSDN quando [criar uma conta.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. A biblioteca de clientes Azure Event Hubs faz uso de novas funcionalidades que foram introduzidas em C# 8.0.  Ainda pode utilizar a biblioteca com versões linguísticas C# anteriores, mas a nova sintaxe não estará disponível. Para utilizar a sintaxe completa, recomenda-se que compile com a versão [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 ou superior e [a versão linguística](/dotnet/csharp/language-reference/configure-language-version#override-a-default) definida para `latest` . Se estiver a utilizar o Visual Studio, as versões antes do Visual Studio 2019 não são compatíveis com as ferramentas necessárias para construir projetos C# 8.0. O Visual Studio 2019, incluindo a edição comunitária gratuita, pode ser descarregado [aqui.](https://visualstudio.microsoft.com/vs/)

## <a name="publish-messages-to-purview"></a>Publicar mensagens para a Purview 
Esta secção mostra-lhe como criar uma aplicação de consola .NET Core para enviar eventos para um Purview via o tópico kafka do centro **de eventos ATLAS_HOOK**. 

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Em seguida, crie uma aplicação de consola C# .NET no Visual Studio:

1. Lançamento **Visual Studio**.
2. Na janela Iniciar, **selecione Criar uma nova** app de consola de projeto  >  **(.NET Framework)**. É necessária a versão 4.5.2 ou superior do .NET.
3. Em **nome do Projeto,** insira **o PurviewKafkaProducer**.
4. Selecione **Create** (Criar) para criar o projeto.

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Visual Studio 2019. 
1. **Selecione Criar um novo projeto.** 
1. Na caixa de diálogo do **projeto,** faça os seguintes passos: Se não vir esta caixa de diálogo, selecione **Ficheiro** no menu, selecione **Novo**, e, em seguida, selecione **Project**. 
    1. Selecione **C#** para a linguagem de programação.
    1. Selecione **Consola** para o tipo de aplicação. 
    1. Selecione **App de Consola (.NET Core)** da lista de resultados. 
    1. Em seguida, selecione **Seguinte**. 


### <a name="add-the-event-hubs-nuget-package"></a>Adicionar o pacote NuGet dos Hubs de Eventos

1. Selecione **ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola** do menu. 
1. Executar o seguinte comando para instalar o pacote **Azure.Messaging.EventHubs** NuGet e **Azure.Messaging.EventHubs.Producer** NuGet pacote:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever códigos para enviar mensagens ao hub de eventos

1. Adicione as `using` seguintes declarações ao topo do ficheiro **.cs Programa:**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Adicione constantes à `Program` classe para a cadeia de ligação Event Hubs e o nome Do Centro de Eventos. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Você pode obter o espaço de nome do centro de eventos associado à conta de visão, olhando para as cordas de ligação primária/secundária do Atlas kafka no separador propriedades da conta Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Espaço de nome do centro de eventos":::

    O nome do centro do evento deve ser **ATLAS_HOOK** para o envio de mensagens para o Purview.

3. Substitua o `Main` método pelo seguinte método e adicione uma para empurrar as `async Main` `async ProduceMessage` mensagens para o Purview. Consulte os comentários de código para mais detalhes. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Construa o projeto e garanta que não há erros.
6. Executar o programa e aguardar a mensagem de confirmação. 

    > [!NOTE]
    > Para obter o código fonte completo com mais comentários informativos, consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Amostra Criar mensagem JSON da Entidade para criar uma tabela sql com duas colunas.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Consumir mensagens de Purview
Esta secção mostra como escrever uma aplicação de consola .NET Core que recebe mensagens de um centro de eventos utilizando um processador de eventos. Você precisa usar ATLAS_ENTITIES centro de eventos para receber mensagens de Purview.O processador do evento simplifica receber eventos de centros de eventos, gerindo pontos de verificação persistentes e receções paralelas desses centros de eventos. 

> [!WARNING]
> Se executar este código no Azure Stack Hub, sofrerá erros de tempo de execução a menos que tenha como alvo uma versão API de armazenamento específica. Isto porque o Event Hubs SDK utiliza a mais recente API de Armazenamento Azure disponível disponível no Azure que pode não estar disponível na sua plataforma Azure Stack Hub. O Azure Stack Hub pode suportar uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure. Se estiver a utilizar o Azure Blob Storage como uma loja de ponto de verificação, verifique a [versão API suportada para o seu Azure Stack Hub e](/azure-stack/user/azure-stack-acs-differences?#api-version) direcione essa versão no seu código. 
>
> Por exemplo, Se estiver a executar a versão Azure Stack Hub 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes Event Hubs SDK utiliza a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Neste caso, além de seguir os passos nesta secção, também terá de adicionar código para direcionar o serviço de armazenamento API versão 2019-02-02. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte [esta amostra no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um armazenamento Azure e um recipiente blob
Neste arranque rápido, utiliza-se o Azure Storage como loja de pontos de verificação. Siga estes passos para criar uma conta de Armazenamento Azure. 

1. [Criar uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contentor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha o fio de ligação para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Observe o fio de ligação e o nome do recipiente. Vai usá-los no código de receção. 


### <a name="create-a-project-for-the-receiver"></a>Criar um projeto para o recetor

1. Na janela Solution Explorer, clique à direita na solução **EventHubQuickStart,** aponte para **Adicionar,** e selecione **Novo Projeto**. 
1. Selecione **App de Consola (.NET Core)** e selecione **Seguinte**. 
1. **Insira o PurviewKafkaConsumer** para o **nome do projeto** e selecione **Criar**. 

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

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa.**

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
    
    Você pode obter o espaço de nome do centro de eventos associado à conta de visão, olhando para as cordas de ligação primária/secundária do Atlas kafka no separador propriedades da conta Purview.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Espaço de nome do centro de eventos":::

    O nome do centro do evento deve ser **ATLAS_ENTITIES** para o envio de mensagens para o Purview.

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
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

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
    > Para obter o código fonte completo com mais comentários informativos, consulte [este ficheiro no GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Executar a aplicação do recetor. 

### <a name="sample-message-received-from-purview"></a>Mensagem de amostra recebida da Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atualmente, o Atlas suporta os seguintes tipos de operação: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2,** **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. O empurrar mensagens para o Purview está atualmente ativado por padrão. Se o cenário envolver a leitura da Purview contacte-nos, pois tem de ser permitido. (fornecer id de assinatura e nome da conta Purview).


## <a name="next-steps"></a>Passos seguintes
Vê as amostras do GitHub. 

- [Amostras de Centros de Eventos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Amostras de processador de eventos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Introdução do Atlas às notificações](https://atlas.apache.org/2.0.0/Notifications.html)