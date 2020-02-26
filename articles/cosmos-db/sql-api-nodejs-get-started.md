---
title: Tutorial de Node.js para a SQL API para Azure Cosmos DB
description: Um tutorial do Node.js que demonstra como ligar e consultar o Azure Cosmos DB com a API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 5204c7cf80a5b52b0c30b3165d522aa2648cd95c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587435"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Construa uma aplicação de consola Node.js com o JavaScript SDK para gerir os dados da API Do Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
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

## <a id="SetupNode"></a>Configurar a sua aplicação Node.js

Antes de começar a escrever código para construir a aplicação, pode construir o quadro para a sua aplicação. Execute os seguintes passos para configurar a sua aplicação Node.js que tem o código-quadro:

1. Abra o seu terminal favorito.
2. Localize a pasta ou o diretório onde pretende guardar a sua aplicação Node.js.
3. Crie dois ficheiros JavaScript vazios com os seguintes comandos:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Crie e inicialize um ficheiro `package.json`. Utilize o seguinte comando:
   * ```npm init -y```

5. Instale o módulo @azure/cosmos através do npm. Utilize o seguinte comando:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Detete as configurações da sua aplicação

Agora que a sua aplicação existe, tem de se certificar de que pode falar com o Azure Cosmos DB. Ao atualizar algumas definições de configuração, como mostrado nos seguintes passos, pode definir a sua aplicação para falar com o Azure Cosmos DB:

1. Abra ```config.js``` no seu editor de texto favorito.

