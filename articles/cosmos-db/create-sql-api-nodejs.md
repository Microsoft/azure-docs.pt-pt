---
title: 'Início rápido: usar o Node. js para consultar de Azure Cosmos DB conta da API do SQL'
description: Como usar o Node. js para criar um aplicativo que se conecta a Azure Cosmos DB a conta da API do SQL e os dados de consultas.
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
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Início rápido: usar o Node. js para se conectar e consultar dados de Azure Cosmos DB conta da API do SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Este guia de início rápido demonstra como usar um aplicativo node. js para se conectar à conta da [API do SQL](sql-api-introduction.md) no Azure Cosmos DB. Você pode usar Azure Cosmos DB consultas SQL para consultar e gerenciar dados. O aplicativo node. js que você cria neste artigo usa o [SDK do JavaScript do SQL](sql-api-sdk-node.md). Este início rápido utiliza a versão 2.0 do [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Além disso:
    * Versão do [Node.js](https://nodejs.org/en/) v6.0.0 ou superior
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Criar uma base de dados 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Adicionar um contêiner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar um aplicativo node. js do GitHub, definir a cadeia de conexão e executá-lo.

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

Este passo é opcional. Se você estiver interessado em aprender como os recursos do banco de dados Cosmos do Azure são criados no código, você pode examinar os trechos a seguir. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Tenha em conta que, se estiver familiarizado com a versão anterior do SDK do JavaScript, poderá estar habituado a ver os termos “coleção” e “documento”. Uma vez que o Azure Cosmos DB suporta [vários modelos de API](https://docs.microsoft.com/azure/cosmos-db/introduction), a versão 2.0+ do SDK do JavaScript utiliza os termos genéricos “contentor”, que pode ser uma coleção, um grafo ou uma tabela, e “item” para descrever os conteúdos dos contentores.

Os seguintes fragmentos são retirados do ficheiro **app.js**.

* O objeto `CosmosClient` é inicializado.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Crie um novo banco de dados Cosmos do Azure.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Um novo contêiner (coleção) é criado no banco de dados.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* É criado um item (documento).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Uma consulta SQL sobre JSON é executada no banco de dados da família. A consulta retorna todos os filhos da família "Anderson". 

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

Agora, volte para a portal do Azure para obter os detalhes da cadeia de conexão de sua conta do Azure Cosmos. Copie a cadeia de conexão no aplicativo para que ele possa se conectar ao banco de dados.

1. Na [portal do Azure](https://portal.azure.com/), em sua conta do Azure Cosmos, no painel de navegação esquerdo, clique em **chaves**e, em seguida, clique em **chaves de leitura/gravação**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro `config.js` no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. Abra o ficheiro `config.js`. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça deste o valor da chave do ponto final em `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de `config.key` em `config.js`. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>Executar a aplicação

1. Executar `npm install` num terminal para instalar os módulos npm necessários

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

Agora você pode voltar para Data Explorer, modificar e trabalhar com esses novos dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como criar uma conta do Azure Cosmos, criar um contêiner usando o data Explorer e executar um aplicativo. Agora você pode importar dados adicionais para o banco de dado Cosmos do Azure. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


