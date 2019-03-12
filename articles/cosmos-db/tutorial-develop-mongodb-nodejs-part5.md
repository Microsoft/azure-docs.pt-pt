---
title: Criar uma aplicação Angular com API do Azure Cosmos DB para o MongoDB - utilizar o Mongoose para ligar ao Cosmos DB
titleSuffix: Azure Cosmos DB
description: Este tutorial descreve como criar uma aplicação de node. js com o Angular e o Express para gerir os dados armazenados no Cosmos DB. Nesta parte, vai utilizar o Mongoose para ligar ao Azure Cosmos DB.
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
ms.openlocfilehash: 59ba69f8bf208ab4f38ae326cf4297947f0b844e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547117"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Criar uma aplicação Angular com API do Azure Cosmos DB para o MongoDB - utilizar o Mongoose para ligar ao Cosmos DB

Este tutorial com várias parte demonstra como criar uma aplicação de node. js com Express e o Angular e ligá-la ao mesmo para sua [conta do Cosmos configurada com a API do Cosmos DB para o MongoDB](mongodb-introduction.md). Este artigo descreve parte 5 do tutorial e baseia-se nas [parte 4](tutorial-develop-mongodb-nodejs-part4.md).

Nesta parte do tutorial, irá:

> [!div class="checklist"]
> * Utilizar o Mongoose para ligar ao Cosmos DB.
> * Obter a cadeia de ligação do Cosmos DB.
> * Crie o modelo de Hero.
> * Crie o serviço de Hero para obter dados de Hero.
> * Execute a aplicação localmente.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Antes de começar este tutorial, conclua os passos na [parte 4](tutorial-develop-mongodb-nodejs-part4.md).

