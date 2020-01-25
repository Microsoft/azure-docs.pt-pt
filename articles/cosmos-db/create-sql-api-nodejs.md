---
title: 'Quickstart: Use Node.js para consultar a conta API Da Azure Cosmos DB SQL'
description: Como usar o Node.js para criar uma app que se conecta à conta API Da Azure Cosmos DB SQL e consulta dados.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 8df78df27ffb7e8bb8fc88567bd0b3d37be20488
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719505"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Use Node.js para ligar e consultar dados da conta API Da Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Este quickstart demonstra como usar uma app Node.js para se conectar à conta [SQL API](sql-api-introduction.md) em Azure Cosmos DB. Em seguida, pode utilizar consultas Azure Cosmos DB SQL para consultar e gerir dados. A aplicação Node.js que construíu neste artigo utiliza o [SQL JavaScript SDK](sql-api-sdk-node.md). Este início rápido utiliza a versão 2.0 do [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Além disso:
    * Versão do [Node.js](https://nodejs.org/en/) v6.0.0 ou superior
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Criar uma base de dados 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicione um recipiente

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação Node.js do GitHub, definir a cadeia de ligação e executá-la.

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

Este passo é opcional. Se estiver interessado em saber como os recursos da base de dados Azure Cosmos são criados no código, pode rever os seguintes cortes. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Tenha em conta que, se estiver familiarizado com a versão anterior do SDK do JavaScript, poderá estar habituado a ver os termos “coleção” e “documento”. Uma vez que o Azure Cosmos DB suporta [vários modelos de API](https://docs.microsoft.com/azure/cosmos-db/introduction), a versão 2.0+ do SDK do JavaScript utiliza os termos genéricos “contentor”, que pode ser uma coleção, um grafo ou uma tabela, e “item” para descrever os conteúdos dos contentores.

Os seguintes fragmentos são retirados do ficheiro **app.js**.

* O `CosmosClient` objeto é inicializado.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Crie uma nova base de dados Azure Cosmos.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Um novo recipiente (recolha) é criado dentro da base de dados.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* É criado um item (documento).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Uma consulta SQL sobre jSON é realizada na base de dados da família. A consulta devolve todas as crianças da família "Anderson". 

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

Agora volte ao portal Azure para obter os detalhes da linha de ligação da sua conta Azure Cosmos. Copie a cadeia de ligação para a aplicação de modo a que possa ligar-se à sua base de dados.

1. No [portal Azure](https://portal.azure.com/), na sua conta Azure Cosmos, na navegação à esquerda clique em Teclas, clique em **Teclas**, e, em seguida, clique em **Teclas de leitura de escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro `config.js` no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. Abra o ficheiro `config.js`. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça deste o valor da chave do ponto final em `config.js`. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de `config.key` em `config.js`. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Executar a aplicação

1. Execute `npm install` num terminal para instalar os módulos npm necessários

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

Agora pode voltar ao Data Explorer, modificar e trabalhar com estes novos dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos, criar um recipiente usando o explorador de dados e executar uma app. Pode agora importar dados adicionais para a sua base de dados Azure Cosmos. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


