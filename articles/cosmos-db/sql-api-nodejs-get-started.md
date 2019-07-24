---
title: Tutorial do node. js para a API do SQL para Azure Cosmos DB
description: Um tutorial do Node.js que demonstra como ligar e consultar o Azure Cosmos DB com a API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: ba1ec821bd25e3b9f4479c3d09fdf5ab981ab0a7
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305516"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Criar um aplicativo de console do node. js com o SDK do JavaScript para gerenciar Azure Cosmos DB dados da API do SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar uma conta da API do SQL no Azure Cosmos DB para armazenar e acessar esses dados do documento. Este tutorial mostra como criar um aplicativo de console do node. js para criar Azure Cosmos DB recursos e consultá-los.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie e conecte-se a uma conta de Azure Cosmos DB.
> * Configure seu aplicativo.
> * Crie um banco de dados.
> * Crie um contêiner.
> * Adicione itens ao contêiner.
> * Executar operações básicas nos itens, contêiner e banco de dados.

## <a name="prerequisites"></a>Pré-requisitos 

Certifique-se de que tem os recursos seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node. js](https://nodejs.org/) v 6.0.0 ou superior.

## <a name="create-azure-cosmos-db-account"></a>Criar conta de Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a aplicação Node.js](#SetupNode). Se estiver a utilizar o Emulador do Azure Cosmos DB, siga os passos em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e avance para [Configurar a aplicação Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Configurar seu aplicativo node. js

Antes de começar a escrever o código para criar o aplicativo, você pode criar a estrutura para seu aplicativo. Execute as seguintes etapas para configurar o aplicativo node. js que tem o código de estrutura:

1. Abra o seu terminal favorito.
2. Localize a pasta ou o diretório onde pretende guardar a sua aplicação Node.js.
3. Crie dois ficheiros JavaScript vazios com os seguintes comandos:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Criar e inicializar um `package.json` arquivo. Utilize o seguinte comando:
   * ```npm init -y```

5. Instale o módulo @azure/cosmos através do npm. Utilize o seguinte comando:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Definir as configurações do aplicativo

Agora que seu aplicativo existe, você precisa ter certeza de que ele pode se comunicar com Azure Cosmos DB. Atualizando algumas definições de configuração, conforme mostrado nas etapas a seguir, você pode definir seu aplicativo para se comunicar com Azure Cosmos DB:

1. Abra ```config.js``` no seu editor de texto favorito.

1. Copie e cole o fragmento de código abaixo e defina as propriedades ```config.endpoint``` e ```config.primaryKey``` como o URI e a chave primária do ponto final do Azure Cosmos DB. Pode encontrar ambas as configurações no [portal do Azure](https://portal.azure.com).

   ![Captura de ecrã para obter chaves do portal do Azure][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Copie e cole os dados de ```database```, ```container``` e ```items``` no objeto ```config```, por baixo do local onde define as propriedades ```config.endpoint``` e ```config.primaryKey```. Se você já tiver dados que gostaria de armazenar em seu banco de dado, poderá usar a ferramenta de migração de dados no Azure Cosmos DB em vez de definir os dados aqui. O arquivo config. js deve ter o seguinte código:

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   O SDK do JavaScript usa o *contêiner* e o *Item*de termos genéricos. Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. 
   
   `module.exports = config;`o código é usado para exportar ```config``` o objeto, para que você possa referenciá-lo ```app.js``` dentro do arquivo.

## <a id="Connect"></a>Conectar-se a uma conta de Azure Cosmos DB

1. Abra o seu ficheiro ```app.js``` vazio no editor de texto. Copie e cole o código abaixo para importar o módulo ```@azure/cosmos``` e o módulo ```config``` que criou recentemente.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Copie e cole o código para utilizar ```config.endpoint``` e ```config.primaryKey``` guardados anteriormente e criar um CosmosClient novo.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> Se estiver se conectando ao emulador de **Cosmos DB**, desabilite a verificação de SSL criando uma política de conexão personalizada.
>   ```
>   const ConnectionPolicy = require('@azure/cosmos').ConnectionPolicy;
>   const connectionPolicy = new ConnectionPolicy();
>   connectionPolicy.DisableSSLVerification = true;
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Agora que tem o código para inicializar o cliente do Azure Cosmos DB, vamos ver como utilizar os recursos do Azure Cosmos DB.

## <a name="create-a-database"></a>Criar uma base de dados

1. Copie e cole o código abaixo para definir a ID do banco de dados e a ID do contêiner. Essas IDs são como o cliente de Azure Cosmos DB encontrará o banco de dados e o contêiner corretos.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Um banco de dados pode ser criado usando a `createIfNotExists` função ou criar da classe  databases. As bases de dados são os contentores lógicos dos itens particionados em contentores. 

2. Copie e cole os métodos **createDatabase** e **readDatabase** no ficheiro app.js, abaixo da definição de ```databaseId``` e de ```containerId```. A  função CreateDatabase criará um novo banco de dados ```FamilyDatabase```com ID, especificado ```config``` a partir do objeto, se ele ainda não existir. A função **readDatabase** lê a definição da base de dados para garantir que a mesma existe.

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
      const { body: databaseDefinition } = await client.database(databaseId).read();
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
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

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

## <a id="CreateContainer"></a>Criar um contêiner

Em seguida, crie um contêiner dentro da conta de Azure Cosmos DB, para que você possa armazenar e consultar os dados. 

> [!WARNING]
> A criação de um contêiner tem implicações de preços. Visite nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para saber o que esperar.

Um contêiner pode ser criado usando a `createIfNotExists` função ou criar da classe **contêineres** .  Os contentores consistem em itens (que, no caso da API SQL, são documentos JSON) e na lógica da aplicação JavaScript associada.

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
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
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
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

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
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
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

Um item pode ser criado usando a função CREATE da classe **Items** . Quando você estiver usando a API do SQL, os itens serão projetados como documentos, que são conteúdo JSON definido pelo usuário (arbitrário). Pode agora inserir um item no Azure Cosmos DB.

1. Copie e cole a função **createFamilyItem** abaixo da função **readContainer**. A função **createFamilyItem** cria os itens que contêm os dados JSON guardados no objeto ```config```. Vamos verificar para garantir que um item com a mesma ID ainda não exista antes de criá-lo.

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


## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB dá suporte a consultas avançadas em documentos JSON armazenados em cada contêiner. O seguinte código de exemplo mostra uma consulta que pode executar nos documentos do seu contentor.

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

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).toArray();
    for (var queryResult of results) {
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


## <a id="ReplaceItem"></a>Substituir um item
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


## <a id="DeleteItem"></a>Excluir um item

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


## <a id="DeleteDatabase"></a>Excluir o banco de dados

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

## <a id="Run"></a>Executar seu aplicativo node. js

Em conjunto, o código deverá ser semelhante a:

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

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

Para executar a solução de introdução que contém todo o código deste artigo, será necessário: 

* Uma [conta de Azure Cosmos DB][create-account]. 
* A solução [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), disponível no GitHub. 

Instale as dependências do projeto via NPM. Utilize o seguinte comando: 

* ```npm install``` 

Em seguida, no ```config.js``` arquivo, atualize os valores config. EndPoint e config. PrimaryKey, conforme descrito [na etapa 3: Defina as configurações](#Config)do aplicativo.  

Em seguida, no seu terminal, localize o ficheiro ```app.js``` e execute o comando:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando esses recursos não forem mais necessários, você poderá excluir o grupo de recursos, Azure Cosmos DB conta e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos que você usou para a conta de Azure Cosmos DB, selecione **excluir**e confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Monitorar uma conta de Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