* Este tutorial requer que execute a CLI do Azure localmente. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI do Azure, veja [instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Este tutorial explica os passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="use-mongoose-to-connect"></a>Utilizar o Mongoose para ligar

O mongoose é um modelagem de biblioteca (ODM) para MongoDB e node. js de dados de objeto. Pode utilizar o Mongoose para ligar à sua conta do Azure Cosmos DB. Utilize os seguintes passos para instalar o Mongoose e ligar ao Azure Cosmos DB:

1. Instale o módulo de npm do mongoose, que é uma API que é utilizada para comunicar com o MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Na **servidor** pasta, crie um ficheiro denominado **mongo**. Adicionará os detalhes de ligação da sua conta do Azure Cosmos DB para este ficheiro.

1. Copie o seguinte código para o **mongo** ficheiro. O código fornece as seguintes funcionalidades:

    * Requer o Mongoose.
    * Substitui a promessa do Mongo para utilizar a promessa básica que está incorporada no versões ES6/ES2015 e posteriores.
    * Chamadas de um ficheiro env que lhe permite configurar determinadas coisas consoante esteja em teste, produção ou desenvolvimento. Irá criar esse arquivo na próxima seção.
    * Inclui a cadeia de ligação do MongoDB, que é definida no ficheiro env.
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
    
1. No painel do Explorer, em **servidor**, crie uma pasta denominada **ambiente**. Na **ambiente** pasta, crie um ficheiro denominado **Environment**.

1. Ficheiro mongo js, é necessário incluir os valores para o `dbName`, o `key`e o `cosmosPort` parâmetros. Copie o seguinte código para o **Environment** ficheiro:

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

Para ligar a aplicação do Azure Cosmos DB, terá de atualizar as definições de configuração para a aplicação. Utilize os seguintes passos para atualizar as definições: 

1. No portal do Azure, obtenha o número de porta, nome da conta do Azure Cosmos DB e os valores de chave primários para a sua conta do Azure Cosmos DB.

1. Na **Environment** de ficheiros, altere o valor de `port` para 10255. 

    ```javascript
    const port = 10255;
    ```

1. Na **Environment** de ficheiros, altere o valor de `accountName` para o nome da conta do Azure Cosmos DB que criou no [parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial. 

1. Obtenha a chave primária da conta do Azure Cosmos DB ao introduzir o comando da CLI seguinte na janela do terminal: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name > é o nome da conta do Azure Cosmos DB que criou no [parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

1. Copie a chave primária para o **Environment** file como o `key` valor.

Agora a aplicação tem todas as informações necessárias para ligar ao Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Criar um modelo de hero

Em seguida, terá de definir o esquema dos dados para armazenar no Azure Cosmos DB com a definição de um ficheiro de modelo. Utilize os seguintes passos para criar uma _modelo de Hero_ que define o esquema dos dados:

1. No painel do Explorer, sob o **servidor** pasta, crie um ficheiro denominado **Hero**.

1. Copie o seguinte código para o **Hero** ficheiro. O código fornece as seguintes funcionalidades:

   * Requer o Mongoose.
   * Cria um esquema novo com ID, nome e mensagem.
   * Cria um modelo com o esquema.
   * Exporta o modelo. 
   * A coleção de nomes **Heroes** (em vez de **Heros**, que é o nome predefinido da coleção com base em regras de nomenclatura plurais do Mongoose).

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

Depois de criar o modelo de hero, precisa definir um serviço para ler os dados e executar a lista, criar, eliminar e operações de atualização. Utilize os seguintes passos para criar uma _serviço Hero_ que consulta os dados do Azure Cosmos DB:

1. No painel do Explorer, sob o **servidor** pasta, crie um ficheiro denominado **hero.service.js**.

1. Copie o seguinte código para o **hero.service.js** ficheiro. O código fornece as seguintes funcionalidades:

   * Obtém o modelo que criou.
   * Liga-se à base de dados.
   * Cria um `docquery` variável que utiliza o `hero.find` método para definir uma consulta que devolve todos os heroes.
   * Executa uma consulta com o `docquery.exec` função com uma promessa de obter uma lista de todos os heroes, onde o estado de resposta é 200. 
   * Envia a mensagem de erro se o estado é 500.
   * Obtém os heroes porque estamos usando módulos. 

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

## <a name="configure-routes"></a>Configurar as rotas

Em seguida, precisa configurar rotas para lidar com os URLs para get, criação, leitura e pedidos de eliminação. Os métodos de encaminhamento de especificar as funções de retorno de chamada (também denominado _funções do manipulador_). Essas funções são chamadas quando o aplicativo recebe um pedido para o ponto final especificado e o método HTTP. Utilize os seguintes passos para adicionar o serviço de Hero e definir as rotas:

1. No Visual Studio Code, no **ROUTES. js** ficheiro, comente o `res.send` função que envia os dados de hero de exemplo. Adicionar uma linha para chamar o `heroService.getHeroes` funcionar em vez disso.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. Na **ROUTES. js** arquivo, `require` o serviço de hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. Na **hero.service.js** de ficheiros, atualize o `getHeroes` função para tirar o `req` e `res` parâmetros da seguinte forma:

    ```javascript
    function getHeroes(req, res) {
    ```

Vamos dispensar um minuto para rever e examinar o código anterior. Em primeiro lugar, temos o ficheiro Index js, que configura o servidor de nó. Observe que configura e define suas rotas. Em seguida, o ficheiro ROUTES. js comunica com o serviço de hero e diz-lhe obter as suas funções, como **getHeroes**e transmitir o pedido e resposta. O ficheiro de hero.service.js obtém o modelo e liga ao Mongo. Em seguida, ele executa **getHeroes** quando vamos chamá-lo e voltar a devolve 200 como resposta. 

## <a name="run-the-app"></a>Executar a aplicação

Em seguida, execute a aplicação utilizando os seguintes passos:

1. No Visual Studio Code, guarde todas as alterações. No lado esquerdo, selecione o **depurar** botão ![ícone de depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)e, em seguida, selecione o **iniciar depuração** botão ![ícone de depuração no Visual Studio Code ](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

1. Agora, mude para o navegador. Abra o **ferramentas de programação** e o **separador rede**. Aceda a `http://localhost:3000`, e há ver nosso aplicativo.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Não existem heroes armazenados ainda na aplicação. A parte seguinte deste tutorial, vamos adicionar put, push e funcionalidade de eliminação. Em seguida, podemos pode adicionar, atualizar e eliminar heroes a partir da interface do Usuário, utilizando ligações do Mongoose à nossa base de dados do Azure Cosmos DB. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos, pode eliminar o grupo de recursos, conta do Azure Cosmos DB e todos os recursos relacionados. Utilize os seguintes passos para eliminar o grupo de recursos:

 1. Vá para o grupo de recursos onde criou a conta do Azure Cosmos DB.
 1. Selecione **Eliminar grupo de recursos**.
 1. Confirme o nome do grupo de recursos a eliminar e selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Continuar a parte 6 do tutorial para adicionar Post, Put e eliminar as funções para a aplicação:

> [!div class="nextstepaction"]
> [Parte 6: Adicionar Post, Put e eliminar as funções para a aplicação](tutorial-develop-mongodb-nodejs-part6.md)
