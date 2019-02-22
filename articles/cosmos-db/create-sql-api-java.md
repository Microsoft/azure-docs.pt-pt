---
title: Criar uma base de dados de documentos do Azure Cosmos DB com Java'
description: Apresenta um exemplo de código Java que pode utilizar para ligar e consultar a Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: sngun
ms.openlocfilehash: 0e80fbc96bf1241308c80d108e9f9580122b801b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587723"
---
# <a name="quickstart-build-a-java-application-using-azure-cosmos-db-sql-api-account"></a>Início rápido: Criar uma aplicação de Java utilizando a conta do Azure Cosmos DB SQL API


> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (pré-visualização)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Este início rápido mostra como criar e gerir recursos de uma conta de [API SQL do Azure Cosmos DB](sql-api-introduction.md) com uma aplicação de Java. Primeiro, crie uma conta de API SQL do Azure Cosmos DB com o portal do Azure, crie uma aplicação de Java com o [SDK de Java do SQL](sql-api-sdk-async-java.md), adicione recursos à sua conta do Cosmos DB com a aplicação de Java. As instruções deste guia de início rápido podem ser seguidas em qualquer sistema operativo capaz de executar Java. Depois de concluir este início rápido, estará familiarizado com a criação e modificação de bases de dados e coleções do Cosmos DB na IU ou programaticamente, conforme a sua preferência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Além disso: 

* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário [Maven](https://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta de API SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação da SQL API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar para [Executar a aplicação](#run-the-app). 

* `AsyncDocumentClient` inicialização. O [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) oferece representação lógica do lado do cliente para o serviço de base de dados do Azure Cosmos DB. Este cliente serve para configurar e executar pedidos contra o serviço.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Criação da [base de dados](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.database).

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Criação de [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection).

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Criação de documentos com o método [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document).

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* As consultas SQL sobre JSON são realizadas com o método [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments?view=azure-java-stable).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Executar a aplicação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e inicie a aplicação com as suas informações de ponto final. Isto permite à aplicação comunicar com a base de dados alojada.


1. Na janela de terminal do git, `cd` para a pasta de código de exemplo.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes Java necessários.

    ```bash
    mvn package
    ```

3. Na janela de terminal do git, utilize o seguinte comando para iniciar a aplicação de Java (substitua YOUR_COSMOS_DB_HOSTNAME pelo valor do URI indicado do portal e substitua YOUR_COSMOS_DB_MASTER_KEY pela chave primária indicada do portal)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A janela do terminal apresenta uma notificação que a base de dados FamilyDB tinha criado. 
    
4. Prima uma tecla para criar a base de dados e, em seguida, outra tecla para criar a coleção. 

    Mude outra vez para o Data Explorer no seu browser para ver que contém agora uma base de dados FamilyDB e uma coleção FamilyCollection.

5. Mude para a janela da consola e prima uma tecla para criar o primeiro documento e, em seguida, outra tecla para criar o segundo documento. Em seguida, mude novamente para o Data Explorer para vê-los. 

6. Prima uma tecla para executar uma consulta e ver o resultado na janela da consola. 

7. A aplicação não elimina os recursos criados. Mude novamente para o portal para [limpar os recursos](#clean-up-resources).  da sua conta, para que não incorra em custos.

    ![Saída da consola](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, aprendeu a criar uma conta do Azure Cosmos DB, uma base de dados de documentos e uma coleção com o Data Explorer, bem como a executar uma aplicação para fazer o mesmo programaticamente. Agora, pode importar dados adicionais para a sua coleção do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
