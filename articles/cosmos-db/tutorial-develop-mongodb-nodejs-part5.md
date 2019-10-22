---
title: Criar um aplicativo angular com a API do Azure Cosmos DB para MongoDB-use Mongoose para se conectar ao Cosmos DB
titleSuffix: Azure Cosmos DB
description: Este tutorial descreve como criar um aplicativo node. js usando o angular e o Express para gerenciar os dados armazenados no Cosmos DB. Nesta parte, você usa o Mongoose para se conectar ao Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Node.js application, so that I can manage the data stored in Cosmos DB.
ms.openlocfilehash: 626015e2aac5eb09dfd271a139dbc5eb49a088fc
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69616425"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Criar um aplicativo angular com a API do Azure Cosmos DB para MongoDB-use Mongoose para se conectar ao Cosmos DB

Este tutorial de várias partes demonstra como criar um aplicativo node. js com o Express e o angular e conectá-lo a ele à sua [conta do cosmos configurada com a API do cosmos DB para MongoDB](mongodb-introduction.md). Este artigo descreve a parte 5 do tutorial e baseia-se na [parte 4](tutorial-develop-mongodb-nodejs-part4.md).

Nesta parte do tutorial, você vai:

> [!div class="checklist"]
> * Use Mongoose para se conectar ao Cosmos DB.
> * Obtenha sua cadeia de conexão Cosmos DB.
> * Crie o modelo Hero.
> * Crie o serviço Hero para obter dados do Hero.
> * Execute a aplicação localmente.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Antes de iniciar este tutorial, conclua as etapas na [parte 4](tutorial-develop-mongodb-nodejs-part4.md).

