---
title: Usando a biblioteca de Java do executor em massa para efetuar a importação em massa e atualizar operações no Azure Cosmos DB
description: Em massa importar e atualizar documentos do Azure Cosmos DB com a biblioteca de Java do executor em massa.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 65023fbf96dc3e1276413f8c40ecb262d60c1454
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863360"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Utilizar a biblioteca de Java do executor em massa para realizar operações em massa nos dados do Azure Cosmos DB

Este tutorial fornece instruções sobre como utilizar a biblioteca de Java do Azure Cosmos DB em massa executor para importar e atualizar documentos do Azure Cosmos DB. Para saber mais sobre a biblioteca de executor em massa e como o ajuda a tirar partido do débito em massa e de armazenamento, consulte [em massa de descrição geral da biblioteca de executor](bulk-executor-overview.md) artigo. Neste tutorial, cria uma aplicação de Java que gera documentos aleatórios e estão em massa importada para um contentor do Azure Cosmos DB. Depois de importar, será em massa atualizar algumas propriedades de um documento. 

Atualmente, a biblioteca de executor de em massa é suportada pelo Azure Cosmos DB SQL API e apenas para contas de API do Gremlin. Este artigo descreve como utilizar a biblioteca de .NET de executor em massa com contas da API de SQL. Para saber mais sobre como utilizar a biblioteca de .NET de executor em massa com a API do Gremlin, veja [realizar operações em massa na API do Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.  

* Pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos. Em alternativa, pode utilizar o [emulador do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida em [Autenticar pedidos](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.  

  - Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.

* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário [Maven](https://maven.apache.org/)  
  
  - No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.

* Criar uma conta do Azure Cosmos DB SQL API com os passos descritos em [criar conta de base de dados](create-sql-api-java.md#create-a-database-account) secção do artigo de início rápido de Java.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos trabalhar com o código ao transferir uma aplicação Java de exemplo do GitHub. Esta aplicação executa operações em massa nos dados do Azure Cosmos DB. Para clonar a aplicação, abra uma linha de comandos, navegue para o diretório onde pretende copiar o aplicativo e execute o seguinte comando:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

O repositório clonado contém dois exemplos "bulkimport" e "bulkupdate" relativo para a pasta "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". O aplicativo de "bulkimport" gera documentos aleatórios e importa-as ao Azure Cosmos DB. O aplicativo de "bulkupdate" atualiza alguns documentos no Azure Cosmos DB. Nas secções seguintes, vamos rever o código em cada uma destas aplicações de exemplo. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Dados de importação em massa para o Azure Cosmos DB

1. Cadeias de ligação do Azure Cosmos DB são lidos como argumentos e atribuídas a variáveis definidas no ficheiro de CmdLineConfiguration.java.  

2. Em seguida, o objeto DocumentClient é inicializado ao utilizar as seguintes instruções:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. O objeto de DocumentBulkExecutor é inicializado com uma repetição elevada os valores de tempo de espera e limitado de pedidos. E, em seguida, eles são definidos como 0 para passar o controle de congestionamento para DocumentBulkExecutor para seu ciclo de vida.  

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

4. Chame o importAll API que gera documentos aleatórios em massa de importação para um contentor do Azure Cosmos DB. Pode configurar as configurações de linha de comandos dentro do arquivo CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   A API de importação em massa aceita uma coleção de documentos JSON serializado e tem a seguinte sintaxe, para obter mais detalhes, consulte a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   O método de importAll aceita os seguintes parâmetros:
 
   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |isUpsert    |   Um sinalizador para ativar upsert dos documentos. Se um documento com ID já existe, é atualizado.  |
   |disableAutomaticIdGeneration     |   Um sinalizador para desativar a geração automática do ID. Por predefinição, é definido como true.   |
   |maxConcurrencyPerPartitionRange    |  O maior grau de simultaneidade por intervalo de chaves de partição. O valor predefinido é 20.  |

   **Em massa a definição do objeto de resposta de importação** o resultado da importação em massa chamada à API contém os seguintes métodos get:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |Int getNumberOfDocumentsImported()  |   O número total de documentos que foram importadas com êxito sem os documentos fornecidos para a maior parte importar chamada à API.      |
   |getTotalRequestUnitsConsumed() duplo   |  As unidades de pedido total (RU) consumidas pela maior parte importar chamada à API.       |
   |Duração getTotalTimeTaken()   |    O tempo total que a importação em massa chamada à API para concluir a execução.     |
   |Lista<Exception> getErrors() |  Obtém a lista de erros se alguns documentos fora do lote fornecido para a maior parte importar chamada de API falhou a obter inserido.       |
   |Lista<Object> getBadInputDocuments()  |    A lista de documentos de formato incorreto que não foram importadas com êxito na massa importar chamada à API. Utilizador deve corrigir os documentos devolvidos e repita a importação. Documentos de formato incorreto incluem documentos cujo valor de ID não é uma cadeia de caracteres (nulo ou qualquer outro tipo de dados é considerado inválido).     |

5. Depois de ter a maior parte importar preparado para o aplicativo, compile a ferramenta da linha de comandos de origem com o comando "arquétipo limpa package". Este comando gera um ficheiro. jar na pasta de destino:  

   ```java
   mvn clean package
   ```

6. Depois das dependências de destino são geradas, pode invocar o aplicativo importador de em massa usando o seguinte comando:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   O importador de em massa cria uma nova base de dados e uma coleção com o nome de base de dados, nome da coleção e os valores de taxa de transferência especificados no ficheiro App. config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Dados de atualização em massa no Azure Cosmos DB

Pode atualizar os documentos existentes com a API de BulkUpdateAsync. Neste exemplo, irá definir o campo de nome para um novo valor e remover o campo de descrição dos documentos existentes. Para o conjunto completo de campo suportado operações de atualização, consulte [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Define os itens de atualização, juntamente com as operações de atualização de campo correspondente. Neste exemplo, irá utilizar SetUpdateOperation para atualizar o campo de nome e UnsetUpdateOperation para remover o campo de descrição de todos os documentos. Pode também executar outras operações, como o incremento um campo de documento por um valor específico, enviar por push valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela atualização em massa API, veja a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

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

2. Chame o updateAll API que gera documentos aleatórios, em seguida, seja em massa importada para um contentor do Azure Cosmos DB. Pode configurar as configurações da linha de comandos a serem passados no ficheiro de CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   A API de atualização em massa aceita uma coleção de itens a serem atualizados. Cada item de atualização Especifica a lista de operações de atualização de campo a ser executada num documento identificado por um ID e um valor de chave de partição. Para obter mais detalhes, consulte a [documentação da API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   O método de updateAll aceita os seguintes parâmetros:

   |**Parâmetro** |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  O maior grau de simultaneidade por intervalo de chaves de partição. O valor predefinido é 20.  |
 
   **Em massa a definição do objeto de resposta de importação** o resultado da importação em massa chamada à API contém os seguintes métodos get:

   |**Parâmetro** |**Descrição**  |
   |---------|---------|
   |Int getNumberOfDocumentsUpdated()  |   O número total de documentos que foram atualizadas com êxito sem os documentos fornecidos para a chamada de API de atualização em massa.      |
   |getTotalRequestUnitsConsumed() duplo |  As unidades de total do pedido (RU) consumidos pela atualização em massa, chamada de API.       |
   |Duração getTotalTimeTaken()  |   O tempo total que a maior parte chamada à API para concluir a execução de atualização.      |
   |Lista<Exception> getErrors()   |     Obtém a lista de erros se alguns documentos fora do lote fornecido para a chamada de API de atualização em massa Falha ao obter inserido.      |

3. Depois de ter a maior parte atualizar preparado para o aplicativo, compile a ferramenta da linha de comandos de origem com o comando "arquétipo limpa package". Este comando gera um ficheiro. jar na pasta de destino:  

   ```
   mvn clean package
   ```

4. Depois das dependências de destino são geradas, pode invocar a aplicação de atualização em massa usando o seguinte comando:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Sugestões de desempenho 

Considere os seguintes pontos para um melhor desempenho ao utilizar a biblioteca de executor em massa:

* Para obter melhor desempenho, execute a aplicação a partir de uma VM do Azure na mesma região que a sua região de escrita de conta do Cosmos DB.  
* Para alcançar um débito mais elevado:  

   * Defina o tamanho da pilha o JVM para um número grande o suficiente para evitar qualquer problema de memória no processamento de grande número de documentos. Sugestões de tamanho da pilha: máximo (3GB, 3 * sizeof (todos os documentos passados para em massa importar API num lote)).  
   * Há um tempo de pré-processamento, devido ao qual irá obter um débito mais elevado quando efetuar operações em massa com um grande número de documentos. Então, se quiser importar 10.000.000 documentos, executar importação em massa 10 vezes em 10 em volume de documentos cada tamanho 1.000.000 é preferível que a execução importação em massa de 100 vezes em 100 em massa de documentos cada um dos documentos de 100 000 de tamanho.  

* Recomenda-se para criar uma instância de um único objeto DocumentBulkExecutor para todo o aplicativo dentro de uma única máquina virtual que corresponde a um contentor específico do Azure Cosmos DB.  

* Uma vez que uma execução de API de operação em massa única consome uma grande parte de e/s da CPU e da rede de máquina cliente. Isso acontece por gerar várias tarefas internamente, evite ao gerar várias tarefas em simultâneo em seu processo de aplicativo que chama cada API de operação em massa em execução. Se uma chamada de API de operação em massa única em execução numa única máquina virtual não é possível consumir o débito do contentor inteiro (se. o débito > 1 o contentor milhão RU/s), é preferível para criar máquinas virtuais separadas para executar simultaneamente em massa chamadas de operação de API.

    
## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre os detalhes do pacote maven e notas da biblioteca de Java do executor em massa de versão, consulte[em massa detalhes sobre o SDK executor](sql-api-sdk-bulk-executor-java.md).


