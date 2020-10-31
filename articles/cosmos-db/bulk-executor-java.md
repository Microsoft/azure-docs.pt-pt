---
title: Utilize a biblioteca Java do executor a granel na Azure Cosmos DB para realizar operações de importação e atualização a granel
description: Importação a granel e atualizar documentos DB da Azure Cosmos utilizando a biblioteca Java do executor a granel
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 89d21e4464cb3c7578b68d68009065ab7848ed19
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092539"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Utilizar a biblioteca Java de executor em massa para realizar operações em massa nos dados do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial fornece instruções sobre a utilização da biblioteca Java, executora a granel da Azure Cosmos DB, para importar e atualizar documentos DB do Azure Cosmos. Para saber mais sobre a biblioteca de executores a granel e como o ajuda a alavancar a produção e armazenamento maciços, consulte o artigo [de visão geral da Biblioteca do executor a granel.](bulk-executor-overview.md) Neste tutorial, você constrói uma aplicação Java que gera documentos aleatórios e são importados a granel para um recipiente Azure Cosmos. Após a importação, irá atualizar em massa algumas propriedades de um documento. 

Atualmente, a biblioteca de executor a granel é suportada apenas por contas AZure Cosmos DB SQL API e Gremlin API. Este artigo descreve como usar a biblioteca Java do executor a granel com contas API SQL. Para saber mais sobre a utilização de uma biblioteca a granel .NET com a Gremlin API, consulte [realizar operações a granel na API API API da Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md). A biblioteca de executor a granel descrita está disponível apenas para o [Azure Cosmos DB Java sync SDK v2](sql-api-sdk-java.md) e é a solução recomendada atual para suporte a granel Java. Atualmente não está disponível para as versões 3.x, 4.x ou outras versões SDK mais altas.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.  

* Pode [experimentar gratuitamente a Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. Ou pode usar o [Emulador Azure Cosmos DB](./local-emulator.md) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticate-requests).  

* [Kit de Desenvolvimento java (JDK) 1.7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)  
  - No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.  

  - Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.

