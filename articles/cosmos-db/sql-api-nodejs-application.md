---
title: 'Tutorial: criar um aplicativo Web node. js com Azure Cosmos DB SDK do JavaScript para gerenciar dados da API do SQL'
description: Este tutorial do node. js explora como usar Microsoft Azure Cosmos DB para armazenar e acessar dados de um aplicativo Web node. js Express hospedado no recurso aplicativos Web do serviço de aplicativo do Microsoft Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Node.js web application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 7a01c436f31a8ce5b8071db3eda4cf5562c421c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441264"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Tutorial: criar um aplicativo Web node. js usando o SDK do JavaScript para gerenciar uma conta da API do SQL no Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Como desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar uma conta da API do SQL no Azure Cosmos DB para armazenar e acessar esses dados do documento. Este tutorial do node. js mostra como armazenar e acessar dados de uma conta da API do SQL no Azure Cosmos DB usando um aplicativo do node. js Express hospedado no recurso aplicativos Web do serviço de aplicativo do Microsoft Azure. Neste tutorial, você criará um aplicativo baseado na Web (aplicativo de todo) que permite criar, recuperar e concluir tarefas. As tarefas são armazenadas como documentos JSON no Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta da API do SQL no Azure Cosmos DB usando o portal do Azure. Em seguida, você cria e executa um aplicativo Web criado no SDK do node. js para criar um banco de dados e um contêiner e adicionar itens ao contêiner. Este tutorial usa o SDK do JavaScript versão 3,0.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Criar uma nova aplicação Node.js
> * Ligar a aplicação ao Azure Cosmos DB
> * Executar e implementar a aplicação no Azure