* Este tutorial requer que você execute o CLI do Azure localmente. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar o CLI do Azure, consulte [instalar o CLI do Azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Este tutorial orienta você pelas etapas para criar o aplicativo passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="use-mongoose-to-connect"></a>Usar o Mongoose para se conectar

Mongoose é uma biblioteca de ODM (modelagem de dados de objeto) para MongoDB e node. js. Você pode usar o Mongoose para se conectar à sua conta do Azure Cosmos DB. Use as etapas a seguir para instalar o Mongoose e conectar-se ao Azure Cosmos DB:

1. Instale o módulo Mongoose NPM, que é uma API usada para conversar com o MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Na pasta do **servidor** , crie um arquivo chamado **Mongo. js**. Você adicionará os detalhes de conexão de sua conta do Azure Cosmos DB a esse arquivo.

1. Copie o código a seguir no arquivo **Mongo. js** . O código fornece a seguinte funcionalidade:

   * Requer o Mongoose.
   * Substitui a promessa de Mongo de usar a promessa básica incorporada ao ES6/ES2015 e versões posteriores.
   * Chamadas em um arquivo env que permite configurar certas coisas com base no fato de você estar em preparo, produção ou desenvolvimento. Você criará esse arquivo na próxima seção.
   * Inclui a cadeia de conexão do MongoDB, que é definida no arquivo env.
   * Cria uma função connect que chama o Mongoose.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. No painel do Explorer, em **servidor**, crie uma pasta chamada **ambiente**. Na pasta **ambiente** , crie um arquivo chamado **Environment. js**.

1. No arquivo Mongo. js, precisamos incluir valores para o `dbName`, o `key` e os parâmetros de `cosmosPort`. Copie o código a seguir no arquivo **Environment. js** :

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Obter a cadeia de ligação

Para conectar seu aplicativo ao Azure Cosmos DB, você precisa atualizar as definições de configuração para o aplicativo. Use as etapas a seguir para atualizar as configurações: 

1. Na portal do Azure, obtenha o número da porta, o nome da conta de Azure Cosmos DB e os valores de chave primária para sua conta de Azure Cosmos DB.

1. No arquivo **Environment. js** , altere o valor de `port` para 10255. 

    ```javascript
    const port = 10255;
    ```

1. No arquivo **Environment. js** , altere o valor de `accountName` para o nome da conta de Azure Cosmos DB que você criou na [parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial. 

1. Obtenha a chave primária da conta do Azure Cosmos DB ao introduzir o comando da CLI seguinte na janela do terminal: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-Name > é o nome da conta de Azure Cosmos DB que você criou na [parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

1. Copie a chave primária no arquivo **Environment. js** como o valor `key`.

Agora seu aplicativo tem todas as informações necessárias para se conectar ao Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Criar um modelo de hero

Em seguida, você precisa definir o esquema dos dados a serem armazenados no Azure Cosmos DB definindo um arquivo de modelo. Use as etapas a seguir para criar um _modelo Hero_ que define o esquema dos dados:

1. No painel Gerenciador, na pasta **servidor** , crie um arquivo chamado **Hero. Model. js**.

1. Copie o código a seguir no arquivo **Hero. Model. js** . O código fornece a seguinte funcionalidade:

   * Requer o Mongoose.
   * Cria um esquema novo com ID, nome e mensagem.
   * Cria um modelo usando o esquema.
   * Exporta o modelo. 
   * Nomeia o **herói** da coleção (em vez de **heróis**, que é o nome padrão da coleção com base nas regras de nomenclatura do Mongoose plural).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Criar um modelo de hero

Depois de criar o modelo Hero, você precisa definir um serviço para ler os dados e executar operações de lista, criação, exclusão e atualização. Use as etapas a seguir para criar um _serviço Hero_ que consulta os dados de Azure Cosmos DB:

1. No painel Gerenciador, na pasta **servidor** , crie um arquivo chamado **Hero. Service. js**.

1. Copie o código a seguir no arquivo **Hero. Service. js** . O código fornece a seguinte funcionalidade:

   * Obtém o modelo que você criou.
   * Conecta-se ao banco de dados.
   * Cria uma variável `docquery` que usa o método `hero.find` para definir uma consulta que retorna todos os Heroes.
   * Executa uma consulta com a função `docquery.exec` com uma promessa para obter uma lista de todos os Heroes, onde o status da resposta é 200. 
   * Retornará a mensagem de erro se o status for 500.
   * Obtém o Hero porque estamos usando módulos. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Configurar rotas

Em seguida, você precisa configurar rotas para lidar com as URLs para solicitações GET, Create, Read e Delete. Os métodos de roteamento especificam funções de retorno de chamada (também chamadas de _funções de manipulador_). Essas funções são chamadas quando o aplicativo recebe uma solicitação para o ponto de extremidade e o método HTTP especificados. Use as etapas a seguir para adicionar o serviço Hero e definir suas rotas:

1. No Visual Studio Code, no arquivo **routes. js** , comente a função `res.send` que envia os dados do herói de exemplo. Adicione uma linha para chamar a função `heroService.getHeroes` em vez disso.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. No arquivo **routes. js** , `require` o serviço Hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. No arquivo **Hero. Service. js** , atualize a função `getHeroes` para obter os parâmetros `req` e `res` da seguinte maneira:

    ```javascript
    function getHeroes(req, res) {
    ```

Vamos revisar um minuto para examinar e percorrer o código anterior. Primeiro, entramos no arquivo index. js, que configura o servidor do nó. Observe que ele configura e define suas rotas. Em seguida, seu arquivo routes. js conversa com o serviço Hero e o informa para obter suas funções, como **Getheros**, e passar a solicitação e a resposta. O arquivo Hero. Service. js Obtém o modelo e se conecta ao Mongo. Em seguida, ele executa **Getheros** quando o chamamos e retorna uma resposta de 200. 

## <a name="run-the-app"></a>Executar a aplicação

Em seguida, execute o aplicativo usando as seguintes etapas:

1. Em Visual Studio Code, salve todas as suas alterações. À esquerda, selecione o botão de **depuração** ![Debug ícone em Visual Studio Code ](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) e, em seguida, selecione o botão **Iniciar Depuração** ![Debug ícone em Visual Studio Code ](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

1. Agora, alterne para o navegador. Abra as **ferramentas de desenvolvedor** e a **guia rede**. Vá para `http://localhost:3000`, e você verá nosso aplicativo.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Ainda não há Heroes armazenados no aplicativo. Na próxima parte deste tutorial, adicionaremos a funcionalidade put, push e Delete. Em seguida, podemos adicionar, atualizar e excluir Heroes da interface do usuário usando conexões Mongoose ao nosso banco de dados Cosmos do Azure. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos, poderá excluir o grupo de recursos, Azure Cosmos DB conta e todos os recursos relacionados. Use as seguintes etapas para excluir o grupo de recursos:

 1. Vá para o grupo de recursos em que você criou a conta de Azure Cosmos DB.
 1. Selecione **Eliminar grupo de recursos**.
 1. Confirme o nome do grupo de recursos a ser excluído e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Continue na parte 6 do tutorial para adicionar funções post, PUT e Delete ao aplicativo:

> [!div class="nextstepaction"]
> [Parte 6: adicionar funções post, PUT e Delete ao aplicativo](tutorial-develop-mongodb-nodejs-part6.md)
