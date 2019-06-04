---
title: 'Tutorial: Criar uma aplicação de Java com o SDK de Java de Async para gerir uma conta da API de SQL no Azure Cosmos DB'
description: Este tutorial mostra como armazenar e aceder a dados dentro de uma conta SQL API no Azure Cosmos DB com uma aplicação de Java assíncrono.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b46914728c684fe4b28cb1325afb1e0b662522ad
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475808"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Criar uma aplicação de Java com o SDK de Java de Async para gerir os dados armazenados numa conta SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (pré-visualização)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET core (pré-visualização)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, pode ter aplicações que utilizam dados de documentos NoSQL. Pode utilizar a conta de API de SQL no Azure Cosmos DB para armazenar e aceder a estes dados de documento. Este tutorial mostra-lhe como criar uma aplicação Java com o SDK de Java de Async para armazenar e gerir dados de documentos. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar e ligar a uma conta do Cosmos do Azure
> * Configurar a sua solução
> * Criação de uma coleção
> * Criação de documentos JSON
> * Consulta da coleção

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem os recursos seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta do Cosmos do Azure, utilize os seguintes passos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Clonar o repositório do GitHub

Clone o repositório do GitHub para [introdução ao Azure Cosmos DB e Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Por exemplo, a partir de um diretório local, execute o seguinte para obter o projeto de exemplo localmente.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

O diretório contém um `pom.xml` ficheiro e uma `src/main/java/com/microsoft/azure/cosmosdb/sample` pasta que contém o código-fonte Java, incluindo `Main.java`. O projeto contém o código necessário para executar operações com o Azure Cosmos DB, como criação de documentos e consultar dados dentro de uma coleção. O `pom.xml` arquivo inclui uma dependência no [SDK de Java do Azure Cosmos DB no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Ligar a uma conta do Cosmos do Azure

Em seguida, regresse ao [portal do Azure](https://portal.azure.com) para obter o seu ponto final e a chave mestra primária. O ponto final e a chave primária do Azure Cosmos DB são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação. O ficheiro `AccountSettings.java` contém a chave primária e os valores URI. 

No portal do Azure, aceda à sua conta do Cosmos do Azure e, em seguida, clique em **chaves**. Copie o URI e a CHAVE PRIMÁRIA do portal e cole-os no ficheiro `AccountSettings.java`. 

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

## <a name="initialize-the-client-object"></a>Inicializar o objeto de cliente

Inicialize o objeto de cliente com o URI de anfitrião e os valores de chave primárias definidas no arquivo "AccountSettings.java".

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Criar uma Base de Dados

Criar a sua base de dados do Azure Cosmos DB utilizando a `createDatabaseIfNotExists()` método da classe DocumentClient. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

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

## <a id="CreateColl"></a>Criar uma coleção

Pode criar uma coleção, utilizando o `createDocumentCollectionIfNotExists()` método da classe DocumentClient. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

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

## <a id="CreateDoc"></a>Criar documentos JSON

Crie um documento usando o método createDocument da classe DocumentClient. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Podemos agora inserir um ou mais documentos. O arquivo de "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" define os famílias de documentos JSON. 

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

## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB suporta consultas extensas de documentos JSON armazenados em cada coleção. O código de exemplo seguinte mostra como consultar documentos no Azure Cosmos DB utilizando sintaxe SQL com o `queryDocuments` método.

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

## <a id="Run"></a>Execute a sua aplicação de consola Java

Para executar a aplicação a partir da consola, vá para a pasta do projeto e compilar com o Maven:

```bash
mvn package
```

Executar `mvn package` transfere a biblioteca do Azure Cosmos DB mais recente a partir do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute a aplicação executando:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Agora concluiu este tutorial NoSQL e ter uma aplicação de consola Java em funcionamento.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a conta do Cosmos do Azure e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **eliminar**e, em seguida, confirme o nome do grupo de recursos para eliminar.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como criar uma aplicação Java com o SDK de Java de Async para gerir os dados da API de SQL no Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Compilar uma aplicação de consola Node.js com o JavaScript SDK e o Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