## <a name="_Toc395783176"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node. js][Node.js] versão 6,10 ou superior.
* [Express generator](https://www.expressjs.com/starter/generator.html) (pode instalar o Express através de `npm install express-generator -g`)
* Instale o [git][Git] em sua estação de trabalho local.

## <a name="_Toc395637761"></a>Criar uma conta de Azure Cosmos DB
Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o Emulador do Azure Cosmos DB para este tutorial, pode avançar para o [Passo 2: Criar uma aplicação Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Criar um novo aplicativo node. js
Agora, vamos aprender a criar um projeto de Olá, Mundo do node. js básico usando a estrutura Express.

1. Abra o seu terminal favorito, como a linha de comandos Node.js.

1. Navegue para o diretório no qual pretende armazenar a nova aplicação.

1. Utilizar o Express generator para gerar uma nova aplicação designada **todo**.

   ```bash
   express todo
   ```

1. Abra o diretório **todo** e instale as dependências.

   ```bash
   cd todo
   npm install
   ```

1. Execute a nova aplicação.

   ```bash
   npm start
   ```

1. Pode ver a sua aplicação nova ao navegar para [http://localhost:3000](http://localhost:3000).
   
   ![Saiba Node.js - Captura de ecrã da aplicação Olá, Mundo numa janela do browser](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

   Pare o aplicativo usando CTRL + C na janela do terminal e selecione **y** para encerrar o trabalho em lotes.

## <a name="_Toc395783179"></a>Instalar os módulos necessários

O ficheiro **package.json** é um dos ficheiros criados na raiz do projeto. Este ficheiro contém uma lista de módulos adicionais que são necessários para a sua aplicação Node.js. Quando implementar esta aplicação no Azure, este ficheiro servirá para determinar quais os módulos que devem estar instalados no Azure para suportar a sua aplicação. Instale mais dois pacotes para este tutorial.

1. Instale o módulo **\@Azure/Cosmos** via NPM. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Conectar o aplicativo node. js ao Azure Cosmos DB
Agora que já concluiu a configuração inicial, vai escrever o código de que a aplicação Todo precisa para comunicar com o Azure Cosmos DB.

### <a name="create-the-model"></a>Criar o modelo
1. Na raiz do diretório do projeto, crie um novo diretório chamado **modelos**.  

2. No diretório **modelos**, crie um novo ficheiro designado **taskDao.js**. Esse arquivo contém o código necessário para criar o banco de dados e o contêiner. Ele também define métodos para ler, atualizar, criar e localizar tarefas no Azure Cosmos DB. 

3. Copie o código a seguir no arquivo **taskDao. js** :

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. Guarde e feche o ficheiro **taskDao.js**.  

### <a name="create-the-controller"></a>Criar o controlador

1. No diretório **rotas** do seu projeto, crie um novo ficheiro designado **tasklist.js**.  

2. Adicione o seguinte código ao **tasklist.js**. Este código carrega os módulos CosmosClient e async, que são utilizados por **tasklist.js**. Este código também define a classe **TaskList**, que é transmitida como uma instância do objeto **TaskDao** que definimos anteriormente:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. Guarde e feche o ficheiro **tasklist.js**.

### <a name="add-configjs"></a>Adicionar config.js

1. Na raiz do diretório do projeto, crie um ficheiro novo designado **config.js**. 

2. Adicione o seguinte código ao ficheiro **config.js**. Este código define os parâmetros e os valores da configuração necessários para a nossa aplicação.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. No arquivo **config. js** , atualize os valores de HOST e AUTH_KEY usando os valores encontrados na página chaves da sua conta de Azure Cosmos DB no [portal do Azure](https://portal.azure.com). 

4. Guarde e feche o ficheiro **config.js**.

### <a name="modify-appjs"></a>Modificar app.js

1. No diretório do projeto, abra o ficheiro **app.js**. Este ficheiro foi criado anteriormente, aquando da criação da aplicação Web Express.  

2. Adicione o seguinte código ao ficheiro **app.js**. Este código define o ficheiro de configuração a utilizar e carrega os valores para algumas variáveis que utilizará nas próximas secções. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Por fim, guarde e feche o ficheiro **app.js**.

## <a name="_Toc395783181"></a>Criar uma interface do usuário

Agora, vamos criar a interface de utilizador para que um utilizador possa interagir com a aplicação. A aplicação Express que criámos nas secções anteriores utiliza o **Jade** como motor de vista.

1. O ficheiro **layout.jade** no diretório **vistas** é utilizado como um modelo global para outros ficheiros **.jade**. Nesta etapa, você irá modificá-lo para usar a inicialização do Twitter, que é um kit de ferramentas usado para criar um site.  

2. Abra o ficheiro **layout.jade** encontrado na pasta **views** e substitua o conteúdo pelo seguinte código:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Esse código diz ao mecanismo **Jade** para renderizar algum HTML para nosso aplicativo e cria um **bloco** chamado **Content** , no qual podemos fornecer o layout para nossas páginas de conteúdo. Guarde e feche o ficheiro **layout.jade**.

3. Agora, abra o ficheiro **index.jade**, a vista que será utilizada pela nossa aplicação, e substitua o conteúdo do ficheiro pelo seguinte código:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Este código expande o modelo e fornece o conteúdo para o marcador de posição **conteúdo** que vimos anteriormente no ficheiro **layout.jade**. Neste esquema, criámos dois formulários HTML.

O primeiro formulário contém uma tabela para os dados e um botão que nos permite atualizar os itens mediante a publicação no método **/completeTask** do controlador.
    
O segundo formulário contém dois campos de entrada e um botão que permite criar um novo item ao publicar no método **/addtask** do controlador. É tudo o que precisamos para que a aplicação funcione.

## <a name="_Toc395783181"></a>Executar seu aplicativo localmente

Agora que você criou o aplicativo, pode executá-lo localmente usando as seguintes etapas:  

1. Para testar o aplicativo em seu computador local, execute `npm start` no terminal para iniciar o aplicativo e, em seguida, atualize a página do navegador [http://localhost:3000](http://localhost:3000) . A página deve ser agora apresentada conforme mostrado na captura de ecrã seguinte:
   
    ![Captura de ecrã da aplicação MyTodo List numa janela do browser](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Se você receber um erro sobre o recuo no arquivo layout. Jade ou no arquivo index. Jade, certifique-se de que as duas primeiras linhas em ambos os arquivos sejam justificadas à esquerda, sem espaços. Se houver espaços antes das duas primeiras linhas, remova-as, salve os dois arquivos e, em seguida, atualize a janela do navegador. 

2. Use os campos item, nome do item e categoria para inserir uma nova tarefa e, em seguida, selecione **Adicionar item**. Cria um documento no Azure Cosmos DB com essas propriedades. 

3. A página deverá ser atualizada para mostrar o item criado recentemente na ToDo List.
   
    ![Captura de ecrã da aplicação com um novo item na ToDo List](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. Para concluir uma tarefa, marque a caixa de seleção na coluna concluir e, em seguida, selecione **Atualizar tarefas**. Este procedimento atualiza o documento já criado e retira-o da vista.

5. Para parar a aplicação, prima CTRL+C na janela do terminal e, em seguida, selecione **Y** para terminar a tarefa de lote.

## <a name="_Toc395783182"></a>Implantar seu aplicativo em aplicativos Web

Depois que o aplicativo for executado localmente, você poderá implantá-lo no Azure usando as seguintes etapas:

1. Se você ainda não tiver feito isso, habilite um repositório Git para seu aplicativo de aplicativos Web.

2. Adicione seu aplicativo de aplicativos Web como um git remoto.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Implemente a aplicação ao enviá-la para a ligação remota.
   
   ```bash
   git push azure master
   ```

4. Em alguns segundos, a aplicação Web é publicada e iniciada num browser.

## <a name="clean-up-resources"></a>Limpar recursos

Quando esses recursos não forem mais necessários, você poderá excluir o grupo de recursos, Azure Cosmos DB conta e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos que você usou para a conta de Azure Cosmos DB, selecione **excluir**e confirme o nome do grupo de recursos a ser excluído.

## <a name="_Toc395637775"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar aplicações móveis com o Xamarin e o Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

