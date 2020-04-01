---
title: Utilize biblioteca de executor a granel .NET em Azure Cosmos DB para operações de importação e atualização a granel
description: Importar e atualizar os documentos Do Azure Cosmos DB utilizando a biblioteca do executor a granel .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 40ef05107f20a3396f6710f894a2dbad2d7fa6c9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478843"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Utilize a biblioteca executor a granel .NET para realizar operações a granel em Azure Cosmos DB

> [!NOTE]
> Esta biblioteca de executor a granel descrita neste artigo é mantida para aplicações utilizando a versão .NET SDK 2.x. Para novas aplicações, pode utilizar o suporte a **granel** que está diretamente disponível com a [versão 3.x .NET SDK](tutorial-sql-api-dotnet-bulk-import.md) e não requer qualquer biblioteca externa. 

> Se está a utilizar a biblioteca de executora a granel e planeia migrar para suporte a granel no novo SDK, utilize os passos no [guia migratório](how-to-migrate-from-bulk-executor-library.md) para migrar a sua aplicação.

Este tutorial fornece instruções sobre a utilização da biblioteca do executor a granel .NET para importar e atualizar documentos para um contentor Azure Cosmos. Para saber sobre a biblioteca de executores a granel e como ajuda a alavancar a entrada e armazenamento maciços, consulte o artigo de visão geral da [biblioteca de executores a granel.](bulk-executor-overview.md) Neste tutorial, você verá uma aplicação de amostra .NET que importa a granel documentos gerados aleatoriamente em um recipiente Azure Cosmos. Após a importação, mostra como pode atualizar em massa os dados importados especificando patches como operações a executar em campos de documentos específicos.

