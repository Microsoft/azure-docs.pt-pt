---
title: Usando a biblioteca de .NET de executor em massa para efetuar a importação em massa e atualizar operações no Azure Cosmos DB
description: Em massa importar e atualizar documentos do Azure Cosmos DB com a biblioteca de .NET de executor em massa.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 969821c8b83b8ef554c67f99e3a16e827b53e647
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845125"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Utilize a biblioteca de .NET de executor em massa para realizar operações em massa no Azure Cosmos DB

Este tutorial fornece instruções sobre como usar o executor de em massa do Azure Cosmos DB biblioteca .NET para importar e atualizar documentos para o contentor do Azure Cosmos DB. Para saber mais sobre a biblioteca de executor em massa e como o ajuda a tirar partido do débito em massa e de armazenamento, consulte [descrição geral da biblioteca de executor em massa](bulk-executor-overview.md) artigo. Neste tutorial, irá ver uma aplicação de .NET de exemplo em massa imports aleatoriamente geradas documentos para um contentor do Azure Cosmos DB. Depois de importar, mostra como pode em massa atualizar os dados importados, especificando os patches como operações a serem executadas em campos de documentos específica. 

Atualmente, a biblioteca de executor de em massa é suportada pelo Azure Cosmos DB SQL API e apenas para contas de API do Gremlin. Este artigo descreve como utilizar a biblioteca de .NET de executor em massa com contas da API de SQL. Para saber mais sobre como utilizar a biblioteca de .NET de executor em massa com a API do Gremlin, veja [realizar operações em massa na API do Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Certifique-se de que ativa o desenvolvimento do Azure durante a configuração do Visual Studio.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar. 

* Pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos. Em alternativa, pode utilizar o [emulador do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticating-requests).

* Criar uma conta do Azure Cosmos DB SQL API com os passos descritos em [criar conta de base de dados](create-sql-api-dotnet.md#create-a-database-account) secção do artigo de início rápido do .NET. 

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos trabalhar com o código baixando algumas aplicações de .NET de exemplo do GitHub. Esses aplicativos realizam operações em massa nos dados do Azure Cosmos DB. A clonagem as aplicações, abra uma linha de comandos, navegue para o diretório onde pretende copiar o-los e execute o seguinte comando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

O repositório clonado contém dois exemplos "BulkImportSample" e "BulkUpdateSample." Pode abrir qualquer uma das aplicações de exemplo, Atualize as cadeias de ligação no ficheiro App. config com cadeias de ligação da sua conta do Azure Cosmos DB, crie a solução e executá-lo. 

O aplicativo de "BulkImportSample" gera documentos aleatórios e em massa importa-as ao Azure Cosmos DB. A maior parte de aplicativo de "BulkUpdateSample" atualiza os documentos importados, especificando os patches como operações a serem executadas em campos de documentos específica. Nas secções seguintes, irá rever o código em cada uma destas aplicações de exemplo.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Dados de importação em massa para o Azure Cosmos DB

1. Navegue para a pasta "BulkImportSample" e abra o ficheiro de "BulkImportSample.sln".  

2. Cadeias de ligação do Azure Cosmos DB são obtidas a partir do ficheiro App. config conforme mostrado no seguinte código:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   O importador de em massa cria uma nova base de dados e uma coleção com o nome de base de dados, nome da coleção e os valores de taxa de transferência especificados no ficheiro App. config. 

3. Em seguida, o objeto DocumentClient é inicializado com o modo de ligação de TCP direto:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. O objeto do BulkExecutor é inicializado com uma repetição elevada os valores de tempo de espera e limitado de pedidos. E, em seguida, eles são definidos como 0 para passar o controle de congestionamento para BulkExecutor para seu ciclo de vida.  

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

5. O aplicativo invoca a API de BulkImportAsync. A biblioteca de .NET fornece duas sobrecargas da maior parte importar API - um que aceita uma lista de documentos JSON serializados e a outra aceita uma lista de documentos POCO serialização anuladas. Para saber mais sobre as definições de cada um desses métodos sobrecarregados, consulte [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Método de BulkImportAsync aceita os seguintes parâmetros:**
   
   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |enableUpsert    |   Um sinalizador para ativar upsert dos documentos. Se um documento com id já existe, é atualizado. Por padrão, ele é definido como false.      |
   |disableAutomaticIdGeneration    |    Um sinalizador para desativar a geração automática do ID. Por predefinição, é definido como true.     |
   |maxConcurrencyPerPartitionKeyRange    | O maior grau de simultaneidade por intervalo de chaves de partição, definição para nulo fará com que a biblioteca a utilizar um valor predefinido de 20. |
   |maxInMemorySortingBatchSize     |  O número máximo de documentos obtido a partir do enumerador de documento que é transmitido para a API chame em cada fase.  Para dentro da memória pré-processamento classificação fase antes da importação em massa, definição para nulo fará com que a biblioteca a utilizar o valor predefinido de min (documents.count, 1000000).       |
   |cancellationToken    |    O token de cancelamento para saírem importação em massa.     |

   **Em massa a definição do objeto de resposta de importação** o resultado da importação em massa chamada à API contém os seguintes atributos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  O número total de documentos que foram importadas com êxito sem os documentos fornecidos para a maior parte importar chamada à API.       |
   |TotalRequestUnitsConsumed (double)   |   As unidades de pedido total (RU) consumidas pela maior parte importar chamada à API.      |
   |TotalTimeTaken (TimeSpan)    |   O tempo total que a importação em massa chamada à API para concluir a execução.      |
   |BadInputDocuments (List<object>)   |     A lista de documentos de formato incorreto que não foram importadas com êxito na massa importar chamada à API. Utilizador deve corrigir os documentos devolvidos e repita a importação. Documentos de formato incorreto incluem documentos cujo valor de ID não é uma cadeia de caracteres (nulo ou qualquer outro tipo de dados é considerado inválido).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Dados de atualização em massa no Azure Cosmos DB

Pode atualizar os documentos existentes com a API de BulkUpdateAsync. Neste exemplo, irá definir o campo de nome para um novo valor e remover o campo de descrição dos documentos existentes. Para o conjunto completo de campo suportado operações de atualização, consulte [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Navegue para a pasta "BulkUpdateSample" e abra o ficheiro de "BulkUpdateSample.sln".  

2. Defina os itens de atualização, juntamente com as operações de atualização de campo correspondente. Neste exemplo, irá utilizar SetUpdateOperation para atualizar o campo de nome e UnsetUpdateOperation para remover o campo de descrição de todos os documentos. Pode também executar outras operações, como o incremento um campo de documento por um valor específico, enviar por push valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela atualização em massa API, consulte a [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. O aplicativo invoca a API de BulkUpdateAsync. Para saber mais sobre a definição do método BulkUpdateAsync, consulte [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Método de BulkUpdateAsync aceita os seguintes parâmetros:**

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   O maior grau de simultaneidade por intervalo de chaves de partição, definição para nulo fará com que a biblioteca a utilizar o valor predefinido de 20.   |
   |maxInMemorySortingBatchSize    |    O número máximo de itens de atualização obtido a partir do enumerador de itens de atualização transmitido para a chamada de API em cada fase para classificação de fase pré-processamento na memória antes da atualização em massa, definição para nulo fará com que biblioteca a utilizar o valor predefinido de min (updateItems.count, 1000000).     |
   | cancellationToken|O token de cancelamento para saírem atualização em massa. |

   **Em massa a definição do objeto de resposta de atualização** o resultado da atualização em massa chamada à API contém os seguintes atributos:

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   O número total de documentos que foram atualizadas com êxito dos fornecido para a chamada de API de atualização em massa.      |
   |TotalRequestUnitsConsumed (double)   |    As unidades de total do pedido (RU) consumidos pela atualização em massa, chamada de API.    |
   |TotalTimeTaken (TimeSpan)   | O tempo total que a maior parte chamada à API para concluir a execução de atualização. |
    
## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executor em massa:

* Para obter melhor desempenho, execute a aplicação a partir de uma máquina virtual do Azure que está na mesma região que a sua região de escrita de conta do Cosmos DB.  

* Recomenda-se para criar uma instância de um único objeto BulkExecutor para toda a aplicação dentro de uma única máquina virtual correspondente a um contentor específico do Cosmos DB.  

* Uma vez que uma execução de API de operação em massa única consome uma grande parte de e/s da CPU e da rede de máquina cliente. Isso acontece por gerar várias tarefas internamente, evite ao gerar várias tarefas em simultâneo em seu processo de aplicativo que chama cada API de operação em massa em execução. Se uma chamada de API de operação em massa única em execução numa única máquina virtual não é possível consumir o débito do contentor inteiro (se. o débito > 1 o contentor milhão RU/s), é preferível para criar máquinas virtuais separadas para executar simultaneamente em massa chamadas de operação de API.  

* Certifique-se de que InitializeAsync() é invocado após instanciar um objeto de BulkExecutor para buscar o mapa de partições de contentor do destino do Cosmos DB.  

* Em App. config de seu aplicativo, certifique-se **gcServer** está ativada para um melhor desempenho
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A biblioteca emite rastreios que podem ser coletados num arquivo de log ou na consola. Para ativar ambos, adicione o seguinte ao App. config de seu aplicativo.

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

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre os detalhes do pacote Nuget e notas da biblioteca de .NET de executor em massa de versão, consulte[em massa detalhes sobre o SDK executor](sql-api-sdk-bulk-executor-dot-net.md). 
