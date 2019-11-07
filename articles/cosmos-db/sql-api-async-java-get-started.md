---
title: 'Tutorial: compilar um aplicativo Java com o SDK do Java assíncrono para gerenciar uma conta da API do SQL no Azure Cosmos DB'
description: Este tutorial mostra como armazenar e acessar dados em uma conta da API do SQL no Azure Cosmos DB usando um aplicativo Java assíncrono.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 972328ec5052f5089994efbb940ff00c889ae78f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720869"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: compilar um aplicativo Java com o SDK do Java assíncrono para gerenciar dados armazenados em uma conta da API do SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar a conta da API do SQL no Azure Cosmos DB para armazenar e acessar esses dados do documento. Este tutorial mostra como criar um aplicativo Java com o SDK Java assíncrono para armazenar e gerenciar dados de documento. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criando e conectando-se a uma conta do Azure Cosmos
> * Configurando sua solução
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

Crie uma conta do Azure Cosmos usando as seguintes etapas:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Clonar o repositório do GitHub

Clone o repositório do GitHub para [introdução ao Azure Cosmos DB e ao Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Por exemplo, em um diretório local, execute o seguinte para recuperar o projeto de exemplo localmente.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

O diretório contém um arquivo `pom.xml` e uma pasta `src/main/java/com/microsoft/azure/cosmosdb/sample` que contém o código-fonte Java, incluindo `Main.java`. O projeto contém o código necessário para executar operações com Azure Cosmos DB, como criar documentos e consultar dados em uma coleção. O arquivo de `pom.xml` inclui uma dependência do [SDK do Java Azure Cosmos DB no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Conectar-se a uma conta do Azure Cosmos

Em seguida, regresse ao [portal do Azure](https://portal.azure.com) para obter o seu ponto final e a chave mestra primária. O ponto final e a chave primária do Azure Cosmos DB são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação. O ficheiro `AccountSettings.java` contém a chave primária e os valores URI. 

Na portal do Azure, vá para sua conta do cosmos do Azure e clique em **chaves**. Copie o URI e a CHAVE PRIMÁRIA do portal e cole-os no ficheiro `AccountSettings.java`. 

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

Inicialize o objeto de cliente usando os valores URI de host e chave primária definidos no arquivo "AccountSettings. java".

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Criar uma Base de Dados

Crie seu banco de dados Cosmos do Azure usando o método `createDatabaseIfNotExists()` da classe DocumentClient. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

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

Você pode criar uma coleção usando o método `createDocumentCollectionIfNotExists()` da classe DocumentClient. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

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

Crie um documento usando o método CreateDocument da classe DocumentClient. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Podemos agora inserir um ou mais documentos. O arquivo "src/main/java/com/Microsoft/Azure/cosmosdb/Sample/familys. java" define os documentos JSON da família. 

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

## <a id="Query"></a>Recursos de Azure Cosmos DB de consulta

O Azure Cosmos DB suporta consultas extensas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra como consultar documentos no Azure Cosmos DB usando a sintaxe SQL com o método `queryDocuments`.

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

## <a id="Run"></a>Executar seu aplicativo de console Java

Para executar o aplicativo no console do, vá para a pasta do projeto e compile usando o Maven:

```bash
mvn package
```

Executar `mvn package` transfere a biblioteca do Azure Cosmos DB mais recente a partir do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute a aplicação executando:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Agora você concluiu este tutorial do NoSQL e tem um aplicativo de console Java funcional.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá excluir o grupo de recursos, a conta do Azure Cosmos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a máquina virtual, selecione **excluir**e confirme o nome do grupo de recursos a ser excluído.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a criar um aplicativo Java com o SDK do Java assíncrono para gerenciar dados da API do SQL em Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Compilar uma aplicação de consola Node.js com o JavaScript SDK e o Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
