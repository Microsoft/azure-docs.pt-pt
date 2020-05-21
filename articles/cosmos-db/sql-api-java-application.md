---
title: 'Tutorial: Construa uma aplicação web Java utilizando o Azure Cosmos DB e a SQL API'
description: 'Tutorial: Este tutorial de aplicação web Java mostra-lhe como usar o Azure Cosmos DB e a API SQL para armazenar e aceder a dados de uma aplicação Java hospedada nos Websites Azure.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: anfeldma
ms.openlocfilehash: 6f8431bfd3be75651f3a08fe9b07fc3902436331
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657292"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Tutorial: Construa uma aplicação web Java utilizando o Azure Cosmos DB e a SQL API

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

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Pré-requisitos para este tutorial de aplicação Web de Java

Antes de começar este tutorial de desenvolvimento de aplicação, tem de ter o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Kit de desenvolvimento do Java (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable).
* [IDE Eclipse para Programadores de Java EE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Um site do Azure com um tempo de execução em ambiente Java (por exemplo, Tomcat ou Jetty) ativado.](../app-service/app-service-web-get-started-java.md)

Se estiver a instalar estas ferramentas pela primeira vez, coreservlets.com fornece um walk-through do processo de instalação na secção de arranque rápido do seu [Tutorial: Instalar o TomCat7 e usá-lo com](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) artigo eclipse.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Criar uma conta Azure Cosmos DB

Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o Emulador do Azure Cosmos DB para este tutorial, pode avançar para o [Passo 2: Criar a aplicação Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Criar a aplicação Java JSP

Para criar a aplicação JSP:

1. Primeiro, vamos começar por criar um projeto Java. Abra o Eclipse e clique em **Ficheiro**, **Novo**, e, em seguida, clique em **Dynamic Web Project**. Se não vir o **Dynamic Web Project** listado como um projeto disponível, faça o seguinte: clique em **File,** clique em **Novo,** clique em **Projeto**Web..., clique no **Web,** clique no **Dynamic Web Project**e clique em **Next**.
   
    ![Desenvolvimento de aplicações de Java JSP](./media/sql-api-java-application/image10.png)

1. Introduza um nome de projeto na caixa **Nome do projeto** e no menu pendente **Tempo de execução de destino**, em opção, selecione um valor (por ex. Apache Tomcat v7.0) e, em seguida, clique em **Concluir**. Selecionar um tempo de execução de destino permite-lhe executar o seu projeto localmente através do Eclipse.

1. No Eclipse, na vista do Explorador de Projeto, expanda o seu projeto. Com o botão direito, faça um clique em **WebContent**, clique em **Novo**, e, em seguida, clique em **Ficheiro JSP**.

1. Na caixa de diálogo **Novo ficheiro JSP**, atribua o nome **index.jsp** ao ficheiro. Mantenha a pasta principal **WebContent**, como apresentado na seguinte ilustração e, em seguida, clique em **Seguinte**.
   
    ![Criar um novo ficheiro JSP - Tutorial de aplicações Web de Java](./media/sql-api-java-application/image11.png)

1. Na caixa de diálogo **Selecionar modelo de JSP**, no âmbito deste tutorial, selecione **Novo ficheiro JSP (html)**, e, em seguida, clique em **Concluir**.

1. Quando o ficheiro *index.jsp* abrir no Eclipse, adicione texto para exibir **Hello World!** dentro do elemento `<body>` existente. O conteúdo `<body>` atualizado deverá ser semelhante ao seguinte código:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Guarde o ficheiro *index.jsp.*

1. Se definir um tempo de execução de destino no passo 2, pode clicar em **Projeto** e, em seguida, em **Executar** para executar localmente a sua aplicação de JSP:

  ![Olá, Mundo – Tutorial de Aplicações Java](./media/sql-api-java-application/image12.png)

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Instale o SQL Java SDK

A forma mais fácil para obter o SDK do SQL Java e as respetivas dependências é através do [Apache Maven](https://maven.apache.org/). Para isso, é necessário converter o seu projeto num projeto DaMaven utilizando os seguintes passos:

1. Faça um clique com o botão direito do rato no Explorador de Projeto, clique em **Configurar**, clique em **Converter em Projeto Maven**.

1. Na nova janela **Pom Criar,** aceite as predefinições e clique em **Terminar**.

1. No **Explorador de Projeto**, abra o ficheiro pom.xml.

1. No separador **Dependências**, no painel **Dependências**, clique em **Adicionar**.

1. Na janela **Selecionar Dependência**, faça o seguinte:
   
   * Na caixa **id do grupo,** introduza `com.azure` .
   * Na caixa **de identificação** de artefactos, introduza `azure-cosmos` .
   * Na caixa **Versão,** introduza `4.0.1-beta.1` .
  
   Ou, pode adicionar a dependência XML para id de grupo e identificação de artefacto diretamente ao ficheiro *pom.xml:*

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
   </dependency>
   ```

1. Clique em **OK** e Maven instalará o SQL Java SDK ou salvará o ficheiro pom.xml.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Utilize o serviço Azure Cosmos DB na sua aplicação Java

Agora vamos adicionar os modelos, as vistas e os controladores à sua aplicação web.

### <a name="add-a-model"></a>Adicione um modelo

Primeiro, vamos definir um modelo dentro de um novo ficheiro *TodoItem.java*. A `TodoItem` classe define o esquema de um item juntamente com os métodos getter e setter:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Adicione as classes de Objetos de Acesso a Dados (DAO)

Crie um Objeto de Acesso de Dados (DAO) ao abstrato persistindo os itens ToDo para Azure Cosmos DB. Para guardar os itens ToDo numa coleção, o cliente tem de saber qual a base de dados e a coleção a manter (conforme referenciado em auto-ligações). Em geral, é melhor colocar em cache a base de dados e a coleção sempre que possível, para evitar tempos de retorno adicionais para a base de dados.

1. Para invocar o serviço Azure Cosmos DB, você deve instantaneamente um novo `cosmosClient` objeto. Em geral, o melhor é reutilizar o `cosmosClient` objeto em vez de construir um novo cliente para cada pedido subsequente. Pode reutilizar o cliente definindo-o dentro da `cosmosClientFactory` classe. Atualize os valores HOST e MASTER_KEY que guardou no [passo 1](#CreateDB). Substitua a variável HOST com o seu URI e substitua o MASTER_KEY pela sua CHAVE PRIMÁRIA. Utilize o seguinte código para criar a `CosmosClientFactory` classe dentro do ficheiro *CosmosClientFactory.java:*

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/CosmosClientFactory.java":::

1. Crie um novo ficheiro *TodoDao.java* e adicione a `TodoDao` classe para criar, atualizar, ler e eliminar os itens todo- oficiais:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDao.java":::

1. Crie um novo ficheiro *MockDao.java* e adicione a `MockDao` classe, esta classe implementa a `TodoDao` classe para realizar operações crud nos itens:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/MockDao.java":::

1. Crie um novo ficheiro *DocDbDao.java* e adicione a `DocDbDao` classe. Esta classe define o código para persistir os TodoItems no recipiente, recupera a sua base de dados e a sua recolha, se ela existe, ou cria uma nova se não existir. Este exemplo usa [gson](https://code.google.com/p/google-gson/) para serializar e desserializar os Objetos Java Antigos (POJOs) de TodoItem Plain (POJOs) para documentos JSON. Para guardar os itens ToDo numa coleção, o cliente tem de saber qual a base de dados e a coleção a manter (conforme referenciado em auto-ligações). Esta classe também define a função do ajudante para recuperar os documentos por outro atributo (por exemplo, "ID") em vez de auto-ligação. Pode utilizar o método do ajudante para recuperar um documento TodoItem JSON por ID e, em seguida, desserializá-lo para um POJO.

   Também pode usar o objeto do `cosmosClient` cliente para obter uma coleção ou lista de TodoItems usando uma consulta SQL. Por fim, define o método de eliminação para eliminar um TodoItem da sua lista. O seguinte código mostra o conteúdo da `DocDbDao` classe:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/DocDbDao.java":::

1. Em seguida, crie um novo ficheiro *TodoDaoFactory.java* e adicione a `TodoDaoFactory` classe que cria um novo objeto DocDbDao:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Adicionar um controlador

Adicione o controlador *TodoItemController* à sua aplicação. Neste projeto, está a utilizar o [Project Lombok](https://projectlombok.org/) para gerar o construtor, os getters, os setters e um construtor. Em alternativa, pode escrever este código manualmente ou fazer com que o IDE o gere.:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Criar um servlet

Em seguida, crie um servlet para encaminhar pedidos HTTP para o controlador. Crie o ficheiro *ApiServlet.java* e defina o seguinte código por baixo do mesmo:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Ligue o resto da app java juntos

Agora que terminamos as partes divertidas, tudo o que resta é construir uma interface rápida de utilizador e ligá-la ao seu DAO.

1. Precisa de uma interface de utilizador web para exibir ao utilizador. Vamos reescrever o *index.jsp* que criamos anteriormente com o seguinte código:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Por fim, escreva um JavaScript do lado do cliente para ligar a interface do utilizador web e o servlet em conjunto:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Agora, só falta testar a aplicação. Execute localmente a aplicação e adicione alguns itens Todo ao indicar o nome e a categoria do item. De seguida, clique em **Adicionar Tarefa**. Depois de o item aparecer, pode atualizar se está completo toggling the checkbox and **clicking Update Tasks**.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Implemente a sua aplicação Java para Web Sites Azure

Os Sites do Azure tornam a implementação de aplicações Java tão simples como a exportação da sua aplicação com um ficheiro WAR e ao carregá-lo através do controlo de código fonte (por exemplo, Git) ou FTP.

1. Para exportar a sua aplicação como um ficheiro WAR, clique com o botão direito do rato no seu projeto em **Explorador de Projeto**, clique em **Exportar**, e, em seguida, clique em **Ficheiro WAR**.

1. Na janela **Exportar WAR**, efetue o seguinte procedimento:
   
   * Na caixa de projeto Web, introduza azure documentdb-java-sample.
   * Na caixa Destino, selecione um destino para guardar o ficheiro WAR.
   * Clique em **Concluir**.

1. Agora que tem um ficheiro WAR, pode simplesmente carregá-lo no diretório **webapps** do seu Site do Azure. Para obter instruções sobre o carregamento do ficheiro, veja [Adicionar uma aplicação Java às Aplicações Web do Serviço de Aplicações do Azure](../app-service/web-sites-java-add-app.md). Depois de o ficheiro WAR ser enviado para o diretório de webapps, o ambiente de tempo de execução detetará que o adicionou e irá carregá-lo automaticamente.

1. Para ver o seu produto concluído, navegue para `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` e comece a adicionar as suas tarefas!

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Obter o projeto a partir do GitHub

Todos os exemplos deste tutorial estão incluídos no projeto [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) no GitHub. Para importar o projeto todo para o Eclipse, certifique-se de que o software e os recursos estão listados na secção [Pré-requisitos](#Prerequisites) e, de seguida, efetue o seguinte procedimento:

1. Instalar [Project Lombok](https://projectlombok.org/). Lombok é utilizado para gerar construtores, getters e setters no projeto. Depois de ter transferido o ficheiro lombok.jar, faça duplo clique para instalá-lo ou instale-o a partir da linha de comandos.

1. Se o Eclipse estiver aberto, feche-o e reinicie-o para carregar o Lombok.

1. No Eclipse, no menu **Ficheiro**, clique em **Importar**.

1. Na janela **Importar**, clique em **Git**, clique em **Projetos a partir do Git**, e, em seguida, clique em **Seguinte**.

1. No ecrã **Selecionar Origem do Repositório**, clique em **Clonar URI**.

1. No ecrã **Repositório de Git de Origem**, na caixa **URI**, introduza https://github.com/Azure-Samples/documentdb-java-todo-app.git e, em seguida, clique em **Seguinte**.

1. No ecrã **Seleção do Ramo**, certifique-se de ter selecionado **principal** e, em seguida, clique em **Seguinte**.

1. No ecrã **Destino Local**, clique em **Procurar** para selecionar uma pasta em que o repositório pode ser copiado e, em seguida, clique em **Seguinte**.

1. No ecrã **Selecionar um assistente a utilizar para importar projetos**, certifique-se de ter selecionado **Importar projetos existentes** e, em seguida, clique em **Seguinte**.

1. No ecrã **Importar Projetos**, anule a seleção do projeto **DocumentDB** e, em seguida, clique em **Concluir**. O projeto do DocumentDB contém o SDK Java do Azure Cosmos DB, que iremos adicionar como uma dependência.

1. Em **Explorador de Projeto**, navegue até azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java e substitua os valores ANFITRIÃO e MASTER_KEY pelo URI e a CHAVE PRIMÁRIA da sua conta do Azure Cosmos DB e, em seguida, guarde o ficheiro. Para mais informações, consulte o [Passo 1. Crie uma conta de base de dados Azure Cosmos.](#CreateDB)

1. Em **Explorador de Projeto**, clique com o botão direito do rato em **azure-documentdb-java-sample**, clique em **Criar Caminho**, e, em seguida, clique em **Configurar Criar Caminho**.

1. No ecrã **Criar Caminho Java**, no painel da direita, selecione o separador **Bibliotecas** e, em seguida, clique em **Adicionar JARs Externos**. Navegue para a localização do ficheiro lombok.jar e clique em **Abrir**, e, em seguida, clique em **OK**.

1. Aplique o passo 12 para abrir novamente a janela **Propriedades** e, em seguida, no painel da esquerda, clique em **Tempos de execução Visados**.

1. No ecrã **Tempos de execução Visados**, clique em **Novo**, selecione **Apache Tomcat v7.0** e, em seguida, clique em **OK**.

1. Aplique o passo 12 para abrir novamente a janela **Propriedades** e, em seguida, no painel da esquerda, clique em **Facetas do Projeto**.

1. No ecrã **Facetas do Projeto**, selecione **Módulo Web Dinâmico** e **Java** e, em seguida, clique em **OK**.

1. No separador **Servidores**, na parte inferior do ecrã, clique com o botão direito do rato em **Servidor Tomcat v7.0 no localhost** e, em seguida, clique em **Adicionar e Remover**.

1. Na janela **Adicionar e Remover**, mover **azure documentdb-java-samples** para a caixa **Configurado** e, em seguida, clique em **Concluir**.

1. No separador **Servidores**, clique com o botão direito do rato em **Tomcat v7.0 Server no localhost** e, em seguida, clique em **Reiniciar**.

1. Num browser, navegue para `http://localhost:8080/azure-documentdb-java-sample/` e comece a adicionar à sua lista de tarefas. Tenha em atenção que se tiver alterado os valores de porta predefinidos, altere 8080 para o valor que selecionou.

1. Para implementar o seu projeto num site azure, consulte o [Passo 6. Implemente a sua aplicação para Web Sites Do Azure](#Deploy).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Construa uma aplicação node.js com a Azure Cosmos DB](sql-api-nodejs-application.md)