* [Faça o download](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário [Maven](https://maven.apache.org/)  
  
  - No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.

* Crie uma conta Azure Cosmos DB SQL API utilizando os passos descritos na secção de conta de base de [dados](create-sql-api-java.md#create-a-database-account) do artigo de arranque rápido de Java.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos mudar para trabalhar com código, descarregando uma aplicação java de amostra do GitHub. Esta aplicação realiza operações a granel nos dados da Azure Cosmos DB. Para clonar a aplicação, abra um pedido de comando, navegue para o diretório onde pretende copiar a aplicação e executar o seguinte comando:

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

O repositório clonado contém duas amostras "bulkimport" e "bulkupdate" relativas à pasta "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\microsoft\azure\cosmosdb\bulkexecutor". A aplicação "bulkimport" gera documentos aleatórios e importa-os para a Azure Cosmos DB. A aplicação "bulkupdate" atualiza alguns documentos em Azure Cosmos DB. Nas próximas secções, vamos rever o código em cada uma destas aplicações de amostra. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Dados de importação a granel para Azure Cosmos DB

1. As cordas de conexão do Azure Cosmos DB são lidas como argumentos e atribuídas a variáveis definidas no ficheiro CmdLineConfiguration.java.  

2. Em seguida, o objeto DocumentClient é inicializado utilizando as seguintes declarações:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. O objeto DocumentBulkExecutor é inicializado com valores elevados de retículo para o tempo de espera e pedidos acelerados. E então eles estão definidos para 0 para passar o controlo de congestionamento para o DocumentBulkExecutor para a sua vida.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Chame a importação Toda a API que gere documentos aleatórios para importar a granel para um recipiente Azure Cosmos. Pode configurar as configurações da linha de comando dentro do ficheiro CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   A API importa a granel aceita uma coleção de documentos serializados pela JSON e tem a seguinte sintaxe, para mais detalhes, consulte a documentação da [API:](/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   O método importAll aceita os seguintes parâmetros:
 
   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |isUpsert    |   Uma bandeira para permitir o upsert dos documentos. Se um documento com identificação já existe, é atualizado.  |
   |desativação AutomaticIdGeneration     |   Uma bandeira para desativar a geração automática de identificação. Por defeito, é definido como verdadeiro.   |
   |maxConcurrencyPerPartitionRange    |  O grau máximo de concordância por intervalo de chaves de partição. O valor predefinido é de 20.  |

   **Definição de objeto de resposta a granel** O resultado da chamada API de importação a granel contém os seguintes métodos de obtê-lo:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   O número total de documentos que foram importados com êxito dos documentos fornecidos à chamada API de importação a granel.      |
   |double getTotalRequestUnitsConsumed()   |  As unidades de pedido total (RU) consumidas pela chamada API de importação a granel.       |
   |Duração getTotalTimeTaken()   |    O tempo total desosseido pela API de importação a granel para a execução completa.     |
   |Lista \<Exception> getErrors() |  Obtém a lista de erros se alguns documentos do lote fornecido à chamada API de importação a granel não forem inseridos.       |
   |Lista \<Object> getBadInputDocuments()  |    A lista de documentos de mau formato que não foram importados com êxito na chamada de API de importação a granel. O utilizador deve corrigir os documentos devolvidos e voltar a tentar a importação. Os documentos mal formatados incluem documentos cujo valor de ID não é uma cadeia (nulo ou qualquer outro tipo de dados é considerado inválido).     |

5. Depois de ter a aplicação de importação a granel pronta, construa a ferramenta de linha de comando a partir da fonte utilizando o comando 'mvn clean package'. Este comando gera um ficheiro de frasco na pasta alvo:  

   ```bash
   mvn clean package
   ```

6. Após a geração das dependências-alvo, pode invocar a aplicação do importador a granel utilizando o seguinte comando:  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   O importador a granel cria uma nova base de dados e uma coleção com o nome da base de dados, o nome da recolha e os valores de produção especificados no ficheiro App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Dados de atualização em massa em Azure Cosmos DB

Pode atualizar os documentos existentes utilizando a API BulkUpdateAsync. Neste exemplo, irá definir o campo Nome para um novo valor e remover o campo Descrição dos documentos existentes. Para o conjunto completo de operações de atualização de campo suportadas, consulte [a documentação da API](/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Define os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, utilizará o SetUpdateOperation para atualizar o campo Nome e a Cooperação Sem Uso para remover o campo Descrição de todos os documentos. Também pode realizar outras operações como incrementar um campo de documentos por um valor específico, empurrar valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para conhecer os diferentes métodos fornecidos pela API de atualização a granel, consulte a documentação da [API](/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                        
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Ligue para a atualização All API que gera documentos aleatórios para ser então importado a granel num recipiente Azure Cosmos. Pode configurar as configurações da linha de comando a passar no ficheiro CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A API de atualização a granel aceita uma recolha de itens a serem atualizados. Cada item de atualização especifica a lista de operações de atualização de campo a realizar num documento identificado por um ID e um valor chave de partição. para mais detalhes, consulte a documentação da [API:](/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   O método updateAll aceita os seguintes parâmetros:

   |**Parâmetro** |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  O grau máximo de concordância por intervalo de chaves de partição. O valor predefinido é de 20.  |
 
   **Definição de objeto de resposta a granel** O resultado da chamada API de importação a granel contém os seguintes métodos de obtê-lo:

   |**Parâmetro** |**Descrição**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   O número total de documentos que foram atualizados com sucesso a partir dos documentos fornecidos à chamada da API de atualização a granel.      |
   |double getTotalRequestUnitsConsumed() |  As unidades de pedido total (RU) consumidas pela chamada API de atualização a granel.       |
   |Duração getTotalTimeTaken()  |   O tempo total tomado pela chamada da API de atualização a granel para completar a execução.      |
   |Lista \<Exception> getErrors()   |       Obtém a lista de problemas operacionais ou de networking relacionados com a operação de atualização.      |
   |Lista \<BulkUpdateFailure> getFailedUpdates()   |       Obtém a lista de atualizações que não puderam ser concluídas juntamente com as exceções específicas que conduzem às falhas.|

3. Depois de ter a aplicação de atualização a granel pronta, construa a ferramenta de linha de comando a partir da fonte utilizando o comando 'mvn clean package'. Este comando gera um ficheiro de frasco na pasta alvo:  

   ```bash
   mvn clean package
   ```

4. Após a geração das dependências-alvo, pode invocar a aplicação de atualização em massa utilizando o seguinte comando:

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executor a granel:

* Para melhor desempenho, execute a sua aplicação a partir de um Azure VM na mesma região que a sua região de escrita de conta Cosmos DB.  
* Para alcançar uma produção mais elevada:  

   * Coloque o tamanho da pilha do JVM num número suficientemente grande para evitar qualquer problema de memória no manuseamento de um grande número de documentos. Tamanho da pilha sugerido: máximo (3GB, 3 * tamanho (todos os documentos passados para a API de importação a granel em um lote)).  
   * Há um tempo de pré-processamento, devido ao qual obterá uma produção mais elevada ao realizar operações a granel com um grande número de documentos. Portanto, se quiser importar 10.000.000 documentos, a importação a granel 10 vezes em 10 volumes de documentos cada um dos 1.000.000 é preferível do que a importação a granel 100 vezes em 100 volumes de documentos cada um dos 100.000 documentos.  

* Recomenda-se instantaneamente um único objeto DocumentBulkExecutor para toda a aplicação dentro de uma única máquina virtual que corresponde a um recipiente Azure Cosmos específico.  

* Uma vez que uma única operação a granel a execução API consome uma grande parte do CPU da máquina cliente e iO de rede. Isto acontece desovando várias tarefas internamente, evite desovar várias tarefas simultâneas dentro do seu processo de aplicação cada uma das chamadas de operação a granel API. Se uma única chamada de API de operação a granel em funcionamento numa única máquina virtual não for capaz de consumir toda a produção do seu contentor (se a produção do seu contentor > de 1 milhão de RU/s), é preferível criar máquinas virtuais separadas para executar simultaneamente chamadas API de operação a granel.

    
## <a name="next-steps"></a>Passos seguintes
* Para conhecer os detalhes do pacote maven e lançar notas da biblioteca Java do executor a granel, consulte[detalhes do executor a granel SDK](sql-api-sdk-bulk-executor-java.md).