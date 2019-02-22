---
title: 'o Azure Cosmos DB: Criar uma aplicação de node. js com o SDK de JavaScript para gerir os dados do Azure Cosmos DB SQL API'
description: Apresenta um exemplo de código Node.js que pode utilizar para ligar e consultar a Azure Cosmos DB SQL API
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 5a5286695508e46fa24eb5c49cdaf0fe1318fc9d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585497"
---
# <a name="quickstart-build-a-nodejs-app-using-azure-cosmos-db-sql-api-account"></a>Início rápido: Criar uma aplicação de node. js com a conta do Azure Cosmos DB SQL API

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (pré-visualização)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta [API SQL](sql-api-introduction.md) do Azure Cosmos DB, bases de dados de documentos e contentores com o portal do Azure. Depois, vai criar e executar uma aplicação de consola criada no [SDK SQL JavaScript](sql-api-sdk-node.md). Este início rápido utiliza a versão 2.0 do [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Além disso:
    * Versão do [Node.js](https://nodejs.org/en/) v6.0.0 ou superior
    * [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação API SQL a partir do GitHub, definir a cadeia de ligação e executá-la.

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

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

Tenha em conta que, se estiver familiarizado com a versão anterior do SDK do JavaScript, poderá estar habituado a ver os termos “coleção” e “documento”. Uma vez que o Azure Cosmos DB suporta [vários modelos de API](https://docs.microsoft.com/azure/cosmos-db/introduction), a versão 2.0+ do SDK do JavaScript utiliza os termos genéricos “contentor”, que pode ser uma coleção, um grafo ou uma tabela, e “item” para descrever os conteúdos dos contentores.

Os seguintes fragmentos são retirados do ficheiro **app.js**.

* O `CosmosClient` é inicializado.

    ```javascript
    const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
    ```

* É criada uma nova base de dados.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* É criado um contentor (coleção) novo.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* É criado um item (documento).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* É feita uma consulta SQL através de JSON.

    ```javascript
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
    ```    

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](https://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro `config.js` no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. Abra o ficheiro `config.js`. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça deste o valor da chave do ponto final em `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de `config.primaryKey` em `config.js`. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `config.primaryKey = "FILLME"`
    
## <a name="run-the-app"></a>Executar a aplicação
1. Executar `npm install` num terminal para instalar os módulos npm necessários

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

Agora, pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e a executar uma aplicação. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


