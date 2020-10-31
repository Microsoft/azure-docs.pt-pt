---
title: Ligue a app Angular à API da Azure Cosmos DB para a MongoDB usando o Mongoose
description: Este tutorial descreve como construir uma aplicação Node.js utilizando angular e express para gerir os dados armazenados em Cosmos DB. Nesta parte, você usa Mongoose para ligar-se ao Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097809"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Crie uma aplicação angular com API da Azure Cosmos DB para o MongoDB - Use o Mongoose para ligar à Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este tutorial multi-partes demonstra como criar uma app Node.js com a Express e a Angular, e conectá-la à sua [conta Cosmos configurada com a API da Cosmos DB para a MongoDB.](mongodb-introduction.md) Este artigo descreve a Parte 5 do tutorial e baseia-se na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Use mongoose para ligar ao Cosmos DB.
> * Obtenha a sua cadeia de ligação Cosmos DB.
> * Crie o modelo Herói.
> * Crie o serviço Hero para obter dados de Herói.
> * Execute a aplicação localmente.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Antes de iniciar este tutorial, complete os passos na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

* Este tutorial requer que você gere o Azure CLI localmente. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se necessitar de instalar ou atualizar o Azure CLI, consulte [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

* Este tutorial leva-o pelos passos para construir a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="use-mongoose-to-connect"></a>Use mongoose para ligar

Mongoose é uma biblioteca de modelação de dados de objetos (ODM) para MongoDB e Node.js. Pode utilizar o Mongoose para se ligar à sua conta DB Azure Cosmos. Utilize os seguintes passos para instalar a Mongoose e ligar-se à Azure Cosmos DB:

1. Instale o módulo npm de mangusto, que é uma API que é usada para falar com o MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Na pasta do **servidor,** crie um ficheiro nomeado **mongo.js** . Irá adicionar os detalhes de ligação da sua conta DB Azure Cosmos a este ficheiro.

1. Copie o seguinte código no ficheiro **mongo.js.** O código fornece a seguinte funcionalidade:

   * Requer o Mongoose.
   * Sobrepõe-se à promessa da Mongo de usar a promessa básica que está incorporada na ES6/ES2015 e versões posteriores.
   * Liga para um ficheiro env que permite configurar certas coisas com base na encenação, produção ou desenvolvimento. Vais criar esse ficheiro na próxima secção.
   * Inclui a cadeia de ligação MongoDB, que está definida no ficheiro env.
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
    
1. No painel Explorer, sob **servidor,** crie uma pasta chamada **ambiente** . Na **pasta** ambiente, crie um ficheiro chamado **environment.js** .

1. A partir do ficheiro mongo.js, precisamos de incluir valores para `dbName` o, o `key` , e os `cosmosPort` parâmetros. Copie o seguinte código no ficheiro **environment.js:**

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

Para ligar a sua aplicação ao Azure Cosmos DB, tem de atualizar as definições de configuração da aplicação. Utilize os seguintes passos para atualizar as definições: 

1. No portal Azure, obtenha o número da porta, o nome da conta DB Azure Cosmos e os valores-chave principais para a sua conta DB Azure Cosmos.

1. No **ficheiroenvironment.js,** altere o valor para `port` 10255. 

    ```javascript
    const port = 10255;
    ```

1. No ficheiro **environment.js,** altere o valor da `accountName` conta DB Azure Cosmos que criou na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial. 

1. Obtenha a chave primária da conta do Azure Cosmos DB ao introduzir o comando da CLI seguinte na janela do terminal: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> é o nome da conta DB Azure Cosmos que criou na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

1. Copie a chave primária para o ficheiro **environment.js** como `key` o valor.

Agora a sua aplicação tem todas as informações necessárias para ligar à Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Criar um modelo de hero

Em seguida, é necessário definir o esquema dos dados para armazenar no Azure Cosmos DB definindo um ficheiro modelo. Utilize os seguintes passos para criar um _modelo Hero_ que define o esquema dos dados:

1. No painel Explorer, sob a pasta do **servidor,** crie um ficheiro nomeado **hero.model.js** .

1. Copie o seguinte código no ficheiro **hero.model.js.** O código fornece a seguinte funcionalidade:

   * Requer o Mongoose.
   * Cria um esquema novo com ID, nome e mensagem.
   * Cria um modelo utilizando o esquema.
   * Exporta o modelo. 
   * Nomeia a coleção **Heroes** (em vez de **Heros,** que é o nome padrão da coleção baseada nas regras de nomeação plural de Mongoose).

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

Depois de criar o modelo de herói, precisa de definir um serviço para ler os dados e executar a lista, criar, excluir e atualizar operações. Use os seguintes passos para criar um _serviço Hero_ que questione os dados da Azure Cosmos DB:

1. No painel Explorer, sob a pasta do **servidor,** crie um ficheiro nomeado **hero.service.js** .

1. Copie o seguinte código no ficheiro **hero.service.js.** O código fornece a seguinte funcionalidade:

   * Obtém o modelo que criaste.
   * Liga-se à base de dados.
   * Cria uma `docquery` variável que usa o `hero.find` método para definir uma consulta que devolve todos os heróis.
   * Executa uma consulta com a `docquery.exec` função com a promessa de obter uma lista de todos os heróis, onde o estado de resposta é 200. 
   * Envia de volta a mensagem de erro se o estado for 500.
   * Apanha os heróis porque estamos a usar módulos. 

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

## <a name="configure-routes"></a>Rotas deconfigure

Em seguida, você precisa definir rotas para lidar com os URLs para obter, criar, ler e apagar pedidos. Os métodos de encaminhamento especificam funções de retorno (também chamadas _funções de manipulador)._ Estas funções são chamadas quando a aplicação recebe um pedido para o ponto final especificado e método HTTP. Use os seguintes passos para adicionar o serviço Herói e definir as suas rotas:

1. No Código do Estúdio Visual, no ficheiro **routes.js,** comente a `res.send` função que envia os dados do herói da amostra. Adicione uma linha para chamar a `heroService.getHeroes` função.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. No ficheiro **routes.js,** `require` o serviço de heróis:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. No ficheiro **hero.service.js,** atualize a `getHeroes` função para tomar os `req` parâmetros e `res` os seguintes:

    ```javascript
    function getHeroes(req, res) {
    ```

Vamos tirar um minuto para rever e passar pelo código anterior. Primeiro, entramos no ficheiro index.js, que configura o servidor de nó. Note que se configura e define as suas rotas. Em seguida, o seu routes.js arquivar conversas com o serviço de heróis e diz-lhe para obter as suas funções, como **getHeroes** , e passar o pedido e resposta. O ficheiro hero.service.js recebe o modelo e liga-se à Mongo. Depois executa **o "GetHeroes"** quando lhe chamamos, e devolve uma resposta de 200. 

## <a name="run-the-app"></a>Executar a aplicação

Em seguida, executar a aplicação usando os seguintes passos:

1. No Código do Estúdio Visual, guarde todas as suas alterações. À esquerda, selecione o botão **Debug** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: e, em seguida, selecione o botão **Iniciar a depuração** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: .

1. Agora mude para o navegador. Abra as **ferramentas do Programador** e o **separador Rede.** Vá `http://localhost:3000` para, e aí você vê a nossa aplicação.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Nova conta do Azure Cosmos DB no portal do Azure":::

Ainda não há heróis armazenados na aplicação. Na próxima parte deste tutorial, adicionaremos a funcionalidade de colocar, empurrar e eliminar. Em seguida, podemos adicionar, atualizar e apagar heróis da UI usando ligações Mongoose para a nossa base de dados Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos, pode eliminar o grupo de recursos, a conta DB da Azure Cosmos e todos os recursos relacionados. Utilize os seguintes passos para eliminar o grupo de recursos:

 1. Vá ao grupo de recursos onde criou a conta DB da Azure Cosmos.
 1. Selecione **Eliminar grupo de recursos** .
 1. Confirme o nome do grupo de recursos para eliminar e selecione **Delete** .

## <a name="next-steps"></a>Passos seguintes

Continue a parte 6 do tutorial para adicionar funções de Post, Put e Delete à aplicação:

> [!div class="nextstepaction"]
> [Parte 6: Adicionar funções de Post, Put e Delete à aplicação](tutorial-develop-mongodb-nodejs-part6.md)