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
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134472"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Use Node.js para ligar e consultar dados da conta API Da Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste arranque rápido, cria e gere uma conta API Azure Cosmos DB SQL a partir do portal Azure, e utilizando uma aplicação Node.js clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Você também pode usar o [Emulador Db Azure Cosmos](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

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

Se estiver familiarizado com a versão anterior do SQL JavaScript SDK, poderá estar habituado a ver os termos *de recolha* e *documento*. Uma vez que o Azure Cosmos DB suporta [vários modelos API](introduction.md), a [versão 2.0+ do JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) utiliza o *recipiente*de termos genéricos , que pode ser uma coleção, gráfico ou tabela, e *item* para descrever o conteúdo do recipiente.

Os seguintes fragmentos são retirados do ficheiro *app.js*.

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

1. Na sua conta Azure Cosmos DB no [portal Azure,](https://portal.azure.com/)selecione **Chaves** da navegação à esquerda e, em seguida, selecione **Teclas de leitura .** Utilize os botões de cópia no lado direito do ecrã para copiar a chave URI e Primary no ficheiro *config.js* no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. Em Abrir o ficheiro *config.js.* 

3. Copie o seu valor URI a partir do portal (utilizando o botão de cópia) e faça-o com o valor da chave de ponto final em *config.js*. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Em seguida, copie o seu valor PRINCIPAL CHAVE do portal e faça dele o valor do `config.key` em *config.js*. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Executar a aplicação

1. Executar `npm install` num terminal para instalar os módulos npm necessários

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

Agora pode voltar ao Data Explorer, modificar e trabalhar com estes novos dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB, criar um recipiente usando o Data Explorer e executar uma app Node.js. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


