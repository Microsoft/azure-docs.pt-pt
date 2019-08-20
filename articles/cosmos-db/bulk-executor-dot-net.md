---
title: Usando a biblioteca .NET do executor em massa para executar operações de importação e atualização em massa no Azure Cosmos DB
description: Importe e atualize em massa Azure Cosmos DB documentos usando a biblioteca .NET do executor em massa.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 0b74c7b178ee4512067de4b8decba0c3c565ccd4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616973"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Use a biblioteca .NET do executor em massa para executar operações em massa no Azure Cosmos DB

Este tutorial fornece instruções sobre como usar a biblioteca .NET do executor em massa do Azure Cosmos DB para importar e atualizar documentos para o contêiner Cosmos do Azure. Para saber mais sobre a biblioteca de executores em massa e como ela ajuda a aproveitar o armazenamento e a produtividade maciça, consulte o artigo [visão geral da biblioteca de executores em massa](bulk-executor-overview.md) Neste tutorial, você verá um aplicativo .NET de exemplo que importa em massa documentos gerados aleatoriamente em um contêiner Cosmos do Azure. Após a importação, ele mostra como você pode atualizar os dados importados em massa especificando patches como operações a serem executadas em campos de documento específicos. 

Atualmente, a biblioteca de executor de em massa é suportada pelo Azure Cosmos DB SQL API e apenas para contas de API do Gremlin. Este artigo descreve como usar a biblioteca .NET do executor em massa com contas da API do SQL. Para saber mais sobre como utilizar a biblioteca de .NET de executor em massa com a API do Gremlin, veja [realizar operações em massa na API do Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Se você ainda não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Certifique-se de habilitar o desenvolvimento do Azure durante a instalação do Visual Studio.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar. 

* Pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos. Ou, você pode usar o [emulador](https://docs.microsoft.com/azure/cosmos-db/local-emulator) de Azure Cosmos DB `https://localhost:8081` com o ponto de extremidade. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticating-requests).

* Crie uma conta da API do SQL Azure Cosmos DB usando as etapas descritas na seção [criar conta do banco de dados](create-sql-api-dotnet.md#create-account) do artigo início rápido do .net. 

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos mudar para trabalhar com código baixando alguns aplicativos .NET de exemplo do GitHub. Esses aplicativos executam operações em massa em Azure Cosmos DB dados. Para clonar os aplicativos, abra um prompt de comando, navegue até o diretório em que você deseja copiá-los e execute o seguinte comando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

O repositório clonado contém dois exemplos "BulkImportSample" e "BulkUpdateSample". Você pode abrir qualquer um dos aplicativos de exemplo, atualizar as cadeias de conexão no arquivo app. config com as cadeias de conexão de sua conta Azure Cosmos DB, compilar a solução e executá-la. 

O aplicativo "BulkImportSample" gera documentos aleatórios e os importa em massa para Azure Cosmos DB. O aplicativo "BulkUpdateSample" atualiza em massa os documentos importados especificando patches como operações a serem executadas em campos de documento específicos. Nas próximas seções, você examinará o código em cada um desses aplicativos de exemplo.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Dados de importação em massa para o Azure Cosmos DB

1. Navegue até a pasta "BulkImportSample" e abra o arquivo "BulkImportSample. sln".  

2. As cadeias de conexão do Azure Cosmos DB são recuperadas do arquivo app. config, conforme mostrado no código a seguir:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   O importador de em massa cria uma nova base de dados e uma coleção com o nome de base de dados, nome da coleção e os valores de taxa de transferência especificados no ficheiro App. config. 

3. Em seguida, o objeto DocumentClient é inicializado com o modo de conexão TCP direta:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. O objeto BulkExecutor é inicializado com um valor de repetição alto para o tempo de espera e solicitações limitadas. E, em seguida, eles são definidos como 0 para passar o controle de congestionamento para BulkExecutor durante seu tempo de vida.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. O aplicativo invoca a API BulkImportAsync. A biblioteca .NET fornece duas sobrecargas da API de importação em massa-uma que aceita uma lista de documentos JSON serializados e a outra aceita uma lista de documentos POCO desserializados. Para saber mais sobre as definições de cada um desses métodos sobrecarregados, consulte a [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **O método BulkImportAsync aceita os seguintes parâmetros:**
   
   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |enableUpsert    |   Um sinalizador para habilitar upserts dos documentos. Se um documento com ID já existe, é atualizado. Por padrão, ele é definido como false.      |
   |disableAutomaticIdGeneration    |    Um sinalizador para desativar a geração automática do ID. Por predefinição, é definido como true.     |
   |maxConcurrencyPerPartitionKeyRange    | O grau máximo de simultaneidade por intervalo de chave de partição, definindo como NULL fará com que a biblioteca use um valor padrão de 20. |
   |maxInMemorySortingBatchSize     |  O número máximo de documentos extraídos do enumerador de documento que é passado para a chamada à API em cada estágio.  Para a fase de classificação de pré-processamento na memória antes da importação em massa, a configuração para NULL fará com que a biblioteca use o valor padrão de min (Documents. Count, 1 milhão).       |
   |cancellationToken    |    O token de cancelamento para sair normalmente da importação em massa.     |

   **Definição de objeto de resposta de importação em massa** O resultado da chamada à API de importação em massa contém os seguintes atributos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |NumberOfDocumentsImported (longo)   |  O número total de documentos que foram importadas com êxito sem os documentos fornecidos para a maior parte importar chamada à API.       |
   |TotalRequestUnitsConsumed (duplo)   |   As unidades de pedido total (RU) consumidas pela maior parte importar chamada à API.      |
   |TotalTimeTaken (TimeSpan)    |   O tempo total que a importação em massa chamada à API para concluir a execução.      |
   |BadInputDocuments (>\<de objeto de lista)   |     A lista de documentos de formato incorreto que não foram importadas com êxito na massa importar chamada à API. Utilizador deve corrigir os documentos devolvidos e repita a importação. Documentos de formato incorreto incluem documentos cujo valor de ID não é uma cadeia de caracteres (nulo ou qualquer outro tipo de dados é considerado inválido).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Dados de atualização em massa no Azure Cosmos DB

Pode atualizar os documentos existentes com a API de BulkUpdateAsync. Neste exemplo, irá definir o campo de nome para um novo valor e remover o campo de descrição dos documentos existentes. Para obter o conjunto completo de operações de atualização de campo com suporte, consulte a [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Navegue até a pasta "BulkUpdateSample" e abra o arquivo "BulkUpdateSample. sln".  

2. Defina os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, irá utilizar SetUpdateOperation para atualizar o campo de nome e UnsetUpdateOperation para remover o campo de descrição de todos os documentos. Pode também executar outras operações, como o incremento um campo de documento por um valor específico, enviar por push valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela API de atualização em massa, consulte a [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. O aplicativo invoca a API BulkUpdateAsync. Para saber mais sobre a definição do método BulkUpdateAsync, consulte a [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **O método BulkUpdateAsync aceita os seguintes parâmetros:**

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   O grau máximo de simultaneidade por intervalo de chave de partição, definindo como NULL fará com que a biblioteca use o valor padrão de 20.   |
   |maxInMemorySortingBatchSize    |    O número máximo de itens de atualização extraídos do enumerador de itens de atualização passado para a chamada à API em cada estágio para a fase de classificação de pré-processamento na memória antes da atualização em massa, definindo como NULL fará com que a biblioteca use o valor padrão de min (updateItems. Count, 1 milhão).     |
   | cancellationToken|O token de cancelamento para sair normalmente da atualização em massa. |

   **Definição de objeto de resposta de atualização em massa** O resultado da chamada à API de atualização em massa contém os seguintes atributos:

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   O número total de documentos que foram atualizados com êxito dos fornecidos para a chamada à API de atualização em massa.      |
   |TotalRequestUnitsConsumed (duplo)   |    As unidades de total do pedido (RU) consumidos pela atualização em massa, chamada de API.    |
   |TotalTimeTaken (TimeSpan)   | O tempo total que a maior parte chamada à API para concluir a execução de atualização. |
    
## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executor em massa:

* Para obter o melhor desempenho, execute o aplicativo de uma máquina virtual do Azure que esteja na mesma região que sua região de gravação de Cosmos DB conta.  

* É recomendável criar uma instância de um único objeto BulkExecutor para todo o aplicativo em uma única máquina virtual correspondente a um contêiner Cosmos específico.  

* Uma vez que uma execução de API de operação em massa única consome uma grande parte de e/s da CPU e da rede de máquina cliente. Isso acontece por gerar várias tarefas internamente, evite ao gerar várias tarefas em simultâneo em seu processo de aplicativo que chama cada API de operação em massa em execução. Se uma única chamada à API de operação em massa que está em execução em uma única máquina virtual não puder consumir a taxa de transferência do contêiner inteiro (se a taxa de transferência do contêiner > 1 milhão RU/s), é preferível criar máquinas virtuais separadas para execução simultânea chamadas de API de operação em massa.  

* Certifique-se de que InitializeAsync () seja invocado após instanciar um objeto BulkExecutor para buscar o mapa de partição do contêiner de destino Cosmos.  

* No app. config do seu aplicativo, verifique se o **gcServer** está habilitado para melhorar o desempenho
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A biblioteca emite rastreamentos que podem ser coletados em um arquivo de log ou no console do. Para habilitar ambos, adicione o seguinte ao app. config do seu aplicativo.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os detalhes do pacote NuGet e notas de versão da biblioteca .NET do executor em massa, consulte[detalhes do SDK do executor em massa](sql-api-sdk-bulk-executor-dot-net.md). 
