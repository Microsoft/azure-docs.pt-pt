---
title: ASP.NET core mvc web app tutorial usando Azure Cosmos DB
description: ASP.NET tutorial Core MVC para criar uma aplicação web MVC usando O Azure Cosmos DB. Você irá armazenar JSON e aceder a dados de uma aplicação todo hospedada no Azure App Service - ASP NET Core MVC tutorial passo a passo.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 5403725a57c68a45621d6cc509c57d864b2e0633
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164921"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Desenvolver uma aplicação web ASP.NET Core MVC com o Azure Cosmos DB utilizando o .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Este tutorial mostra-lhe como utilizar o Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação ASP.NET MVC alojada no Azure. Neste tutorial, utiliza-se o .NET SDK V3. A imagem que se segue mostra a página web que irá construir utilizando a amostra neste artigo:

![Screenshot da aplicação web da lista completa MVC criada por este tutorial - ASP NET Core MVC tutorial passo a passo](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Se não tiver tempo para completar o tutorial, pode descarregar o projeto de amostra completa do [GitHub.][GitHub]

Este tutorial aborda:

> [!div class="checklist"]
>
> * Criar uma conta do Cosmos do Azure
> * Criar uma aplicação ASP.NET Core MVC
> * Ligar a aplicação ao Azure Cosmos DB
> * Realizar operações de criação, leitura, atualização e exclusão (CRUD) nos dados

> [!TIP]
> Este tutorial assume que tem experiência prévia usando ASP.NET Core MVC e Azure App Service. Se você é novo para ASP.NET Core ou as [ferramentas pré-requisitos](#prerequisites), recomendamos que descarregue o projeto de amostra completa do [GitHub,][GitHub]adicione os pacotes NuGet necessários e execute-o. Depois de compilar o projeto, pode rever este artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Estúdio Visual 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas as imagens deste artigo são da Microsoft Visual Studio Community 2019. Se utilizar uma versão diferente, os seus ecrãs e opções podem não coincidir totalmente. A solução deve funcionar se cumprir os pré-requisitos.

## <a name="create-an-azure-cosmos-account"></a>Passo 1: Criar uma conta Azure Cosmos

Vamos começar por criar uma conta do Cosmos do Azure. Se já tem uma conta API Azure Cosmos DB SQL ou se estiver a utilizar o emulador Azure Cosmos DB, salte para o [Passo 2: Crie uma nova aplicação ASP.NET MVC.](#create-a-new-mvc-application)

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na secção seguinte, cria-se uma nova aplicação core MVC ASP.NET.

## <a name="create-a-new-mvc-application"></a>Passo 2: Criar uma nova aplicação ASP.NET Core MVC

1. Open Visual Studio e selecione **Criar um novo projeto.**

1. Em **Criar um novo projeto,** encontre e selecione ASP.NET **Aplicação Web Core** para C#. Selecione **Seguinte** para continuar.

   ![Criar novo projeto de aplicação web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Na **Configuração do seu novo projeto,** nomeie o projeto *todo* e selecione **Criar**.

1. Em **Criar uma nova aplicação Web core ASP.NET,** escolha **aplicação web (Model-View-Controller)** . Selecione **Criar** para continuar.

   O Visual Studio cria uma aplicação vazia de MVC.

1. Selecione **Debug** > **Iniciar Debugging** ou F5 para executar a sua aplicação ASP.NET localmente.

## <a name="add-nuget-packages"></a>Passo 3: Adicionar pacote Azure Cosmos DB NuGet ao projeto

Agora que temos a maior parte do código-quadro do Núcleo de MVC ASP.NET que precisamos para esta solução, vamos adicionar os pacotes NuGet necessários para ligar ao Azure Cosmos DB.

1. No **Solution Explorer,** clique no seu projeto e selecione **Gerir pacotes NuGet**.

1. No **NuGet Package Manager,** procure e selecione **Microsoft.Azure.Cosmos**. Selecione **Instalar**.

   ![Instalar pacote NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   O Visual Studio descarrega e instala o pacote Azure Cosmos DB e as suas dependências.

   Também pode utilizar a **Consola de Gestor de Pacotes** para instalar o pacote NuGet. Para isso, selecione **Tools** > **NuGet Package Manager** > Package **Manager Console**. Na linha de comandos, escreva o seguinte comando:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Passo 4: Configurar a aplicação ASP.NET Core MVC

Agora vamos adicionar os modelos, as vistas e os controladores a esta aplicação MVC.

### <a name="add-a-model"></a>Adicione um modelo

1. No **Solution Explorer,** clique à direita na pasta **Models,** selecione **Adicionar** > **Classe**.

1. Em **Adicionar Novo Item,** nomeie a sua nova classe *Item.cs* e selecione **Adicionar**.

1. Substitua o conteúdo da classe *Item.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

A Azure Cosmos DB utiliza a JSON para mover e armazenar dados. Pode utilizar o atributo `JsonProperty` para controlar como a JSON serializa e desserializa objetos. A aula de `Item` demonstra o atributo `JsonProperty`. Este código controla o formato do nome de propriedade que vai para jSON. Também renomea a propriedade .NET `Completed`.

### <a name="add-views"></a>Adicionar vistas

Em seguida, vamos criar as seguintes três vistas.

* Adicione uma visão de item da lista
* Adicione uma nova vista de item
* Adicione uma vista de item de edição

#### <a name="AddItemIndexView"></a>Adicione uma visão de item da lista

1. No **Solution Explorer,** clique à direita na pasta **Views** e selecione **Adicionar** > **nova pasta**. Nomeie o *item*da pasta .

1. Clique à direita na pasta **de Item** vazia e, em seguida, selecione **Adicionar** > **Ver**.

1. Em **Adicionar visão MVC,** forneça os seguintes valores:

   * No **nome Ver,** insira *Index*.
   * No **modelo,** selecione **Lista**.
   * Na **aula de Modelo,** selecione **Item (todo. Modelos)** .
   * Selecione **Utilize uma página** de layout e introduza *~/Views/Shared/_Layout.cshtml*.

   ![Screenshot mostrando a caixa de diálogo Adicionar MVC Ver](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Depois de adicionar estes valores, selecione **Adicionar** e deixe o Estúdio Visual criar uma nova visão de modelo.

Uma vez feito, o Visual Studio abre o ficheiro *cshtml* que cria. Pode fechar o ficheiro no Estúdio Visual. Voltaremos mais tarde.

#### <a name="AddNewIndexView"></a>Adicione uma nova vista de item

Semelhante à forma como criou uma vista para itens de lista, criar uma nova vista para criar itens com os seguintes passos:

1. No **Solution Explorer,** clique novamente na pasta **'Item',** selecione **Adicionar** > **Ver**.

1. Em **Adicionar visão MVC,** faça as seguintes alterações:

   * No **nome Ver,** *introduza Criar*.
   * No **modelo,** **selecione Criar**.
   * Na **aula de Modelo,** selecione **Item (todo. Modelos)** .
   * Selecione **Utilize uma página** de layout e introduza *~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

#### <a name="AddEditIndexView"></a>Adicione uma vista de item de edição

E, finalmente, adicione uma vista para editar um item com os seguintes passos:

1. A partir do **Solution Explorer,** clique novamente na pasta **Item,** selecione **Adicionar** > **Ver**.

1. Em **Adicionar visão MVC,** faça as seguintes alterações:

   * Na caixa **Nome da vista**, escreva *Editar*.
   * Na caixa **Modelo**, selecione **Editar**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)** .
   * Selecione **Utilize uma página** de layout e introduza *~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

Assim que completar estes passos, feche todos os *documentos cshtml* no Estúdio Visual à medida que regressa a estas vistas mais tarde.

### <a name="initialize-services"></a>Declarar e inicializar serviços

Primeiro, vamos adicionar uma classe que contém a lógica de ligar e usar o Azure Cosmos DB. Para este tutorial, vamos encapsular esta lógica numa classe chamada `CosmosDBService` e uma interface chamada `ICosmosDBService`. Este serviço faz as operações da CRUD. Também lê operações de alimentação, tais como a listagem de itens incompletos, a criação, edição e a exclusão dos itens.

1. No **Solution Explorer,** clique no seu projeto e selecione **Adicionar** > **nova pasta**. Nomeie a pasta *Serviços*.

1. Clique na pasta **Serviços,** selecione **Adicionar** > **Classe**. Nomeie a nova classe *CosmosDBService* e **selecione Adicionar**.

1. Substitua o conteúdo da *CosmosDBService.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Clique na pasta **Serviços,** selecione **Adicionar** > **Classe**. Nomeie a nova classe *ICosmosDBService* e **selecione Adicionar**.

1. Adicione o seguinte código à classe *ICosmosDBService:*

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Abra o ficheiro *Startup.cs* na sua solução e substitua o método `ConfigureServices` por:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    O código neste passo inicializa o cliente com base na configuração como uma instância singleton a ser injetada através da [injeção de dependência no Núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. Dentro do mesmo ficheiro, adicione o seguinte método **InitializeCosmosClientInstanceAsync,** que lê a configuração e inicializa o cliente.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync":::

1. Defina a configuração no ficheiro *appsettings.json* do projeto, conforme mostrado no seguinte corte:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a>Adicionar um controlador

1. No **Solution Explorer,** clique à direita na pasta **controladores,** selecione **Adicionar** > **Controlador**.

1. Em **Adicionar Andaime, selecione** **Controlador MVC - Empty** and select **.**

   ![Selecione Controlador MVC - Empty in Add Scaffold](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nomeie o seu novo *controlador Decontrolador*.

1. Substitua o conteúdo da *ItemController.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

O **atributo ValidaAntiForgeryToken** é usado aqui para ajudar a proteger esta aplicação contra ataques de falsificação de pedidos de sites. As suas opiniões também devem funcionar com este símbolo anti-falsificação. Para obter mais informações e exemplos, consulte A Prevenção de Ataques de Falsificação de [Pedidos De Cross-Site (CSRF) em ASP.NET Aplicação MVC][Preventing Cross-Site Request Forgery]. O código de origem fornecido no [GitHub][GitHub] tem a implementação completa no local.

Também usamos o atributo **Bind** no parâmetro do método para ajudar a proteger contra ataques de sobre-postagem. Para mais informações, consulte [Tutorial: Implementar funcionalidade CRUD com o Quadro de Entidades em ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="run-the-application"></a>Passo 5: Executar a aplicação localmente

Para testar a aplicação no seu computador local, utilize os seguintes passos:

1. Pressione F5 no Visual Studio para criar a aplicação no modo de depuração. Este deve compilar a aplicação e iniciar um browser com a página de grelha vazia que vimos anteriormente:

   ![Screenshot da aplicação web da lista de todo criada por este tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Se a aplicação abrir para a página inicial, apreenda `/Item` ao url.

1. Selecione o **link Criar Novo** e adicione valores aos campos **Nome** e **Descrição.** Deixe a caixa de verificação **completa** sem selecionada. Se o selecionar, a aplicação adiciona o novo item em estado completo. O item já não aparece na lista inicial.

1. Selecione **Criar**. A aplicação envia-o de volta para a vista **Index,** e o seu item aparece na lista. Pode adicionar mais alguns itens à sua lista **de A-Fazer.**

    ![Screenshot da vista Index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. **Selecione Editar** ao lado de um **Item** na lista. A aplicação abre a vista **Edit** onde pode atualizar qualquer propriedade do seu objeto, incluindo a bandeira **completa.** Se selecionar **Complete e** selecionar **Guardar,** a aplicação apresenta o **Item** como concluído na lista.

   ![Screenshot da vista Index com a caixa concluída verificada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Verifique o estado dos dados no serviço Azure Cosmos DB utilizando o [Cosmos Explorer](https://cosmos.azure.com) ou o Explorador de Dados do Emulador Dedôs Do MAD Azure.

1. Depois de testar a aplicação, selecione Ctrl+F5 para parar de depurar a aplicação. Está pronto para implementar!

## <a name="deploy-the-application-to-azure"></a>Passo 6: Implementar a aplicação

Agora que a sua aplicação completa funciona corretamente no Azure Cosmos DB, iremos implementar esta aplicação Web no Serviço de Aplicações do Azure.  

1. Para publicar esta aplicação, clique no projeto no **Solution Explorer** e selecione **Publicar**.

1. Em **Pick a publish target**, selecione App **Service**.

1. Para utilizar um perfil de Serviço de Aplicações existente, escolha **Selecione Exista**e, em seguida, **selecione Publicar**.

1. No **Serviço de Aplicações,** selecione uma **Subscrição**. Utilize o filtro **'Ver'** para ordenar por grupo de recursos ou tipo de recurso.

1. Encontre o seu perfil e, em seguida, selecione **OK**. Em seguida, procure o serviço de aplicações Azure necessário e selecione **OK**.

   ![Caixa de diálogo Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Outra opção é criar um novo perfil:

1. Tal como no procedimento anterior, clique no projeto no **Solution Explorer** e selecione **Publicar**.
  
1. Em **Pick a publish target**, selecione App **Service**.

1. Em **Pick a publish target**, selecione Create **New** e selecione **Publish**.

1. No **Serviço de Aplicações,** introduza o nome da sua Aplicação Web e a subscrição apropriada, grupo de recursos e plano de hospedagem, em seguida, selecione **Create**.

   ![Caixa de diálogo Criar Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Em poucos segundos, o Visual Studio publica a sua aplicação web e lança um navegador onde pode ver o seu projeto a funcionar em Azure!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir uma aplicação web ASP.NET Core MVC. A sua aplicação pode aceder aos dados armazenados no Azure Cosmos DB. Agora pode continuar com estes recursos:

* [Criação de partições no Azure Cosmos DB](./partitioning-overview.md)
* [Começar com consultas SQL](./how-to-sql-query.md)
* [Como modelar e criar partições de dados no Azure Cosmos DB com um exemplo do mundo real](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
