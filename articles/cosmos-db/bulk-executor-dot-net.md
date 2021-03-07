---
title: Utilize o executor a granel .net library in Azure Cosmos DB para operações de importação e atualização a granel
description: Importar a granel e atualizar os documentos DB do Azure Cosmos utilizando a biblioteca do executor a granel .NET.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429821"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Utilize o executor a granel .NET library para realizar operações a granel em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Esta biblioteca de executor a granel descrita neste artigo é mantida para aplicações utilizando a versão .NET SDK 2.x. Para novas aplicações, pode utilizar o **suporte a granel** que está disponível diretamente com a versão [.NET SDK 3.x](tutorial-sql-api-dotnet-bulk-import.md) e não necessita de qualquer biblioteca externa. 

> Se estiver a utilizar a biblioteca de executor a granel e planeia migrar para suporte em massa no SDK mais recente, utilize os passos no [guia migratório](how-to-migrate-from-bulk-executor-library.md) para migrar a sua aplicação.

Este tutorial apresenta instruções sobre a utilização da biblioteca .NET do executor em massa para importar e atualizar documentos para um contentor do Azure Cosmos. Para saber mais sobre a biblioteca do executor a granel e como o ajuda a alavancar a produção e armazenamento maciços, consulte o artigo da biblioteca de [executor a granel.](bulk-executor-overview.md) Neste tutorial, você verá uma aplicação de amostra .NET que importa documentos gerados aleatoriamente em um recipiente Azure Cosmos. Depois de importar, mostra como pode atualizar em massa os dados importados especificando patches como operações para executar em campos de documento específicos.

