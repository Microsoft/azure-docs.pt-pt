---
title: Utilize a biblioteca Java executor a granel em Azure Cosmos DB para realizar operações de importação e atualização a granel
description: Importar e atualizar documentos da Azure Cosmos DB utilizando biblioteca java executor a granel
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: f5c6562c6def1fa588724b3bc5da502536b16aa9
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985648"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Utilizar a biblioteca Java de executor em massa para realizar operações em massa nos dados do Azure Cosmos DB

Este tutorial fornece instruções sobre a utilização da biblioteca Java, executor a granel do Azure Cosmos DB, para importar e atualizar documentos da Azure Cosmos DB. Para saber sobre a biblioteca de executores a granel e como ajuda a alavancar a entrada e armazenamento maciços, consulte o artigo de visão geral da [Biblioteca executor a granel.](bulk-executor-overview.md) Neste tutorial, você constrói uma aplicação Java que gera documentos aleatórios e eles são importados a granel em um recipiente Azure Cosmos. Após a importação, irá atualizar em massa algumas propriedades de um documento. 

Atualmente, a biblioteca de executora a granel é suportada apenas pelas contas Azure Cosmos DB SQL API e Gremlin API. Este artigo descreve como usar a biblioteca Java executor a granel com contas SQL API. Para aprender sobre a utilização da biblioteca do executor a granel .NET com a API Gremlin, consulte a realização de operações a [granel em Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.  

* Você pode [experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. Ou, pode usar o [Emulador DB Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/local-emulator) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticating-requests).  

* [Kit de Desenvolvimento Java (JDK) 1.7+](/java/azure/jdk/?view=azure-java-stable)  
  - No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.  

  - Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.

* [Descarregue](https://maven.apache.org/download.cgi) e [instale](https://maven.apache.org/install.html) um arquivo binário [Maven](https://maven.apache.org/)  
  
  - No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.

* Crie uma conta API Azure Cosmos DB SQL utilizando os passos descritos na secção de [conta de base](create-sql-api-java.md#create-a-database-account) de dados do artigo java quickstart.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos mudar para trabalhar com código, baixando uma aplicação java de amostra do GitHub. Esta aplicação realiza operações a granel em dados da Azure Cosmos DB. Para clonar a aplicação, abra um pedido de comando, navegue para o diretório onde pretende copiar a aplicação e executar o seguinte comando:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

O repositório clonado contém duas amostras "bulkimport" e "bulkupdate" relativas à pasta "\azure-cosmosdb-bulkexecutor-java-getting-start\samples\bulkexecuteor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecuteor". A aplicação "bulkimport" gera documentos aleatórios e importa-os para a Azure Cosmos DB. A aplicação "bulkupdate" atualiza alguns documentos em Azure Cosmos DB. Nas próximas secções, iremos rever o código em cada uma destas aplicações de amostra. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Dados de importação a granel para O BD da Azure Cosmos

1. As cordas de ligação do Azure Cosmos DB são lidas como argumentos e atribuídas a variáveis definidas no ficheiro CmdLineConfiguration.java.  

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

3. O objeto DocumentBulkExecutor é inicializado com um elevado valor de repetição para o tempo de espera e pedidos acelerados. E então eles estão definidos para 0 para passar o controlo de congestionamento para DocumentBulkExecutor para a sua vida.  

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

4. Ligue para a importaçãoAll API que gera documentos aleatórios para importar a granel em um recipiente Azure Cosmos. Pode configurar as configurações da linha de comando dentro do ficheiro CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   A API importaa a granel aceita uma coleção de documentos em série jSON e tem a seguinte sintaxe, para mais detalhes, consulte a documentação da [API:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   O método importado Todos os métodos aceitam os seguintes parâmetros:
 
   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |isUpsert    |   Uma bandeira para permitir a upsert dos documentos. Se um documento com identificação já existir, é atualizado.  |
   |desativarAutomaticIdGeneration     |   Uma bandeira para desativar a geração automática de identificação. Por defeito, está definido para verdade.   |
   |maxConcurrencyPerPartitionRange    |  O grau máximo de conmoeda por intervalo de divisórias. O valor padrão é de 20.  |

   **Definição de objeto de resposta à importação a granel** O resultado da chamada API importada a granel contém os seguintes métodos de obter:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |int getNumberOfDocumentsImportados()  |   O número total de documentos importados com êxito dos documentos fornecidos à chamada a granel.      |
   |duplo getTotalRequestUnitsConsumido()   |  As unidades de pedido totais (RU) consumidas pela chamada API de importação a granel.       |
   |Duração getTotalTimeTaken()   |    O tempo total deato na chamada a granel da API para a execução completa.     |
   |Lista\<Exceção> obter Erros() |  Obtém a lista de erros se alguns documentos fora do lote fornecido à chamada API de importação a granel não forem inseridos.       |
   |Objeto\<de lista> obterBadInputDocuments()  |    A lista de documentos em mau formato que não foram importados com êxito na chamada API de importação a granel. O utilizador deve fixar os documentos devolvidos e voltar a tentar a importação. Os documentos mal formados incluem documentos cujo valor de id não é uma cadeia (nulo ou qualquer outro tipo de dados é considerado inválido).     |

5. Depois de ter a aplicação de importação a granel pronta, construa a ferramenta da linha de comando a partir da fonte utilizando o comando 'mvn clean package'. Este comando gera um ficheiro de frasco na pasta-alvo:  

   ```java
   mvn clean package
   ```

6. Após a geração das dependências-alvo, pode invocar a aplicação do importador a granel utilizando o seguinte comando:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   O importador a granel cria uma nova base de dados e uma coleção com o nome da base de dados, nome de recolha e valores de entrada especificados no ficheiro App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Dados de atualização a granel em Azure Cosmos DB

Pode atualizar os documentos existentes utilizando a API BulkUpdateAsync. Neste exemplo, irá definir o campo Nome para um novo valor e remover o campo Descrição dos documentos existentes. Para o conjunto completo de operações de atualização de campo suportadas, consulte a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Define os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, utilizará a SetUpdateOperation para atualizar o campo nome e a Operação UnsetUpdate para remover o campo Descrição de todos os documentos. Também pode realizar outras operações como incrementar um campo de documentos por um valor específico, empurrar valores específicos para um campo de matriz, ou remover um valor específico de um campo de matriz. Para conhecer os diferentes métodos fornecidos pela atualização a granel API, consulte a documentação da [API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Ligue para a actualizaçãoAll API que gera documentos aleatórios para ser importado a granel num contentor Azure Cosmos. Pode configurar as configurações da linha de comando a passar no ficheiro CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A atualização a granel AAAceita uma coleção de itens para ser atualizado. Cada item de atualização especifica a lista de operações de atualização de campo a realizar num documento identificado por um ID e um valor-chave de partição. Para mais detalhes, consulte a documentação da [API:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   A atualização Todos os métodos aceitam os seguintes parâmetros:

   |**Parâmetro** |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  O grau máximo de conmoeda por intervalo de divisórias. O valor padrão é de 20.  |
 
   **Definição de objeto de resposta à importação a granel** O resultado da chamada API importada a granel contém os seguintes métodos de obter:

   |**Parâmetro** |**Descrição**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   O número total de documentos que foram atualizados com sucesso dos documentos fornecidos à chamada a granel da API.      |
   |duplo getTotalRequestUnitsConsumido() |  As unidades de pedido totais (RU) consumidas pela chamada API de atualização a granel.       |
   |Duração getTotalTimeTaken()  |   O tempo total deatualização a granel chamada API para completar a execução.      |
   |Lista\<Exceção> obter Erros()   |       Obtém a lista de erros se alguns documentos fora do lote fornecido para a chamada API de atualização a granel não forem inseridos.      |

3. Depois de ter a aplicação de atualização a granel pronta, construa a ferramenta da linha de comando a partir da fonte utilizando o comando 'mvn clean package'. Este comando gera um ficheiro de frasco na pasta-alvo:  

   ```
   mvn clean package
   ```

4. Após a geração das dependências-alvo, pode invocar a aplicação de atualização a granel utilizando o seguinte comando:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executores a granel:

* Para um melhor desempenho, execute a sua aplicação a partir de um VM Azure na mesma região que a sua região de escrita de conta Cosmos DB.  
* Para obter um maior resultado:  

   * Desloque o tamanho da pilha do JVM para um número suficientemente grande para evitar qualquer problema de memória no manuseamento de um grande número de documentos. Tamanho sugerido do heap: máx(3GB, 3 * tamanho (todos os documentos passados para a Importação a granel API num lote)).  
   * Existe um tempo de pré-processamento, devido ao qual obterá uma maior potência ao realizar operações a granel com um grande número de documentos. Assim, se quiser importar 10.000.000 documentos, a importação a granel 10 vezes em 10 grossos de documentos cada um de tamanho 1.000.000 é preferível do que executar a importação a granel 100 vezes em 100 grossos de documentos cada um de tamanho 100.000 documentos.  

* Recomenda-se que se instantie um único objeto DocumentBulkExecutor para toda a aplicação dentro de uma única máquina virtual que corresponda a um recipiente azure cosmos específico.  

* Uma vez que uma única operação a granel a execução da API consome uma grande parte do CPU e da rede IO da máquina cliente. Isto acontece desogerando várias tarefas internamente, evitando desovar múltiplas tarefas simultâneas no âmbito do seu processo de aplicação, cada operação a granel que executa chamadas API. Se uma única operação a granel a chamada API em execução numa única máquina virtual não conseguir consumir a entrada de todo o seu recipiente (se a entrada do seu recipiente > 1 milhão de RU/s), é preferível criar máquinas virtuais separadas para executar simultaneamente chamadas API de operação a granel.

    
## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os detalhes do pacote Maven e divulgar notas da biblioteca Java executor a granel, consulte[os detalhes do executor a granel SDK](sql-api-sdk-bulk-executor-java.md).


