---
title: Tutorial de desenvolvimento de aplicações de Java utilizando o Azure Cosmos DB
description: Este tutorial de aplicação Web do Java mostra-lhe como utilizar o Azure Cosmos DB e a API de SQL para armazenar e aceder a dados a partir de uma aplicação Java alojada em sites do Azure.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 08/22/2017
ms.author: ramkris
ms.openlocfilehash: 4d6d26bd142a62d57b55caa290a2a94a3988737d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008703"
---
# <a name="build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Criar uma aplicação Web Java com o Azure Cosmos DB e a API de SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Este tutorial de aplicação Web do Java mostra-lhe como utilizar o serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) para armazenar e aceder a dados a partir de uma aplicação Java alojada em Aplicações Web do Serviço de Aplicações do Azure. Neste artigo, vai aprender:

* Como criar uma aplicação JSP (JavaServer Pages) básica no Eclipse.
* Como trabalhar com o serviço do Azure Cosmos DB com o [SDK Java do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

Este tutorial de aplicação Java mostra-lhe como criar uma aplicação de gestão de tarefas baseada na Web que lhe permite criar, obter e marcar tarefas como concluídas, conforme mostrado na imagem que se segue. Cada uma das tarefas da lista ToDo é armazenada como documentos JSON no Azure Cosmos DB.

![A minha aplicação Java da ToDo List](./media/sql-api-java-application/image1.png)

> [!TIP]
> Este tutorial do desenvolvimento de aplicações parte do princípio que tem experiência na utilização do Java. Se estiver familiarizado com o Java ou com as [ferramentas dos pré-requisitos](#Prerequisites), recomendamos-lhe transferir todo o projeto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) a partir do GitHub e criá-lo utilizando [as instruções no final deste artigo](#GetProject). Assim que o tiver criado, pode rever o artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.  
> 
> 

## <a id="Prerequisites"></a>Pré-requisitos para este tutorial de aplicação Web de Java
Antes de começar este tutorial de desenvolvimento de aplicação, tem de ter o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Kit de desenvolvimento do Java (JDK) 7 +](https://aka.ms/azure-jdks).
* [IDE Eclipse para Programadores de Java EE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Um site do Azure com um tempo de execução em ambiente Java (por exemplo, Tomcat ou Jetty) ativado.](../app-service/app-service-web-get-started-java.md)

Se estiver a instalar estas ferramentas pela primeira vez, coreservlets.com fornece instruções do processo de instalação na seção de início rápido do respetivo [Tutorial: Instalar TomCat7 e utilizá-lo com o Eclipse](https://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) artigo.

## <a id="CreateDB"></a>Passo 1: Criar uma conta do Azure Cosmos DB
Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o emulador do Azure Cosmos DB para este tutorial, pode avançar para [passo 2: Criar a aplicação Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>Passo 2: Criar a aplicação Java JSP
Para criar a aplicação JSP:

1. Em primeiro lugar, iremos começar por criar um projeto de Java. Abra o Eclipse e clique em **Ficheiro**, **Novo**, e, em seguida, clique em **Dynamic Web Project**. Se não vir **Dynamic Web Project** listado como um projeto disponível, faça o seguinte: clique em **Ficheiro**, **Novo**, clique em **Projeto**..., expanda **Web**, clique em **Dynamic Web Project**, e clique em **Seguinte**.
   
    ![Desenvolvimento de aplicações de Java JSP](./media/sql-api-java-application/image10.png)
2. Introduza um nome de projeto na caixa **Nome do projeto** e no menu pendente **Tempo de execução de destino**, em opção, selecione um valor (por ex. Apache Tomcat v7.0) e, em seguida, clique em **Concluir**. Selecionar um tempo de execução de destino permite-lhe executar o seu projeto localmente através do Eclipse.
3. No Eclipse, na vista do Explorador de Projeto, expanda o seu projeto. Com o botão direito, faça um clique em **WebContent**, clique em **Novo**, e, em seguida, clique em **Ficheiro JSP**.
4. Na caixa de diálogo **Novo ficheiro JSP**, atribua o nome **index.jsp** ao ficheiro. Mantenha a pasta principal **WebContent**, como apresentado na seguinte ilustração e, em seguida, clique em **Seguinte**.
   
    ![Criar um novo ficheiro JSP - Tutorial de aplicações Web de Java](./media/sql-api-java-application/image11.png)
5. Na caixa de diálogo **Selecionar modelo de JSP**, no âmbito deste tutorial, selecione **Novo ficheiro JSP (html)**, e, em seguida, clique em **Concluir**.
6. Quando abrir o ficheiro index.jsp no Eclipse, adicione o texto a apresentar **Olá, Mundo!** dentro do elemento <body> existente. O conteúdo <body> atualizado deverá ser semelhante ao seguinte código:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Guarde o ficheiro index.jsp.
8. Se definir um tempo de execução de destino no passo 2, pode clicar em **Projeto** e, em seguida, em **Executar** para executar localmente a sua aplicação de JSP:
   
    ![Olá, Mundo – Tutorial de Aplicações Java](./media/sql-api-java-application/image12.png)

## <a id="InstallSDK"></a>Passo 3: Instalar o SQL de Java SDK
A forma mais fácil para obter o SDK do SQL Java e as respetivas dependências é através do [Apache Maven](https://maven.apache.org/).

Para tal, terá de converter o seu projeto em projeto maven, realizando os seguintes passos:

1. Faça um clique com o botão direito do rato no Explorador de Projeto, clique em **Configurar**, clique em **Converter em Projeto Maven**.
2. Na janela **Criar nova POM**, aceite as predefinições e clique em **Concluir**.
3. No **Explorador de Projeto**, abra o ficheiro pom.xml.
4. No separador **Dependências**, no painel **Dependências**, clique em **Adicionar**.
5. Na janela **Selecionar Dependência**, faça o seguinte:
   
   * Na caixa **Id do Grupo**, introduza com.microsoft.azure.
   * Na caixa **Id do Artefacto**, Introduza azure-documentdb.
   * Na caixa **Versão**, introduza 1.5.1.
     
   ![Instalar o SDK da Aplicação de SQL Java](./media/sql-api-java-application/image13.png)
     
   * Ou adicione a dependência XML para Id do Grupo e Id do Artefacto diretamente no pom.xml através de um editor de texto:
     
        <dependency> <groupId>com.microsoft.azure</groupId> <artifactId>azure-documentdb</artifactId> <version>1.9.1</version> </dependency>
6. Clique em **OK** e o Maven irá instalar o SDK de SQL Java.
7. Guarde o ficheiro pom.xml.

## <a id="UseService"></a>Passo 4: Utilizar o serviço do Azure Cosmos DB numa aplicação de Java
1. Em primeiro lugar, definamos o objeto TodoItem em TodoItem.java:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    Neste projeto, está a utilizar o [Project Lombok](https://projectlombok.org/) para gerar o construtor, os getters, os setters e um construtor. Em alternativa, pode escrever manualmente este código ou pedir a sua geração ao IDE.
2. Para invocar o serviço do Azure Cosmos DB, tem de instanciar um novo **DocumentClient**. Em geral, é melhor reutilizar o **DocumentClient** - em vez de construir um novo cliente para cada pedido subsequente. Podemos reutilizar o cliente envolvendo-o num **DocumentClientFactory**. Em DocumentClientFactory.java, também terá de colar o valor do URI e a CHAVE PRIMÁRIA que guardou na sua área de transferência no [passo 1](#CreateDB). Substitua o [SEU\_PONTO FINAL\_AQUI] pelo seu URI e substitua a [SUA\_CHAVE\_AQUI] pela sua CHAVE PRIMÁRIA.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Agora, criemos um Objeto de Acesso a Dados (DAO) para abstrair os nossos itens ToDo no Azure Cosmos DB.
   
    Para guardar os itens ToDo numa coleção, o cliente tem de saber qual a base de dados e a coleção a manter (conforme referenciado em auto-ligações). Em geral, é melhor colocar em cache a base de dados e a coleção sempre que possível, para evitar tempos de retorno adicionais para a base de dados.
   
    O seguinte código ilustra como obter a nossa base de dados e coleção, se existir, ou como criar uma nova se não existir:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. O passo seguinte consiste em escrever alguns códigos para manter o TodoItems na coleção. Neste exemplo, utilizaremos o [Gson](https://code.google.com/p/google-gson/) para serializar e anular a serialização de antigos objetos simples TodoItem de Java (POJOs) para documentos JSON.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Tal como as bases de dados do Azure Cosmos DB e coleções, os documentos também são referenciados por auto-ligações. A seguinte função do programa auxiliar permite-nos obter documentos por outro atributo (por exemplo, "id") em vez de auto-ligação:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Podemos utilizar o método de programa auxiliar no passo 5 para obter um documento TodoItem JSON por id e, em seguida, anular a serialização para um POJO:
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. Também podemos utilizar o DocumentClient para obter uma coleção ou uma lista de TodoItems com o SQL:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. Existem várias formas de atualizar um documento com o DocumentClient. Na nossa aplicação Todo List, queremos conseguir alternar se um TodoItem está concluído. Esta ação pode ser realizada atualizando o atributo "completo" no documento:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Por fim, queremos poder eliminar um TodoItem da nossa lista. Para tal, podemos utilizar o método de programa auxiliar que escrevemos anteriormente para obter a ligação personalizada e, em seguida, pedir ao cliente para a eliminar:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>Passo 5: Fazer a ligação do restante do de projeto de desenvolvimento de aplicações Java em conjunto
Agora que concluímos a parte divertida - apenas falta criar uma interface de utilizador rápida e associá-la ao nosso DAO.

1. Em primeiro lugar, comecemos por criar um controlador para chamar o nosso DAO:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    Numa aplicação mais complexa, o controlador poderá hospedar a lógica de negócio mais complicada na parte superior do DAO.
2. De seguida, iremos criar uma servlet para encaminhar pedidos de HTTP ao controlador:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Precisamos de uma Interface de utilizador Web a apresentar ao utilizador. Vamos escrever novamente o index.jsp que criámos anteriormente:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. E por fim, escreva algum JavaScript do lado do cliente para ligar a interface de utilizador Web e a servlet:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Fantástico! Agora, só falta testar a aplicação. Execute localmente a aplicação e adicione alguns itens Todo ao indicar o nome e a categoria do item. De seguida, clique em **Adicionar Tarefa**.
6. Assim que o item for apresentado, pode atualizar se estiver concluído desativar a caixa de verificação e ao clicar em **Atualizar Tarefas**.

## <a id="Deploy"></a>Passo 6: Implementar a sua aplicação de Java para Web Sites do Azure
Os Sites do Azure tornam a implementação de aplicações Java tão simples como a exportação da sua aplicação com um ficheiro WAR e ao carregá-lo através do controlo de código fonte (por exemplo, Git) ou FTP.

1. Para exportar a sua aplicação como um ficheiro WAR, clique com o botão direito do rato no seu projeto em **Explorador de Projeto**, clique em **Exportar**, e, em seguida, clique em **Ficheiro WAR**.
2. Na janela **Exportar WAR**, efetue o seguinte procedimento:
   
   * Na caixa de projeto Web, introduza azure documentdb-java-sample.
   * Na caixa Destino, selecione um destino para guardar o ficheiro WAR.
   * Clique em **Concluir**.
3. Agora que tem um ficheiro WAR, pode simplesmente carregá-lo no diretório **webapps** do seu Site do Azure. Para obter instruções sobre o carregamento do ficheiro, veja [Adicionar uma aplicação Java às Aplicações Web do Serviço de Aplicações do Azure](../app-service/web-sites-java-add-app.md).
   
    Depois de o ficheiro WAR ter sido carregado no diretório de webapps, o ambiente de tempo de execução irá detetar que a adição e irá carregá-lo automaticamente.
4. Para ver o seu produto concluído, navegue para `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` e comece a adicionar as suas tarefas!

## <a id="GetProject"></a>Obter o projeto a partir do GitHub
Todos os exemplos deste tutorial estão incluídos no projeto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) no GitHub. Para importar o projeto todo para o Eclipse, certifique-se de que o software e os recursos estão listados na secção [Pré-requisitos](#Prerequisites) e, de seguida, efetue o seguinte procedimento:

1. Instalar [Project Lombok](https://projectlombok.org/). Lombok é utilizado para gerar construtores, getters e setters no projeto. Depois de ter transferido o ficheiro lombok.jar, faça duplo clique para instalá-lo ou instale-o a partir da linha de comandos.
2. Se o Eclipse estiver aberto, feche-o e reinicie-o para carregar o Lombok.
3. No Eclipse, no menu **Ficheiro**, clique em **Importar**.
4. Na janela **Importar**, clique em **Git**, clique em **Projetos a partir do Git**, e, em seguida, clique em **Seguinte**.
5. No ecrã **Selecionar Origem do Repositório**, clique em **Clonar URI**.
6. No ecrã **Repositório de Git de Origem**, na caixa **URI**, introduza https://github.com/Azure-Samples/documentdb-java-todo-app.git e, em seguida, clique em **Seguinte**.
7. No ecrã **Seleção do Ramo**, certifique-se de ter selecionado **principal** e, em seguida, clique em **Seguinte**.
8. No ecrã **Destino Local**, clique em **Procurar** para selecionar uma pasta em que o repositório pode ser copiado e, em seguida, clique em **Seguinte**.
9. No ecrã **Selecionar um assistente a utilizar para importar projetos**, certifique-se de ter selecionado **Importar projetos existentes** e, em seguida, clique em **Seguinte**.
10. No ecrã **Importar Projetos**, anule a seleção do projeto **DocumentDB** e, em seguida, clique em **Concluir**. O projeto do DocumentDB contém o SDK Java do Azure Cosmos DB, que iremos adicionar como uma dependência.
11. Em **Explorador de Projeto**, navegue até azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java e substitua os valores ANFITRIÃO e MASTER_KEY pelo URI e a CHAVE PRIMÁRIA da sua conta do Azure Cosmos DB e, em seguida, guarde o ficheiro. Para obter mais informações, veja o [Passo 1. Crie uma conta de base de dados do Azure Cosmos DB](#CreateDB).
12. Em **Explorador de Projeto**, clique com o botão direito do rato em **azure-documentdb-java-sample**, clique em **Criar Caminho**, e, em seguida, clique em **Configurar Criar Caminho**.
13. No ecrã **Criar Caminho Java**, no painel da direita, selecione o separador **Bibliotecas** e, em seguida, clique em **Adicionar JARs Externos**. Navegue para a localização do ficheiro lombok.jar e clique em **Abrir**, e, em seguida, clique em **OK**.
14. Aplique o passo 12 para abrir novamente a janela **Propriedades** e, em seguida, no painel da esquerda, clique em **Tempos de execução Visados**.
15. No ecrã **Tempos de execução Visados**, clique em **Novo**, selecione **Apache Tomcat v7.0** e, em seguida, clique em **OK**.
16. Aplique o passo 12 para abrir novamente a janela **Propriedades** e, em seguida, no painel da esquerda, clique em **Facetas do Projeto**.
17. No ecrã **Facetas do Projeto**, selecione **Módulo Web Dinâmico** e **Java** e, em seguida, clique em **OK**.
18. No separador **Servidores**, na parte inferior do ecrã, clique com o botão direito do rato em **Servidor Tomcat v7.0 no localhost** e, em seguida, clique em **Adicionar e Remover**.
19. Na janela **Adicionar e Remover**, mover **azure documentdb-java-samples** para a caixa **Configurado** e, em seguida, clique em **Concluir**.
20. No separador **Servidores**, clique com o botão direito do rato em **Tomcat v7.0 Server no localhost** e, em seguida, clique em **Reiniciar**.
21. Num browser, navegue para `http://localhost:8080/azure-documentdb-java-sample/` e comece a adicionar à sua lista de tarefas. Tenha em atenção que se tiver alterado os valores de porta predefinidos, altere 8080 para o valor que selecionou.
22. Para implementar o seu num site Web do Azure, consulte o artigo [Passo 6. Implemente a sua aplicação existente nos Sites do Azure](#Deploy).

