---
title: Ligue a app Angular à API da Azure Cosmos DB para mongoDB usando mongoose
description: Este tutorial descreve como construir uma aplicação Node.js usando angular e Expresso para gerir os dados armazenados em Cosmos DB. Nesta parte, você usa Mongoose para se conectar com Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: fb0e08c9d85f4472a2fa13001e71edd12149d430
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858729"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Crie uma app Angular com a API da Azure Cosmos DB para mongoDB - Use Mongoose para ligar ao Cosmos DB

Este tutorial em várias partes demonstra como criar uma app Node.js com Express e Angular, e conectá-la à sua [conta Cosmos configurada com a API da Cosmos DB para MongoDB.](mongodb-introduction.md) Este artigo descreve a Parte 5 do tutorial e baseia-se na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

Nesta parte do tutorial, irá:

> [!div class="checklist"]
> * Use Mongoose para ligar ao Cosmos DB.
> * Pegue a sua corda de ligação Cosmos DB.
> * Crie o modelo herói.
> * Crie o serviço De Herói para obter dados de Herói.
> * Execute a aplicação localmente.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Antes de iniciar este tutorial, complete os passos na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

* Este tutorial requer que você dirija o Azure CLI localmente. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar o Azure CLI, consulte [Instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Este tutorial acompanha-o através dos degraus para construir a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="use-mongoose-to-connect"></a>Use Mongoose para ligar

Mongoose é uma biblioteca de modelação de dados de objetos (ODM) para MongoDB e Node.js. Pode usar a Mongoose para se ligar à sua conta Azure Cosmos DB. Utilize os seguintes passos para instalar a Mongoose e ligar-se ao Azure Cosmos DB:

1. Instale o módulo npm mongoose, que é uma API que é usada para falar com mongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Na pasta do **servidor,** crie um ficheiro chamado **mongo.js**. Você vai adicionar os detalhes de ligação da sua conta Azure Cosmos DB a este ficheiro.

1. Copie o seguinte código no ficheiro **mongo.js.** O código fornece a seguinte funcionalidade:

   * Requer o Mongoose.
   * Sobrepõe-se à promessa de Mongo de usar a promessa básica que está incorporada em versões ES6/ES2015 e versões posteriores.
   * Chama um ficheiro ENV que permite configurar certas coisas com base em se está em encenação, produção ou desenvolvimento. Vais criar esse ficheiro na próxima secção.
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
    
1. No painel explorer, sob **o servidor,** crie uma pasta chamada **ambiente**. Na pasta do **ambiente,** crie um ficheiro chamado **ambiente.js**.

1. A partir do ficheiro mongo.js, precisamos incluir valores para os, `dbName`os `key`e os `cosmosPort` parâmetros. Copie o seguinte código no ficheiro **ambiente.js:**

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

Para ligar a sua aplicação ao Azure Cosmos DB, é necessário atualizar as definições de configuração da aplicação. Utilize os seguintes passos para atualizar as definições: 

1. No portal Azure, obtenha o número de porta, nome da conta Azure Cosmos DB e valores-chave primários para a sua conta Azure Cosmos DB.

1. No ficheiro **ambiente.js,** altere `port` o valor para 10255. 

    ```javascript
    const port = 10255;
    ```

1. No arquivo **ambiente.js,** altere `accountName` o valor do nome da conta Azure Cosmos DB que criou na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial. 

1. Obtenha a chave primária da conta do Azure Cosmos DB ao introduzir o comando da CLI seguinte na janela do terminal: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<nome cosmosdb> é o nome da conta Azure Cosmos DB que criou na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

1. Copie a chave primária para o arquivo `key` **ambiente.js** como o valor.

Agora a sua aplicação tem todas as informações necessárias para ligar ao Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Criar um modelo de hero

Em seguida, você precisa definir o esquema dos dados para armazenar em Azure Cosmos DB definindo um ficheiro modelo. Utilize os seguintes passos para criar um _modelo Hero_ que defina o esquema dos dados:

1. No painel explorer, sob a pasta do **servidor,** crie um ficheiro chamado **hero.model.js**.

1. Copie o seguinte código no ficheiro **hero.model.js.** O código fornece a seguinte funcionalidade:

   * Requer o Mongoose.
   * Cria um esquema novo com ID, nome e mensagem.
   * Cria um modelo usando o esquema.
   * Exporta o modelo. 
   * Nomeia a coleção **Heroes** (em vez de **Heros**, que é o nome padrão da coleção baseada em regras de nomeação plural Mongoose).

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

Depois de criar o modelo de herói, precisa de definir um serviço para ler os dados e executar a lista, criar, eliminar e atualizar as operações. Utilize os seguintes passos para criar um _serviço Hero_ que questiona os dados do Azure Cosmos DB:

1. No painel explorer, sob a pasta do **servidor,** crie um ficheiro chamado **hero.service.js**.

1. Copie o seguinte código no ficheiro **hero.service.js.** O código fornece a seguinte funcionalidade:

   * Obtém o modelo que criou.
   * Liga-se à base de dados.
   * Cria `docquery` uma variável `hero.find` que usa o método para definir uma consulta que devolve todos os heróis.
   * Faz uma consulta `docquery.exec` com a função com a promessa de obter uma lista de todos os heróis, onde o estado de resposta é 200. 
   * Envia a mensagem de erro se o estado for de 500.
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

## <a name="configure-routes"></a>Configure rotas

Em seguida, é necessário configurar rotas para lidar com os URLs para obter, criar, ler e eliminar pedidos. Os métodos de encaminhamento especificam funções de chamada (também chamadas _funções de manipulador)._ Estas funções são chamadas quando a aplicação recebe um pedido para o ponto final especificado e método HTTP. Utilize os seguintes passos para adicionar o serviço Hero e para definir as suas rotas:

1. No Visual Studio Code, no ficheiro **routes.js,** comentar a `res.send` função que envia os dados de herói da amostra. Adicione uma linha `heroService.getHeroes` para chamar a função em vez disso.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. No ficheiro **routes.js,** `require` o serviço de herói:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. No ficheiro **hero.service.js,** `getHeroes` atualize a `req` `res` função para tomar os e parâmetros seguintes:

    ```javascript
    function getHeroes(req, res) {
    ```

Vamos rever e ver o código anterior. Primeiro, entramos no ficheiro index.js, que configura o servidor do nó. Note que configura e define as suas rotas. Em seguida, o seu ficheiro routes.js fala com o serviço de heróis e diz-lhe para obter as suas funções, como **getHeroes**, e passar o pedido e resposta. O ficheiro hero.service.js recebe o modelo e liga-se a Mongo. Depois executa **os GetHeroes** quando lhe chamamos, e devolve uma resposta de 200. 

## <a name="run-the-app"></a>Executar a aplicação

Em seguida, executar a aplicação utilizando os seguintes passos:

1. No Código do Estúdio Visual, guarde todas as suas alterações. À esquerda, selecione ![o ícone](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) **Debug Debug** no Código do Estúdio ![Visual e, em](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png)seguida, selecione o botão **Iniciar depuração** Dobug ícone em Código de Estúdio Visual .

1. Agora mude para o navegador. Abra as **ferramentas Developer** e o **separador Rede**. Vá `http://localhost:3000`para, e aí vê a nossa candidatura.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Ainda não há heróis armazenados na aplicação. Na próxima parte deste tutorial, vamos adicionar colocar, empurrar e eliminar a funcionalidade. Depois podemos adicionar, atualizar e eliminar heróis da UI usando ligações da Mongoose à nossa base de dados Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos, pode eliminar o grupo de recursos, a conta Azure Cosmos DB e todos os recursos relacionados. Utilize os seguintes passos para eliminar o grupo de recursos:

 1. Vá ao grupo de recursos onde criou a conta Azure Cosmos DB.
 1. Selecione **Eliminar grupo de recursos**.
 1. Confirme o nome do grupo de recursos para apagar e **selecione Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Continue a Parte 6 do tutorial para adicionar funções post, put e Delete à aplicação:

> [!div class="nextstepaction"]
> [Parte 6: Adicionar post, colocar e excluir funções na aplicação](tutorial-develop-mongodb-nodejs-part6.md)
