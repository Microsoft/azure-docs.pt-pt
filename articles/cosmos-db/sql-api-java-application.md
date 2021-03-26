---
title: 'Tutorial: Construa uma aplicação web Java usando Azure Cosmos DB e a API SQL'
description: 'Tutorial: Este tutorial de aplicação web java mostra-lhe como usar o Azure Cosmos DB e a API SQL para armazenar e aceder a dados de uma aplicação Java hospedada em Websites Azure.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 01c9a8b62190f3b545169bb5ba6732e6799ff9f2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559813"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Tutorial: Construa uma aplicação web Java usando Azure Cosmos DB e a API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Este tutorial de aplicação Web do Java mostra-lhe como utilizar o serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) para armazenar e aceder a dados a partir de uma aplicação Java alojada em Aplicações Web do Serviço de Aplicações do Azure. Neste artigo, vai aprender:

* Como criar uma aplicação JSP (JavaServer Pages) básica no Eclipse.
* Como trabalhar com o serviço do Azure Cosmos DB com o [SDK Java do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

Este tutorial de aplicação Java mostra-lhe como criar uma aplicação de gestão de tarefas baseada na Web que lhe permite criar, obter e marcar tarefas como concluídas, conforme mostrado na imagem que se segue. Cada uma das tarefas da lista ToDo é armazenada como documentos JSON no Azure Cosmos DB.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="A minha aplicação Java da ToDo List":::

> [!TIP]
> Este tutorial do desenvolvimento de aplicações parte do princípio que tem experiência na utilização do Java. Se você é novo em Java ou as [ferramentas pré-requisitos,](#Prerequisites)recomendamos baixar o projeto completo [todo] https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) do GitHub e construí-lo usando [as instruções no final deste artigo.](#GetProject) Assim que o tiver criado, pode rever o artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Pré-requisitos para este tutorial de aplicação Web de Java

Antes de começar este tutorial de desenvolvimento de aplicação, tem de ter o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Kit de desenvolvimento do Java (JDK) 7 +](/java/azure/jdk/).
* [IDE Eclipse para Programadores de Java EE.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Um site do Azure com um tempo de execução em ambiente Java (por exemplo, Tomcat ou Jetty) ativado.](../app-service/quickstart-java.md)

Se estiver a instalar estas ferramentas pela primeira vez, coreservlets.com fornece uma passagem do processo de instalação na secção de arranque rápido do seu [Tutorial: Instalar o TomCat7 e usá-lo com](https://www.youtube.com/watch?v=jOdCfW7-ybI&t=2s) artigo Eclipse.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Criar uma conta do Azure Cosmos DB

Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o Emulador do Azure Cosmos DB para este tutorial, pode avançar para o [Passo 2: Criar a aplicação Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Criar a aplicação Java JSP

Para criar a aplicação JSP:

1. Primeiro, vamos começar por criar um projeto Java. Abra o Eclipse e clique em **Ficheiro**, **Novo**, e, em seguida, clique em **Dynamic Web Project**. Se não vir **o Dynamic Web Project** listado como um projeto disponível, faça o seguinte: clique em **'Ficheiro',** clique em **Novo,** clique em **Projeto**..., expanda **a Web,** clique em **Dynamic Web Project**, e clique em **Seguinte**.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="Desenvolvimento de aplicações de Java JSP":::

1. Introduza um nome de projeto na caixa **Nome do projeto** e no menu pendente **Tempo de execução de destino**, em opção, selecione um valor (por ex. Apache Tomcat v7.0) e, em seguida, clique em **Concluir**. Selecionar um tempo de execução de destino permite-lhe executar o seu projeto localmente através do Eclipse.

1. No Eclipse, na vista do Explorador de Projeto, expanda o seu projeto. Com o botão direito, faça um clique em **WebContent**, clique em **Novo**, e, em seguida, clique em **Ficheiro JSP**.

1. Na caixa de diálogo **Novo ficheiro JSP**, atribua o nome **index.jsp** ao ficheiro. Mantenha a pasta principal **WebContent**, como apresentado na seguinte ilustração e, em seguida, clique em **Seguinte**.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Criar um novo ficheiro JSP - Tutorial de aplicações Web de Java":::

1. Na caixa de diálogo **Selecionar modelo de JSP**, no âmbito deste tutorial, selecione **Novo ficheiro JSP (html)**, e, em seguida, clique em **Concluir**.

1. Quando o ficheiro *index.jsp* abrir no Eclipse, adicione texto para exibir **Hello World!** dentro do elemento `<body>` existente. O conteúdo `<body>` atualizado deverá ser semelhante ao seguinte código:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Guarde o ficheiro *Pindex.js.*

1. Se definir um tempo de execução de destino no passo 2, pode clicar em **Projeto** e, em seguida, em **Executar** para executar localmente a sua aplicação de JSP:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Olá, Mundo – Tutorial de Aplicações Java":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Instale o SQL Java SDK

A forma mais fácil para obter o SDK do SQL Java e as respetivas dependências é através do [Apache Maven](https://maven.apache.org/). Para isso, precisa converter o seu projeto num projeto Maven utilizando os seguintes passos:

1. Faça um clique com o botão direito do rato no Explorador de Projeto, clique em **Configurar**, clique em **Converter em Projeto Maven**.

1. Na nova janela **POM,** aceite os padrão e clique em **Terminar**.

1. No **Explorador de Projeto**, abra o ficheiro pom.xml.

1. No separador **Dependências**, no painel **Dependências**, clique em **Adicionar**.

1. Na janela **Selecionar Dependência**, faça o seguinte:
   
   * Na caixa de identificação do **grupo,** insira `com.azure` .
   * Na caixa de identificação do **artefacto,** insira. `azure-cosmos`
   * Na caixa **versão,** insira `4.11.0` .
  
   Ou, pode adicionar o XML de dependência para iD do grupo e iD de artefacto diretamente ao ficheiro *pom.xml:*

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. Clique **em OK** e Maven instalará o SQL Java SDK ou salvará o ficheiro pom.xml.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Utilize o serviço DB Azure Cosmos na sua aplicação Java

Agora vamos adicionar os modelos, as vistas e os controladores à sua aplicação web.

### <a name="add-a-model"></a>Adicionar um modelo

Primeiro, vamos definir um modelo dentro de um novo ficheiro *TodoItem.java*. A `TodoItem` classe define o esquema de um item juntamente com os métodos getter e setter:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Adicione as classes de Objeto de Acesso a Dados (DAO)

Crie um objeto de acesso a dados (DAO) para abstrato persistindo os itens ToDo para Azure Cosmos DB. Para guardar os itens ToDo numa coleção, o cliente tem de saber qual a base de dados e a coleção a manter (conforme referenciado em auto-ligações). Em geral, é melhor colocar em cache a base de dados e a coleção sempre que possível, para evitar tempos de retorno adicionais para a base de dados.

1. Para invocar o serviço DB da Azure Cosmos, tem de instantanear um novo `cosmosClient` objeto. Em geral, é melhor reutilizar o `cosmosClient` objeto em vez de construir um novo cliente para cada pedido subsequente. Pode reutilizar o cliente definindo-o dentro da `cosmosClientFactory` classe. Atualize o ANFITRIÃO e MASTER_KEY valores que guardou no [passo 1](#CreateDB). Substitua a variável HOST pelo seu URI e substitua o MASTER_KEY pela chave principal. Utilize o seguinte código para criar a `CosmosClientFactory` classe dentro do ficheiro *CosmosClientFactory.java:*

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Crie um novo ficheiro *TodoDao.java* e adicione a `TodoDao` classe para criar, atualizar, ler e eliminar todos os itens:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Crie um novo ficheiro *MockDao.java* e adicione a `MockDao` classe, esta classe implementa a `TodoDao` classe para realizar operações CRUD nos itens:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Crie um novo ficheiro *.java DocDbDao* e adicione a `DocDbDao` classe. Esta classe define código para persistir os TodoItems no recipiente, recupera a sua base de dados e recolha, se ela existir, ou criar um novo caso não exista. Este exemplo usa [Gson](https://code.google.com/p/google-gson/) para serializar e des serializar os Objetos TodoItem Plain Old Java (POJOs) para documentos JSON. Para guardar os itens ToDo numa coleção, o cliente tem de saber qual a base de dados e a coleção a manter (conforme referenciado em auto-ligações). Esta classe também define a função de ajudante para recuperar os documentos por outro atributo (por exemplo, "ID") em vez de auto-ligação. Pode utilizar o método de ajuda para recuperar um documento TodoItem JSON por ID e, em seguida, deserializá-lo para um POJO.

   Também pode usar o objeto do `cosmosClient` cliente para obter uma coleção ou lista de TodoItems usando uma consulta SQL. Por fim, define o método de eliminação para eliminar um TodoItem da sua lista. O seguinte código mostra o conteúdo da `DocDbDao` classe:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Em seguida, crie um novo ficheiro *TodoDaoFactory.java* e adicione a `TodoDaoFactory` classe que cria um novo objeto DocDbDao:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Adicionar um controlador

Adicione o controlador *TodoItemController* à sua aplicação. Neste projeto, está a utilizar o [Project Lombok](https://projectlombok.org/) para gerar o construtor, os getters, os setters e um construtor. Em alternativa, pode escrever este código manualmente ou ter o IDE a gerar.:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Criar um servidão

Em seguida, crie um servlet para encaminhar os pedidos HTTP para o controlador. Crie o ficheiro *.java ApiServlet* e defina o seguinte código sob o mesmo:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Ligue o resto da app Java juntos

Agora que terminámos as partes divertidas, tudo o que resta é construir uma interface rápida de utilizador e ligá-la ao teu DAO.

1. Precisa de uma interface de utilizador web para exibir ao utilizador. Vamos reescrever o *index.jsp* que criamos anteriormente com o seguinte código:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Finalmente, escreva javaScript do lado do cliente para ligar a interface do utilizador web e o servlet juntos:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Agora, só falta testar a aplicação. Execute localmente a aplicação e adicione alguns itens Todo ao indicar o nome e a categoria do item. De seguida, clique em **Adicionar Tarefa**. Depois de o item aparecer, pode atualizar se está completo, tocando a caixa de verificação e clicando em **Tarefas de Atualização**.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Implemente a sua aplicação Java para Web Sites Azure

Os Sites do Azure tornam a implementação de aplicações Java tão simples como a exportação da sua aplicação com um ficheiro WAR e ao carregá-lo através do controlo de código fonte (por exemplo, Git) ou FTP.

1. Para exportar a sua aplicação como um ficheiro WAR, clique com o botão direito do rato no seu projeto em **Explorador de Projeto**, clique em **Exportar**, e, em seguida, clique em **Ficheiro WAR**.

1. Na janela **Exportar WAR**, efetue o seguinte procedimento:
   
   * Na caixa de projeto web, insira a amostra azure-cosmos-java.
   * Na caixa Destino, selecione um destino para guardar o ficheiro WAR.
   * Clique em **Concluir**.

1. Agora que tem um ficheiro WAR, pode simplesmente carregá-lo no diretório **webapps** do seu Site do Azure. Para obter instruções sobre o carregamento do ficheiro, veja [Adicionar uma aplicação Java às Aplicações Web do Serviço de Aplicações do Azure](../app-service/quickstart-java.md). Depois de o ficheiro WAR ser enviado para o diretório de webapps, o ambiente de tempo de execução irá detetar que o adicionou e irá carregá-lo automaticamente.

1. Para ver o seu produto concluído, navegue para `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` e comece a adicionar as suas tarefas!

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Obter o projeto a partir do GitHub

Todos os exemplos deste tutorial estão incluídos no projeto [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) no GitHub. Para importar o projeto todo para o Eclipse, certifique-se de que o software e os recursos estão listados na secção [Pré-requisitos](#Prerequisites) e, de seguida, efetue o seguinte procedimento:

1. Instalar [Project Lombok](https://projectlombok.org/). Lombok é utilizado para gerar construtores, getters e setters no projeto. Depois de ter transferido o ficheiro lombok.jar, faça duplo clique para instalá-lo ou instale-o a partir da linha de comandos.

1. Se o Eclipse estiver aberto, feche-o e reinicie-o para carregar o Lombok.

1. No Eclipse, no menu **Ficheiro**, clique em **Importar**.

1. Na janela **Importar**, clique em **Git**, clique em **Projetos a partir do Git**, e, em seguida, clique em **Seguinte**.

1. No ecrã **Selecionar Origem do Repositório**, clique em **Clonar URI**.

1. No ecrã **Repositório de Git de Origem**, na caixa **URI**, introduza https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app e, em seguida, clique em **Seguinte**.

1. No ecrã **de Seleção de Ramificação,** certifique-se de que o **main** está selecionado e, em seguida, clique em **Seguinte**.

1. No ecrã **Destino Local**, clique em **Procurar** para selecionar uma pasta em que o repositório pode ser copiado e, em seguida, clique em **Seguinte**.

1. No ecrã **Selecionar um assistente a utilizar para importar projetos**, certifique-se de ter selecionado **Importar projetos existentes** e, em seguida, clique em **Seguinte**.

1. No ecrã **Importar Projetos**, anule a seleção do projeto **DocumentDB** e, em seguida, clique em **Concluir**. O projeto do DocumentDB contém o SDK Java do Azure Cosmos DB, que iremos adicionar como uma dependência.

1. No **Project Explorer**, navegue para azure-cosmos-java-sample\src\com.microsoft.azure.cosmos.sample.dao\DocumentClientFactory.java e substitua os valores DE ANFITRIÃO e MASTER_KEY pela chave URI e PRIMARY para a sua conta DB Azure Cosmos e, em seguida, guarde o ficheiro. Para mais informações, consulte [o passo 1. Criar uma conta de base de dados Azure Cosmos.](#CreateDB)

1. No **Project Explorer,** clique à direita na **amostra azure-cosmos-java,** clique em **Build Path** e, em seguida, clique em **Configure Build Path**.

1. No ecrã **Criar Caminho Java**, no painel da direita, selecione o separador **Bibliotecas** e, em seguida, clique em **Adicionar JARs Externos**. Navegue para a localização do ficheiro lombok.jar e clique em **Abrir**, e, em seguida, clique em **OK**.

1. Aplique o passo 12 para abrir novamente a janela **Propriedades** e, em seguida, no painel da esquerda, clique em **Tempos de execução Visados**.

1. No ecrã **Tempos de execução Visados**, clique em **Novo**, selecione **Apache Tomcat v7.0** e, em seguida, clique em **OK**.

1. Aplique o passo 12 para abrir novamente a janela **Propriedades** e, em seguida, no painel da esquerda, clique em **Facetas do Projeto**.

1. No ecrã **Facetas do Projeto**, selecione **Módulo Web Dinâmico** e **Java** e, em seguida, clique em **OK**.

1. No separador **Servidores**, na parte inferior do ecrã, clique com o botão direito do rato em **Servidor Tomcat v7.0 no localhost** e, em seguida, clique em **Adicionar e Remover**.

1. Na janela **Add and Remove,** mova a **amostra azure-cosmos-java** para a caixa **configurada** e, em seguida, clique em **Terminar**.

1. No separador **Servidores**, clique com o botão direito do rato em **Tomcat v7.0 Server no localhost** e, em seguida, clique em **Reiniciar**.

1. Num browser, navegue para `http://localhost:8080/azure-cosmos-java-sample/` e comece a adicionar à sua lista de tarefas. Tenha em atenção que se tiver alterado os valores de porta predefinidos, altere 8080 para o valor que selecionou.

1. Para implementar o seu projeto num site da Azure, consulte [o Passo 6. Implemente a sua aplicação para web sites Azure](#Deploy).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construir uma aplicação node.js com a Azure Cosmos DB](sql-api-nodejs-application.md)