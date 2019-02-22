---
title: Criar uma aplicação de consola com a API do Azure Cosmos DB para MongoDB e o SDK de Java
description: Apresenta um exemplo de código Java que pode utilizar para ligar e consultar com a API do Azure Cosmos DB do MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2017
ms.author: rimman
ms.openlocfilehash: 2fcd5f9e68d7f8bfa15cd596407c78af7fc8976b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587264"
---
# <a name="quickstart-build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>Início rápido: Criar uma aplicação web com a API do Azure Cosmos DB para MongoDB e o SDK de Java

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar documentos, chave/valor e gráficos bases de dados, que beneficiam de capacidades de escalamento horizontal no núcleo do Cosmos DB e distribuição global rapidamente. 

Este início rápido demonstra como criar uma conta do Cosmos com [API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md). Em seguida, vai criar e implementar uma aplicação de consola criada utilizando o [controlador Java do MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
* JDK 1.7+ (Execute `apt-get install default-jdk` se não tiver o JDK)
* Maven (Execute `apt-get install maven` se não tiver o Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

Dê um nome à base de dados nova, **db**, e à coleção nova, **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação a partir do GitHub, definir a cadeia de ligação e executá-lo. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Em seguida, abra o código no seu editor favorito. 

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Os seguintes fragmentos são retirados do ficheiro Program.java.

* O DocumentClient é inicializado.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* São criadas uma base de dados e uma coleção novas.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* São inseridos alguns documentos com `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* São executadas algumas consultas com `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. Em Conta, selecione **Guia de Introdução**, selecione Java e copie a cadeia de ligação para a área de transferência.

2. Abra o ficheiro `Program.java`, substitua o argumento do construtor MongoClientURI pela cadeia de ligação. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Execute `mvn package` num terminal para instalar os módulos npm necessários

2. Execute `mvn exec:java -D exec.mainClass=GetStarted.Program` num terminal para iniciar a aplicação Java.

Agora, pode utilizar [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) para consultar, modificar e trabalhar com estes dados novos.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como criar uma conta do Cosmos, criar uma coleção e executar uma aplicação de consola. Agora, pode importar dados adicionais à sua base de dados do Cosmos.

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
