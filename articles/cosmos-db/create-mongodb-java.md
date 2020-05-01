---
title: 'Quickstart: Construa uma aplicação web utilizando o Azure Cosmos DB API para Mongo DB e Java SDK'
description: Aprenda a construir uma amostra de código Java que pode usar para ligar e consultar usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 35c6944ddcfac1553ffb2c1cc28472f2a56d4515
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77061723"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Quickstart: Crie uma app de consola com Java e o MongoDB API em Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Neste arranque rápido, cria e gere uma conta Azure Cosmos DB para a API da MongoDB a partir do portal Azure, e adiciona dados utilizando uma aplicação Java SDK clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Também pode utilizar o [Emulador DB Azure Cosmos](https://aka.ms/cosmosdb-emulator) com a corda `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`de ligação .
- [Java Development Kit (JDK) versão 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). 
- [Maven.](https://maven.apache.org/download.cgi) Ou `apt-get install maven` correr para instalar Maven.
- [Git.](https://git-scm.com/downloads) 

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

Nomeie a sua nova base de dados **db**, e a sua nova **coleção coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

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

Os seguintes cortes são todos retirados do ficheiro *Program.java.*

Esta aplicação de consola utiliza o [condutor MongoDB Java.](https://docs.mongodb.com/ecosystem/drivers/java/) 

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

1. A partir da sua conta Azure Cosmos DB, selecione **Quick Start**, selecione **Java**e, em seguida, copie a cadeia de ligação à sua área de redação.

2. Abra o ficheiro *Program.java,* substitua o argumento para o construtor MongoClientURI com a cadeia de ligação. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Execute `mvn package` num terminal para instalar os módulos npm necessários

2. Execute `mvn exec:java -D exec.mainClass=GetStarted.Program` num terminal para iniciar a aplicação Java.

Agora pode usar [o Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) para consultar, modificar e trabalhar com estes novos dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um API Db Azure Cosmos para a conta Mongo DB, adicionar uma base de dados e um contentor usando o Data Explorer e adicionar dados usando uma aplicação de consola Java. Agora pode importar dados adicionais para a sua base de dados cosmos. 

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