Atualmente, a biblioteca de executora a granel é apoiada apenas pelas contas Azure Cosmos DB SQL API e Gremlin API. Este artigo descreve como usar a biblioteca do executor a granel .NET com contas SQL API. Para aprender sobre a utilização da biblioteca do executor a granel .NET com contas DaAGremlin, consulte a realização de operações a [granel na API Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Certifique-se de que ativa o "desenvolvimento Do Azure" durante a configuração do Estúdio Visual.

* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Você pode [Experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. Ou, pode usar o [emulador Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticating-requests).

* Crie uma conta API Azure Cosmos DB SQL utilizando os passos descritos na secção de [conta de base](create-sql-api-dotnet.md#create-account) de dados do artigo .NET quickstart.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos mudar para trabalhar com código, baixando uma aplicação de amostra .NET do GitHub. Esta aplicação realiza operações a granel nos dados armazenados na sua conta Azure Cosmos. Para clonar a aplicação, abra um pedido de comando, navegue para o diretório onde pretende copiá-la e executar o seguinte comando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

O repositório clonado contém duas amostras "BulkImportSample" e "BulkUpdateSample". Pode abrir qualquer uma das aplicações da amostra, atualizar as cordas de ligação no ficheiro App.config com as cordas de ligação da sua conta Azure Cosmos DB, construir a solução e executá-la.

A aplicação "BulkImportSample" gera documentos aleatórios e importa-os a granel para a sua conta Azure Cosmos. A aplicação "BulkUpdateSample" atualiza os documentos importados especificando patches como operações a executar em campos de documentos específicos. Nas próximas secções, irá rever o código em cada uma destas aplicações de amostra.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Dados de importação a granel para uma conta Azure Cosmos

1. Navegue para a pasta "GranelImportSample" e abra o ficheiro "BulkImportSample.sln".  

2. As cordas de ligação do Azure Cosmos DB são recuperadas do ficheiro App.config, conforme mostrado no seguinte código:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   O importador a granel cria uma nova base de dados e um recipiente com o nome da base de dados, o nome do contentor e os valores de entrada especificados no ficheiro App.config.

3. Em seguida, o objeto DocumentClient é rubricado com o modo de ligação TCP direto:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. O objeto BulkExecuteor é inicializado com um elevado valor de repetição para o tempo de espera e pedidos acelerados. E então eles estão definidos para 0 para passar o controlo de congestionamento para BulkExecutor para a sua vida.  

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

5. O pedido invoca a API BulkImportAsync. A biblioteca .NET fornece duas sobrecargas da API de importação a granel - uma que aceita uma lista de documentos JSON serializados e a outra que aceita uma lista de documentos POCO desserializados. Para saber mais sobre as definições de cada um destes métodos sobrecarregados, consulte a documentação da [API.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet)

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
   |enableUpsert    |   Uma bandeira para permitir operações upsert nos documentos. Se um documento com a identificação dada já existir, é atualizado. Por defeito, está definido como falso.      |
   |desativarAutomaticIdGeneration    |    Uma bandeira para desativar a geração automática de identificação. Por defeito, está definido para verdade.     |
   |maxConcurrencyPerPartitionKeyRange    | O grau máximo de conmoeda por divisória, definição para nulo fará com que a biblioteca utilize um valor padrão de 20. |
   |maxInMemorySortingBatchSize     |  O número máximo de documentos que são retirados do enumerador de documentos, que é passado para a chamada da API em cada fase. Para a fase de triagem em memória que ocorre antes da importação a granel, a fixação deste parâmetro a nula fará com que a biblioteca utilize o valor mínimo predefinido (documentos.count, 10000000).       |
   |cancelamentoToken    |    O símbolo de cancelamento para sair graciosamente da operação de importação a granel.     |

   **Definição de objeto de resposta à importação a granel** O resultado da chamada API importada a granel contém os seguintes atributos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |NumberOfDocumentsImportados (longo)   |  O número total de documentos importados com êxito do total dos documentos fornecidos à chamada a granel.       |
   |TotalRequestUnitsConsumido (duplo)   |   As unidades de pedido totais (RU) consumidas pela chamada API de importação a granel.      |
   |Total timetaken (Timespan)    |   O tempo total deato na chamada a granel da API para completar a execução.      |
   |BadInputDocuments (> de objeto de lista)\<   |     A lista de documentos em mau formato que não foram importados com êxito na chamada API de importação a granel. Corrija os documentos devolvidos e volte a tentar a importação. Os documentos mal formados incluem documentos cujo valor de id não é uma cadeia (nulo ou qualquer outro tipo de dados é considerado inválido).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Dados de atualização a granel na sua conta Azure Cosmos

Pode atualizar os documentos existentes utilizando a API BulkUpdateAsync. Neste exemplo, irá definir `Name` o campo para um `Description` novo valor e remover o campo dos documentos existentes. Para o conjunto completo de operações de atualização suportadas, consulte a documentação da [API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Navegue na pasta "BulkUpdateSample" e abra o ficheiro "BulkUpdateSample.sln".  

2. Defina os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, utilizará `SetUpdateOperation` para `Name` atualizar `UnsetUpdateOperation` o campo `Description` e remover o campo de todos os documentos. Também pode realizar outras operações como incrementar um campo de documentos por um valor específico, empurrar valores específicos para um campo de matriz, ou remover um valor específico de um campo de matriz. Para conhecer os diferentes métodos fornecidos pela atualização a granel API, consulte a documentação da [API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. A aplicação invoca a API BulkUpdateAsync. Para conhecer a definição do método BulkUpdateAsync, consulte a documentação da [API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

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
   |maxConcurrencyPerPartitionKeyRange    |   O grau máximo de conmoeda por divisória, definindo este parâmetro como nulo, fará com que a biblioteca utilize o valor predefinido(20).   |
   |maxInMemorySortingBatchSize    |    O número máximo de itens de atualização retirados do enumerador de itens de atualização passou para a chamada da API em cada fase. Para a fase de triagem em memória que ocorre antes da atualização a granel, a definição deste parâmetro para nula fará com que a biblioteca utilize o valor mínimo predefinido (actualizaçãoItems.count, 10000000).     |
   | cancelamentoToken|O símbolo de cancelamento para sair graciosamente da operação de atualização a granel. |

   **Definição** de objeto de resposta a atualização a granel O resultado da chamada API a granel contém os seguintes atributos:

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |NumberOfDocumentsAtualizado (longo)    |   O número de documentos que foram atualizados com sucesso a partir do total de documentos fornecidos à chamada a granel da API.      |
   |TotalRequestUnitsConsumido (duplo)   |    As unidades de pedido totais (RUs) consumidas pela chamada API de atualização a granel.    |
   |Total timetaken (Timespan)   | O tempo total deatualização a granel da Chamada API para completar a execução. |
    
## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executora a granel:

* Para melhor desempenho, execute a sua aplicação a partir de uma máquina virtual Azure que esteja na mesma região que a região de escrita da sua conta Azure Cosmos.  

* Recomenda-se que você instantaneamente um único `BulkExecutor` objeto para toda a aplicação dentro de uma única máquina virtual que corresponda a um recipiente Azure Cosmos específico.  

* Uma vez que uma única operação a granel a execução da API consome uma grande parte do CPU e da rede IO da máquina cliente (isto acontece desogerando várias tarefas internamente). Evite desovar várias tarefas simultâneas no âmbito do seu processo de aplicação que executam chamadas API da operação a granel. Se uma única chamada a granel da API que está a funcionar numa única máquina virtual não conseguir consumir a entrada de todo o recipiente (se a entrada do seu contentor > 1 milhão de RU/s), é preferível criar máquinas virtuais separadas para executar simultaneamente as chamadas API de operação a granel.  

* Certifique-se de que o `InitializeAsync()` método é invocado após instantaneamente um objeto BulkExecuteor para obter o mapa de partição do contentor cosmos alvo.  

* Na App.Config da sua aplicação, certifique-se de que o **gcServer** está habilitado para um melhor desempenho
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A biblioteca emite vestígios que podem ser recolhidos num ficheiro de registo ou na consola. Para ativar ambos, adicione o seguinte código ao ficheiro App.Config da sua aplicação.

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

* Para saber mais sobre os detalhes do pacote Nuget e as notas de lançamento, consulte os [detalhes do SDK](sql-api-sdk-bulk-executor-dot-net.md)do executor a granel .
