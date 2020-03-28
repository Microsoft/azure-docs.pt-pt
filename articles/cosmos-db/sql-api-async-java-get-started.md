---
title: 'Tutorial: Construa uma app Java com Azure Cosmos DB Async Java SDK para gerir dados na conta SQL API'
description: Este tutorial mostra-lhe como armazenar e aceder a dados dentro de uma conta SQL API em Azure Cosmos DB utilizando uma aplicação Async Java.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 8704e399156b9cfc6b04ff47af49b956b597a539
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75444896"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Construa uma aplicação Java com o SDK Async Java para gerir os dados armazenados numa conta SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java assíncrono](sql-api-async-java-get-started.md)
> * [Nó.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, pode ter aplicações que utilizam dados de documentos NoSQL. Pode utilizar a conta SQL API no Azure Cosmos DB para armazenar e aceder a estes dados documentais. Este tutorial mostra-lhe como construir uma aplicação Java com o SDK Async Java para armazenar e gerir dados de documentos. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criação e ligação a uma conta Azure Cosmos
> * Configurar a sua solução
> * Criação de uma coleção
> * Criação de documentos JSON
> * Consulta da coleção

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem os recursos seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 

* [Git.](https://git-scm.com/downloads)

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven.](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta Azure Cosmos utilizando os seguintes passos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>Clone do repositório GitHub

Clone o repositório GitHub para [Get Started com Azure Cosmos DB e Java.](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) Por exemplo, a partir de um diretório local, executar o seguinte para recuperar o projeto de amostra localmente.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

O diretório `pom.xml` contém um `src/main/java/com/microsoft/azure/cosmosdb/sample` ficheiro e uma `Main.java`pasta contendo código fonte Java, incluindo . O projeto contém código necessário para realizar operações com a Azure Cosmos DB, como criar documentos e consultar dados dentro de uma coleção. O `pom.xml` ficheiro inclui uma dependência do [Azure Cosmos DB Java SDK em Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>Ligue-se a uma conta Azure Cosmos

Em seguida, volte para o [portal Azure](https://portal.azure.com) para recuperar o seu ponto final e a chave principal. O ponto final e a chave primária do Azure Cosmos DB são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação. O ficheiro `AccountSettings.java` contém a chave primária e os valores URI. 

No portal Azure, vá à sua conta Azure Cosmos e, em seguida, clique em **Keys**. Copie o URI e a CHAVE PRIMÁRIA do portal e cole-os no ficheiro `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Captura de ecrã para obter chaves do portal][keys]

## <a name="initialize-the-client-object"></a>Inicializar o objeto do cliente

Inicialize o objeto do cliente utilizando o URI do anfitrião e os valores-chave primários definidos no ficheiro "AccountSettings.java".

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>Criar uma base de dados

Crie a sua base de `createDatabaseIfNotExists()` dados Azure Cosmos utilizando o método da classe DocumentClient. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a name="create-a-collection"></a><a id="CreateColl"></a>Criar uma coleção

Pode criar uma coleção `createDocumentCollectionIfNotExists()` utilizando o método da classe DocumentClient. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

> [!WARNING]
> **createCollection** cria uma nova coleção com débito reservado, tendo repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a name="create-json-documents"></a><a id="CreateDoc"></a>Criar documentos JSON

Crie um documento utilizando o método createDocument da classe DocumentClient. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Podemos agora inserir um ou mais documentos. O ficheiro "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" define os documentos json da família. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>Recursos DB da Consulta Azure Cosmos

O Azure Cosmos DB suporta consultas extensas de documentos JSON armazenados em cada coleção. O seguinte código de amostra mostra como consultar documentos em Azure Cosmos DB usando sintaxe SQL com o `queryDocuments` método.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a name="run-your-java-console-application"></a><a id="Run"></a>Execute a sua aplicação de consola Java

Para executar a aplicação a partir da consola, vá à pasta do projeto e compilar usando Maven:

```bash
mvn package
```

Executar `mvn package` transfere a biblioteca do Azure Cosmos DB mais recente a partir do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute a aplicação executando:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Já completou este tutorial NoSQL e tem uma aplicação de consola Java em funcionamento.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não são necessários, pode eliminar o grupo de recursos, a conta Azure Cosmos e todos os recursos relacionados. Para isso, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar**, e, em seguida, confirmar o nome do grupo de recursos para apagar.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir uma app Java com o SDK Async Java para gerir os dados da SQL API em Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Compilar uma aplicação de consola Node.js com o JavaScript SDK e o Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
