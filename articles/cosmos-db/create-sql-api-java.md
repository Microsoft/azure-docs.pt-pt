---
title: Quickstart - Use Java para criar uma base de dados de documentos usando o Azure Cosmos DB
description: Este quickstart apresenta uma amostra de código Java que pode usar para ligar e consultar a API Azure Cosmos DB SQL
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8c2ae82bae8457a1c715f160994c7a0da94193ff
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134498"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Quickstart: Construa uma app Java para gerir dados da API Da Azure Cosmos DB SQL


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste arranque rápido, cria-se e gere-se uma conta API Azure Cosmos DB SQL a partir do portal Azure, e utilizando uma aplicação Java clonada do GitHub. Primeiro, cria-se uma conta Azure Cosmos DB SQL API utilizando o portal Azure, depois cria-se uma aplicação Java utilizando o SQL Java SDK e, em seguida, adiciona recursos à sua conta Cosmos DB utilizando a aplicação Java. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Você também pode usar o [Emulador Db Azure Cosmos](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- Kit de [Desenvolvimento Java (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Aponte a sua variável ambiente `JAVA_HOME` para a pasta onde o JDK está instalado.
- Um [arquivo binário Maven.](https://maven.apache.org/download.cgi) Em Ubuntu, corra `apt-get install maven` para instalar Maven.
- [Git](https://www.git-scm.com/downloads). Em Ubuntu, executar `sudo apt-get install git` para instalar Git.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta de API SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicione um recipiente

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação da SQL API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar para [Executar a aplicação](#run-the-app). 

* `CosmosClient` inicialização. O `CosmosClient` fornece representação lógica do lado do cliente para o serviço de base de dados Azure Cosmos. Este cliente serve para configurar e executar pedidos contra o serviço.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Criação CosmosDatabase.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Criação CosmosContainer.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Criação de artigos utilizando o método `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* As leituras das pontos são realizadas utilizando `getItem` e `read` método

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* As consultas SQL sobre jSON são realizadas usando o método `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Executar a aplicação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e inicie a aplicação com as suas informações de ponto final. Isto permite à aplicação comunicar com a base de dados alojada.

1. Na janela de terminal do git, `cd` para a pasta de código de exemplo.

    ```bash
    cd azure-cosmos-java-getting-started
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
    
4. A aplicação cria base de dados com nome `AzureSampleFamilyDB`
5. A aplicação cria recipiente com nome `FamilyContainer`
6. A aplicação executará leituras de pontos utilizando IDs de objetos e valor chave de divisória (que é o último nome na nossa amostra). 
7. A aplicação irá consultar itens para recuperar todas as famílias com apelido em ('Andersen', 'Wakefield', 'Johnson')

7. A aplicação não elimina os recursos criados. Mude novamente para o portal para [limpar os recursos](#clean-up-resources).  da sua conta, para que não incorra em custos.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta API Azure Cosmos DB SQL, criar uma base de dados de documentos e um contentor usando o Data Explorer, e executar uma app Java para fazer a mesma coisa programáticamente. Agora pode importar dados adicionais para a sua conta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
