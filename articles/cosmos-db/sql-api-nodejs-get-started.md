---
title: Tutorial de Node.js para a SQL API para Azure Cosmos DB
description: Um tutorial do Node.js que demonstra como ligar e consultar o Azure Cosmos DB com a API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 212dd243842a8bdacc8a77241f456795ef508d9e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731694"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Construa uma aplicação de consola Node.js com o JavaScript SDK para gerir os dados da API Do Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java assíncrono](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, pode ter aplicações que utilizam dados de documentos NoSQL. Pode utilizar uma conta SQL API no Azure Cosmos DB para armazenar e aceder a estes dados documentais. Este tutorial mostra-lhe como construir uma aplicação de consola Node.js para criar recursos Db Azure Cosmos e questioná-los.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie e ligue a uma conta Azure Cosmos DB.
> * Configura risa a sua candidatura.
> * Criar uma base de dados.
> * Crie um recipiente.
> * Adicione itens ao recipiente.
> * Efetuar operações básicas nos itens, contentores e bases de dados.

## <a name="prerequisites"></a>Pré-requisitos 

Certifique-se de que tem os recursos seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 ou superior.

## <a name="create-azure-cosmos-db-account"></a>Criar conta Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a aplicação Node.js](#SetupNode). Se estiver a utilizar o Emulador do Azure Cosmos DB, siga os passos em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e avance para [Configurar a aplicação Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Configurar a aplicação Node.js

Antes de começar a escrever código para construir a aplicação, pode construir o quadro para a sua aplicação. Execute os seguintes passos para configurar a sua aplicação Node.js que tem o código-quadro:

1. Abra o seu terminal favorito.
2. Localize a pasta ou o diretório onde pretende guardar a sua aplicação Node.js.
3. Crie ficheiros JavaScript vazios com os seguintes comandos:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Criar e inicializar um `package.json` ficheiro. Utilize o seguinte comando:
   * ```npm init -y```

5. Instale o módulo @azure/cosmos através do npm. Utilize o seguinte comando:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Detete as configurações da sua aplicação

Agora que a sua aplicação existe, tem de se certificar de que pode falar com o Azure Cosmos DB. Ao atualizar algumas definições de configuração, como mostrado nos seguintes passos, pode definir a sua aplicação para falar com o Azure Cosmos DB:

1. Abra o ficheiro *config.js* no seu editor de texto favorito.

1. Copie e cole o seguinte corte de código no ficheiro *config.js* e detete as propriedades `endpoint` e `key` o seu ponto final Azure Cosmos DB URI e chave primária. A base de dados, os nomes dos contentores estão definidos para **tarefas** e **itens**. A chave de partição que utilizará para esta aplicação é **/categoria**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Pode encontrar os detalhes finais e chave no painel **de Teclas** do [portal Azure](https://portal.azure.com).

   ![Captura de ecrã para obter chaves do portal do Azure][keys]

O JavaScript SDK utiliza o *recipiente* de termos genéricos e *o item*. Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. No código anterior, o `module.exports = config;` código é utilizado para exportar o objeto de config, para que possa referenciar no ficheiro *app.js.*

## <a name="create-a-database-and-a-container"></a>Criar uma base de dados e um recipiente

1. Abra o ficheiro *databaseContext.js* no seu editor de texto favorito.

1. Copiar e colar o seguinte código no ficheiro *databaseContext.js.* Este código define uma função que cria a base de dados "Tarefas", "Itens" e o recipiente se eles já não existirem na sua conta Azure Cosmos:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   As bases de dados são os contentores lógicos dos itens particionados em contentores. Cria uma base de `createIfNotExists` dados utilizando a função ou criação da classe **Databases.** Um recipiente é composto por itens que, no caso da API SQL, são documentos JSON. Cria um recipiente utilizando `createIfNotExists` a função ou criar a partir da classe **Contentores.** Depois de criar um recipiente, pode armazenar e consultar os dados.

   > [!WARNING]
   > A criação de um contentor tem implicações nos preços. Visite [a](https://azure.microsoft.com/pricing/details/cosmos-db/) nossa página de preços para que saiba o que esperar.

## <a name="import-the-configuration"></a>Importar a configuração

1. Abra o ficheiro *app.js* no seu editor de texto favorito.

1. Copie e cole o código `@azure/cosmos` abaixo para importar o módulo, a configuração e a base de dadosContexto que definiu nos passos anteriores. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Ligue-se à conta Azure Cosmos

No ficheiro *app.js,* copie e cole o seguinte código para utilizar o ponto final e a chave previamente guardados para criar um novo objeto CosmosClient.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Se ligar ao **Emulador Cosmos DB,** desative a verificação tLS para o seu processo de nó:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Agora que tem o código para inicializar o cliente do Azure Cosmos DB, vamos ver como utilizar os recursos do Azure Cosmos DB.

## <a name="query-items"></a><a id="QueryItem"></a>Itens de consulta

A Azure Cosmos DB suporta ricas consultas contra itens JSON armazenados em cada recipiente. O seguinte código de amostra mostra uma consulta que pode correr contra os itens do seu recipiente. Pode consultar os itens utilizando a função de `Items` consulta da classe. Adicione o seguinte código ao ficheiro *app.js* para consultar os itens da sua conta Azure Cosmos:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Criar um item

Um item pode ser criado usando `Items` a função de criação da classe. Quando está a utilizar a API SQL, os itens são projetados como documentos, que são conteúdo JSON definido pelo utilizador (arbitrário). Neste tutorial, cria um novo item dentro da base de dados de tarefas.

1. No ficheiro app.js, defina a definição do item:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Adicione o seguinte código para criar o item previamente definido:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Atualizar um item

A Azure Cosmos DB suporta a substituição do conteúdo dos itens. Copie e cole o seguinte código para o ficheiro *app.js.* Este código recebe um item do recipiente e atualiza o campo *isComplete* para verdade.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Eliminar um item

O Azure Cosmos DB suporta a eliminação de itens JSON. O código que se segue mostra como obter um item pelo seu ID e eliminá-lo. Copiar e colar o seguinte código para o ficheiro *app.js:*

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Executar a aplicação Node.js

Em conjunto, o código deverá ser semelhante a:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando:

```bash 
node app.js
```

Deverá ver o resultado da sua aplicação Introdução. A saída deve corresponder ao texto de exemplo abaixo.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Obter a solução completa do tutorial Node.js

Se não teve tempo de completar os passos deste tutorial, ou apenas descarregamento do código, pode obtê-lo no [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Para executar a solução de início de início que contém todo o código neste artigo, você precisará:

* Uma [conta do Azure Cosmos DB][create-account].
* A solução [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), disponível no GitHub.

Instale as dependências do projeto através do npm. Utilize o seguinte comando:

* ```npm install``` 

Em seguida, ```config.js``` no ficheiro, atualize os valores de config.endpoint e config.key conforme descrito no [Passo 3: Desloque as configurações da sua aplicação](#Config).  

Em seguida, no seu terminal, localize o ficheiro ```app.js``` e execute o comando:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando estes recursos já não são necessários, pode eliminar o grupo de recursos, a conta Azure Cosmos DB e todos os recursos relacionados. Para tal, selecione o grupo de recursos que utilizou para a conta Azure Cosmos DB, selecione **Delete,** e depois confirme o nome do grupo de recursos para apagar.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Monitorize uma conta Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