1. Copie e cole o fragmento de código abaixo e defina as propriedades ```config.endpoint``` e ```config.key``` como o URI e a chave primária do ponto final do Azure Cosmos DB. Pode encontrar ambas as configurações no [portal do Azure](https://portal.azure.com).

   ![Captura de ecrã para obter chaves do portal do Azure][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.key = "~your primary key here~";
   ``` 

1. Copie e cole os dados de ```database```, ```container``` e ```items``` no objeto ```config```, por baixo do local onde define as propriedades ```config.endpoint``` e ```config.key```. Se já tem dados que gostaria de armazenar na sua base de dados, pode utilizar a ferramenta Data Migration em Azure Cosmos DB em vez de definir os dados aqui. O ficheiro config.js deve ter o seguinte código:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   JavaScript SDK utiliza o *recipiente* de termos genéricos e *o item*. Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. 
   
   `module.exports = config;` código é usado para exportar o seu objeto ```config```, para que possa referenciar no ficheiro ```app.js```.

## <a id="Connect"></a>Ligue-se a uma conta Azure Cosmos DB

1. Abra o seu ficheiro ```app.js``` vazio no editor de texto. Copie e cole o código abaixo para importar o módulo ```@azure/cosmos``` e o módulo ```config``` que criou recentemente.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Copie e cole o código para utilizar ```config.endpoint``` e ```config.key``` guardados anteriormente e criar um CosmosClient novo.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });
   ```
   
> [!Note]
> Se ligar ao **Emulador Cosmos DB,** desative a verificação SSL para o seu processo de nó:
>   ```
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Agora que tem o código para inicializar o cliente do Azure Cosmos DB, vamos ver como utilizar os recursos do Azure Cosmos DB.

## <a name="create-a-database"></a>Criar uma base de dados

1. Copie e cole o código abaixo para definir o ID da base de dados e o ID do recipiente. Estas identificações são como o cliente da Azure Cosmos DB vai encontrar a base de dados e o contentor certos.

   ```javascript
   const client = new CosmosClient({ endpoint, key });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Uma base de dados pode ser criada utilizando o `createIfNotExists` ou criar função da classe Bases de **Dados.** As bases de dados são os contentores lógicos dos itens particionados em contentores. 

2. Copie e cole os métodos **createDatabase** e **readDatabase** no ficheiro app.js, abaixo da definição de ```databaseId``` e de ```containerId```. A função **createDatabase** criará uma nova base de dados com id ```FamilyDatabase```, especificada a partir do ```config``` objeto se ainda não existir. A função **readDatabase** lê a definição da base de dados para garantir que a mesma existe.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { resource: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Copie e cole o código por baixo do local onde definiu as funções **createDatabase** e **readDatabase** para adicionar a função de programa auxiliar **exit**, que vai imprimir a mensagem de saída. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Copie e cole o código abaixo de onde definiu a função **exit** para chamar as funções **createDatabase** e **readDatabase**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Neste momento, o código em ```app.js``` deve ser semelhante ao código a seguir:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { resource: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Criar um recipiente

Em seguida, crie um recipiente dentro da conta Azure Cosmos DB, para que possa armazenar e consultar os dados. 

> [!WARNING]
> A criação de um contentor tem implicações nos preços. Visite [a](https://azure.microsoft.com/pricing/details/cosmos-db/) nossa página de preços para que saiba o que esperar.

Um recipiente pode ser criado utilizando a `createIfNotExists` ou criar função a partir da classe **Contentores.**  Os contentores consistem em itens (que, no caso da API SQL, são documentos JSON) e na lógica da aplicação JavaScript associada.

1. Copie e cole as funções **createContainer** e **readContainer** abaixo da função **readDatabase**, no ficheiro app.js. A função **createContainer** cria um contentor novo com ```containerId```, especificado a partir do objeto ```config```, se ainda não existir. A função **readContainer** lê a definição do contentor para verificar se o mesmo existe.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Copie e cole o código abaixo da chamada para **readDatabase** par executar as funções **createContainer** e **readContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Neste momento, o código em ```app.js``` deve ser semelhante a:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Criar um item

Um item pode ser criado utilizando a função de criação da classe **Itens.** Quando está a utilizar a API SQL, os itens são projetados como documentos, que são conteúdo JSON definido pelo utilizador (arbitrário). Pode agora inserir um item no Azure Cosmos DB.

1. Copie e cole a função **createFamilyItem** abaixo da função **readContainer**. A função **createFamilyItem** cria os itens que contêm os dados JSON guardados no objeto ```config```. Vamos verificar se um item com a mesma identificação já não existe antes de o criar.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. Copie e cole o código por baixo da chamada para **readContainer** para executar a função **createFamilyItem**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Recursos DB da Consulta Azure Cosmos

A Azure Cosmos DB suporta ricas consultas contra documentos JSON armazenados em cada contentor. O seguinte código de exemplo mostra uma consulta que pode executar nos documentos do seu contentor.

1. Copie e cole a função **queryContainer** abaixo da função **createFamilyItem**, no ficheiro app.js. O Azure Cosmos DB suporta consultas de tipo SQL, conforme mostrado abaixo.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { resources } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).fetchAll();
    for (var queryResult of resources) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Copie e cole o código por baixo das chamadas para **createFamilyItem** para executar a função **queryContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Substitua um item
O Azure Cosmos DB suporta a substituição do conteúdo dos itens.

1. Copie e cole a função **replaceFamilyItem** abaixo da função **queryContainer**, no ficheiro app.js. Repare que alterámos a propriedade “grade” de uma criança do valor original de 5 para 6.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. Copie e cole o código por baixo da chamada para **queryContainer** para executar a função **replaceFamilyItem**. Além disso, adicione o código para chamar novamente **queryContainer**, para confirmar que o item foi alterado com êxito.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Apagar um item

O Azure Cosmos DB suporta a eliminação de itens JSON.

1. Copie e cole a função **deleteFamilyItem** por baixo da função **replaceFamilyItem**.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Copie e cole o código por baixo da chamada da segunda **queryContainer** para executar a função **deleteFamilyItem**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Apagar a base de dados

Eliminar a base de dados criada remove a base de dados e todos os recursos subordinados (contentores, itens, etc.).

1. Copie e cole a função **cleanup** por baixo da função **deleteFamilyItem** para remover a base de dados e todos os recursos subordinados.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Copie e cole o código por baixo da chamada para **deleteFamilyItem** para executar a função **cleanup**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Executar a sua aplicação Node.js

Em conjunto, o código deverá ser semelhante a:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: 

```bash 
node app.js
```

Deverá ver o resultado da sua aplicação Introdução. A saída deve corresponder ao texto de exemplo abaixo.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Obter a solução completa do tutorial Node.js 

Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir o código, pode obtê-lo a partir do [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Para executar a solução de início de início que contém todo o código neste artigo, você precisará: 

* Uma [conta Azure Cosmos DB.][create-account] 
* A solução [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), disponível no GitHub. 

Instale as dependências do projeto através do npm. Utilize o seguinte comando: 

* ```npm install``` 

Em seguida, no ficheiro ```config.js```, atualize os valores de config.endpoint e config.key conforme descrito no Passo 3: Definir as [configurações da sua aplicação](#Config).  

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
