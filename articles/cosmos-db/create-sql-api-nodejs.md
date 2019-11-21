---
title: 'Quickstart: Use Node.js to query from Azure Cosmos DB SQL API account'
description: How to use Node.js to create an app that connects to Azure Cosmos DB SQL API account and queries data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 44cdd4307be56d864afb45d619958cc59a3fa978
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220525"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Use Node.js to connect and query data from Azure Cosmos DB SQL API account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

This quickstart demonstrates how to use a Node.js app to connect to the [SQL API](sql-api-introduction.md) account in Azure Cosmos DB. You can then use Azure Cosmos DB SQL queries to query and manage data. The Node.js app you build in this article uses the [SQL JavaScript SDK](sql-api-sdk-node.md). Este início rápido utiliza a versão 2.0 do [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Além disso:
    * Versão do [Node.js](https://nodejs.org/en/) v6.0.0 ou superior
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Criar uma base de dados 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Add a container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Now let's clone a Node.js app from GitHub, set the connection string, and run it.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. If you're interested in learning how the Azure Cosmos database resources are created in the code, you can review the following snippets. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Tenha em conta que, se estiver familiarizado com a versão anterior do SDK do JavaScript, poderá estar habituado a ver os termos “coleção” e “documento”. Uma vez que o Azure Cosmos DB suporta [vários modelos de API](https://docs.microsoft.com/azure/cosmos-db/introduction), a versão 2.0+ do SDK do JavaScript utiliza os termos genéricos “contentor”, que pode ser uma coleção, um grafo ou uma tabela, e “item” para descrever os conteúdos dos contentores.

Os seguintes fragmentos são retirados do ficheiro **app.js**.

* The `CosmosClient` object is initialized.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Create a new Azure Cosmos database.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* A new container (collection) is created within the database.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* É criado um item (documento).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* A SQL query over JSON is performed on the family database. The query returns all the children of the "Anderson" family. 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Now go back to the Azure portal to get the connection string details of your Azure Cosmos account. Copy the connection string into the app so that it can connect to your database.

1. In the [Azure portal](https://portal.azure.com/), in your Azure Cosmos account, in the left navigation click **Keys**, and then click **Read-write Keys**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro `config.js` no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. Abra o ficheiro `config.js`. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça deste o valor da chave do ponto final em `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de `config.key` em `config.js`. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>Executar a aplicação

1. Execute `npm install` num terminal para instalar os módulos npm necessários

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

You can now go back to Data Explorer, modify, and work with this new data.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you've learned how to create an Azure Cosmos account, create a container using the data explorer, and run an app. You can now import additional data to your Azure Cosmos database. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