Atualmente, a biblioteca de executores a granel é suportada apenas pelas contas API API EPI API da AZure Cosmos E a API. Este artigo descreve como utilizar a biblioteca do executor a granel .NET com contas API SQL. Para saber mais sobre a utilização da biblioteca a granel .NET com contas da Gremlin API, consulte [realizar operações a granel na API da Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a [Edição Comunitária do Visual Studio 2019.](https://www.visualstudio.com/downloads/) Certifique-se de que ativa o "Desenvolvimento Azure" durante a configuração do Estúdio Visual.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Você pode [experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. Ou pode usar o [Emulador Azure Cosmos DB](./local-emulator.md) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticate-requests).

* Crie uma conta Azure Cosmos DB SQL API utilizando os passos descritos na secção de conta de base de [dados](create-sql-api-dotnet.md#create-account) do artigo de arranque rápido .NET.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos mudar para trabalhar com código, descarregando uma aplicação de amostra .NET do GitHub. Esta aplicação realiza operações a granel nos dados armazenados na sua conta Azure Cosmos. Para clonar a aplicação, abra um pedido de comando, navegue para o diretório onde pretende copiá-lo e executar o seguinte comando:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

O repositório clonado contém duas amostras "BulkImportSample" e "BulkUpdateSample". Pode abrir qualquer uma das aplicações da amostra, atualizar as cadeias de ligação no ficheiro App.config com as cordas de ligação da sua conta Azure Cosmos, construir a solução e executá-la.

A aplicação "BulkImportSample" gera documentos aleatórios e importa-os a granel para a sua conta Azure Cosmos. A aplicação "BulkUpdateSample" atualiza os documentos importados especificando os patches como operações a realizar em campos de documento específicos. Nas próximas secções, irá rever o código em cada uma destas aplicações de amostra.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Dados de importação a granel para uma conta da Azure Cosmos

1. Navegue na pasta "BulkImportSample" e abra o ficheiro "BulkImportSample.sln".  

2. As cordas de ligação do Azure Cosmos DB são recuperadas a partir do ficheiro App.config, tal como mostrado no seguinte código:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   O importador a granel cria uma nova base de dados e um contentor com o nome da base de dados, o nome do contentor e os valores de produção especificados no ficheiro App.config.

3. Em seguida, o objeto DocumentClient é inicializado com o modo de ligação TCP direto:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. O objeto BulkExecutor é inicializado com um elevado valor de retagem para o tempo de espera e pedidos acelerados. E então eles estão definidos para 0 para passar o controlo de congestionamento para BulkExecutor para a sua vida.  

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

5. O pedido invoca a API BulkImportAsync. A biblioteca .NET fornece duas sobrecargas da API importadora a granel - uma que aceita uma lista de documentos JSON serializados e a outra que aceita uma lista de documentos POCO deserizados. Para saber mais sobre as definições de cada um destes métodos sobrecarregados, consulte a documentação da [API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync).

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
   |enableUpsert    |   Uma bandeira para permitir operações de upsert nos documentos. Se um documento com o ID já existe, é atualizado. Por padrão, é definido como falso.      |
   |desativação AutomaticIdGeneration    |    Uma bandeira para desativar a geração automática de identificação. Por defeito, é definido como verdadeiro.     |
   |maxConcurrencyPerPartitionKeyRange    | O grau máximo de concordância por intervalo de teclas de partição, configuração a nulo fará com que a biblioteca utilize um valor predefinido de 20. |
   |maxInMemorySortingBatchSize     |  O número máximo de documentos que são retirados do enumerador de documentos, que é passado para a chamada da API em cada fase. Para a fase de triagem na memória que acontece antes da importação a granel, a definição deste parâmetro para nulo fará com que a biblioteca utilize o valor mínimo predefinido (documentos.contagem, 1000000).       |
   |cancelamentoToken    |    O sinal de cancelamento para sair graciosamente da operação de importação a granel.     |

   **Definição de objeto de resposta a granel** O resultado da chamada API de importação a granel contém os seguintes atributos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |NúmeroOfDocumentsImported (longo)   |  O número total de documentos que foram importados com êxito do total dos documentos fornecidos à chamada API de importação a granel.       |
   |TotalRequestUnitsConsumed (duplo)   |   As unidades de pedido total (RU) consumidas pela chamada API de importação a granel.      |
   |TotalTimeTaken (TimeSpan)    |   O tempo total desosseido pela API de importação a granel para concluir a execução.      |
   |BadInputDocuments \<object> (Lista)   |     A lista de documentos de mau formato que não foram importados com êxito na chamada de API de importação a granel. Corrija os documentos devolvidos e redagre a importação. Os documentos mal formatados incluem documentos cujo valor de ID não é uma cadeia (nulo ou qualquer outro tipo de dados é considerado inválido).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Dados de atualização em massa na sua conta Azure Cosmos

Pode atualizar os documentos existentes utilizando a API BulkUpdateAsync. Neste exemplo, irá definir o `Name` campo para um novo valor e remover o campo dos `Description` documentos existentes. Para o conjunto completo de operações de atualização apoiadas, consulte a documentação da [API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

1. Navegue na pasta "BulkUpdateSample" e abra o ficheiro "BulkUpdateSample.sln".  

2. Defina os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, utilizará `SetUpdateOperation` para atualizar o campo e remover o campo de todos os `Name` `UnsetUpdateOperation` `Description` documentos. Também pode realizar outras operações como incrementar um campo de documentos por um valor específico, empurrar valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela API de atualização a granel, consulte a documentação da [API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

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

3. O pedido invoca a API BulkUpdateAsync. Para saber mais sobre a definição do método BulkUpdateAsync, consulte a documentação da [API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync).  

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
   |maxConcurrencyPerPartitionKeyRange    |   O grau máximo de concordância por intervalo de teclas de partição, definindo este parâmetro para nulo, fará com que a biblioteca utilize o valor predefinido(20).   |
   |maxInMemorySortingBatchSize    |    O número máximo de itens de atualização retirados do enumerador de itens de atualização passou para a chamada da API em cada fase. Para a fase de triagem na memória que ocorre antes da atualização a granel, a definição deste parâmetro para nulo fará com que a biblioteca utilize o valor mínimo predefinido (updateItems.count, 1000000).     |
   | cancelamentoToken|O sinal de cancelamento para sair graciosamente da operação de atualização a granel. |

   **Definição de objeto de resposta de atualização a granel** O resultado da chamada API de atualização a granel contém os seguintes atributos:

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |NúmeroOfDocumentsUpdated (longo)    |   O número de documentos que foram atualizados com sucesso a partir do total de documentos fornecidos à chamada API de atualização a granel.      |
   |TotalRequestUnitsConsumed (duplo)   |    As unidades de pedido total (RUs) consumidas pela chamada API de atualização a granel.    |
   |TotalTimeTaken (TimeSpan)   | O tempo total tomado pela chamada da API de atualização a granel para completar a execução. |
    
## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executor a granel:

* Para melhor desempenho, execute a sua aplicação a partir de uma máquina virtual Azure que está na mesma região que a região de escrita da sua conta Azure Cosmos.  

* Recomenda-se que você instantaneamente um único `BulkExecutor` objeto para toda a aplicação dentro de uma única máquina virtual que corresponde a um recipiente Azure Cosmos específico.  

* Uma vez que uma única operação a granel a execução da API consome uma grande parte do CPU e iO da rede da máquina cliente (isto acontece desovando várias tarefas internamente). Evite desovar várias tarefas simultâneas no âmbito do seu processo de aplicação que executam chamadas de API de operação a granel. Se uma única chamada de API de operação a granel que esteja a funcionar numa única máquina virtual não for capaz de consumir toda a produção do contentor (se a produção do seu contentor > 1 milhão de RU/s), é preferível criar máquinas virtuais separadas para executar simultaneamente as chamadas API de operação a granel.  

* Certifique-se de que o método é invocado após a instantânea instantânea de `InitializeAsync()` um objeto BulkExecutor para ir buscar o mapa de partição do contentor cosmos alvo.  

* No App.Config da sua aplicação, certifique-se de que **o gcServer** está habilitado para um melhor desempenho
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

* Para saber mais sobre os detalhes do pacote NuGet e as notas de lançamento, consulte os detalhes do [executor a granel SDK](sql-api-sdk-bulk-executor-dot-net.md).
