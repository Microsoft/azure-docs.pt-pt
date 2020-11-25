---
title: tutorial de aplicativo web Core MVC ASP.NET usando Azure Cosmos DB
description: ASP.NET tutorial Core MVC para criar uma aplicação web MVC usando Azure Cosmos DB. Você irá armazenar JSON e aceder a dados de uma aplicação toda hospedada no Azure App Service - ASP NET Core MVC tutorial passo a passo.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96010362"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Desenvolver uma aplicação web core MVC ASP.NET com a Azure Cosmos DB utilizando .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Este tutorial mostra-lhe como usar a Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação MVC ASP.NET que está hospedada no Azure. Neste tutorial, utilize o .NET SDK V3. A imagem a seguir mostra a página web que irá construir utilizando a amostra deste artigo:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Screenshot da aplicação web MVC de lista de todos criada por este tutorial - ASP NET Core MVC tutorial passo a passo":::

Se não tiver tempo para completar o tutorial, pode descarregar o projeto completo de amostras do [GitHub.][GitHub]

Este tutorial aborda:

> [!div class="checklist"]
>
> * Criação de uma conta Azure Cosmos
> * Criar uma aplicação core MVC ASP.NET
> * Ligação da app à Azure Cosmos DB
> * Realizar operações de criação, leitura, atualização e eliminação (CRUD) nos dados

> [!TIP]
> Este tutorial pressupõe que tem experiência prévia usando ASP.NET Core MVC e Azure App Service. Se você é novo para ASP.NET Core ou as [ferramentas pré-requisitos](#prerequisites), recomendamos que você descarregue o projeto de amostra completa do [GitHub][GitHub], adicione os pacotes NuGet necessários, e execute-o. Assim que construir o projeto, pode rever este artigo para obter informações sobre o código no contexto do projeto.

## <a name="prerequisites"></a><a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que dispõe dos seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Estúdio Visual 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas as imagens deste artigo são da Microsoft Visual Studio Community 2019. Se utilizar uma versão diferente, os ecrãs e opções podem não corresponder inteiramente. A solução deve funcionar se cumprir os pré-requisitos.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Passo 1: Criar uma conta Azure Cosmos

Vamos começar por criar uma conta Azure Cosmos. Se já tem uma conta Azure Cosmos DB SQL API ou se estiver a utilizar o Emulador Azure Cosmos DB, salte para o [Passo 2: Crie uma nova aplicação MVC ASP.NET](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na secção seguinte, cria-se uma nova aplicação core MVC ASP.NET.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Passo 2: Criar uma nova aplicação core MVC ASP.NET

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, find and select **ASP.NET Core Web Application** for C#. Selecione **Seguinte** para continuar.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Criar novo projeto de aplicação web core ASP.NET":::

1. Em **Configurar o seu novo projeto,** nomeie o projeto *todo* e selecione **Criar.**

1. In **Create a new ASP.NET Core Web Application,** escolha a **Aplicação Web (Model-View-Controller)**. Selecione **Criar** para continuar.

   O Visual Studio cria uma aplicação MVC vazia.

1. Selecione **Debug**  >  **Start Debugging** ou F5 para executar a sua aplicação ASP.NET localmente.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Passo 3: Adicione o pacote DB NuGet da Azure Cosmos ao projeto

Agora que temos a maior parte do código-quadro core MVC ASP.NET que precisamos para esta solução, vamos adicionar os pacotes NuGet necessários para ligar ao Azure Cosmos DB.

1. No **Solution Explorer,** clique com o botão direito do seu projeto e selecione **Gerir pacotes NuGet**.

1. No **NuGet Package Manager,** procure e selecione **Microsoft.Azure.Cosmos**. Selecione **Install** (Instalar).

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="Instalar pacote NuGet":::

   Visual Studio descarrega e instala o pacote Azure Cosmos DB e suas dependências.

   Também pode utilizar **a Consola Gestor de Pacotes** para instalar o pacote NuGet. Para tal, selecione **Ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola**. No pedido, digite o seguinte comando:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Passo 4: Configurar a aplicação core MVC ASP.NET

Agora vamos adicionar os modelos, as vistas e os controladores a esta aplicação MVC.

### <a name="add-a-model"></a><a name="add-a-model"></a> Adicionar um modelo

1. No **Solution Explorer,** clique à direita na pasta **Modelos,** selecione **Add**  >  **Class**.

1. In **Add New Item**, nomeie a sua nova classe *Item.cs* e selecione **Add**.

1. Substitua o conteúdo da classe *Item.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

A Azure Cosmos DB usa a JSON para mover e armazenar dados. Pode utilizar o `JsonProperty` atributo para controlar como o JSON serializa e desseriza objetos. A `Item` classe demonstra o `JsonProperty` atributo. Este código controla o formato do nome da propriedade que vai para JSON. Também renomea a propriedade `Completed` .NET.

### <a name="add-views"></a><a name="add-views"></a>Adicionar vistas

Em seguida, vamos adicionar as seguintes vistas.

* Uma vista de itens de criação
* Uma vista de produto de exclusão
* Uma vista para obter detalhes de um item
* Uma vista de artigo de edição
* Uma vista para listar todos os itens

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Criar vista de item

1. No **Solution Explorer,** clique com o botão direito da pasta **Vistas** e selecione **Adicionar**  >  **Nova Pasta**. Nomeie a pasta *Item*.

1. Clique com o botão direito na pasta **item** vazio e, em seguida, selecione **Add**  >  **View**.

1. In **Add MVC View,** faça as seguintes alterações:

   * No **nome Ver,** insira *Criar*.
   * No **Modelo,** selecione **Criar**.
   * Na **classe Modelo,** selecione **Item (todo. Modelos)**.
   * Selecione **Utilize uma página de layout** e *insira ~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="Screenshot mostrando a caixa de diálogo Add MVC View":::

1. Em seguida, **selecione Add** e deixe o Visual Studio criar uma nova vista de modelo. Substitua o código no ficheiro gerado pelos seguintes conteúdos:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Eliminar a vista do artigo

1. A partir do **Solution Explorer,** clique com o botão direito na pasta **Item** novamente, selecione **Add**  >  **View**.

1. In **Add MVC View,** faça as seguintes alterações:

   * Na caixa **de nomes Ver,** *escreva Eliminar*.
   * Na caixa **de modelo,** selecione **Eliminar**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)**.
   * Selecione **Utilize uma página de layout** e *insira ~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

1. Em seguida, **selecione Add** e deixe o Visual Studio criar uma nova vista de modelo. Substitua o código no ficheiro gerado pelos seguintes conteúdos:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Adicionar uma vista para obter detalhes de um item

1. No **Solution Explorer,** clique com o botão direito na pasta **Item** novamente, selecione **Add**  >  **View**.

1. No **Add MVC View,** forneça os seguintes valores:

   * No **ver o nome,** insira *detalhes*.
   * No **modelo,** selecione **Detalhes.**
   * Na **classe Modelo,** selecione **Item (todo. Modelos)**.
   * Selecione **Utilize uma página de layout** e *insira ~/Views/Shared/_Layout.cshtml*.

1. Em seguida, **selecione Add** e deixe o Visual Studio criar uma nova vista de modelo. Substitua o código no ficheiro gerado pelos seguintes conteúdos:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Adicione uma vista de artigo de edição

1. A partir do **Solution Explorer,** clique com o botão direito na pasta **Item** novamente, selecione **Add**  >  **View**.

1. In **Add MVC View,** faça as seguintes alterações:

   * Na caixa **Nome da vista**, escreva *Editar*.
   * Na caixa **Modelo**, selecione **Editar**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)**.
   * Selecione **Utilize uma página de layout** e *insira ~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

1. Em seguida, **selecione Add** e deixe o Visual Studio criar uma nova vista de modelo. Substitua o código no ficheiro gerado pelos seguintes conteúdos:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Adicionar uma vista para listar todos os itens

E, finalmente, adicione uma vista para obter todos os itens com os seguintes passos:

1. A partir do **Solution Explorer,** clique com o botão direito na pasta **Item** novamente, selecione **Add**  >  **View**.

1. In **Add MVC View,** faça as seguintes alterações:

   * Na caixa **Nome da vista**, escreva *Índice*.
   * Na caixa **Modelo**, selecione **Lista**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)**.
   * Selecione **Utilize uma página de layout** e *insira ~/Views/Shared/_Layout.cshtml*.
   * Selecione **Adicionar**.

1. Em seguida, **selecione Add** e deixe o Visual Studio criar uma nova vista de modelo. Substitua o código no ficheiro gerado pelos seguintes conteúdos:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

Assim que completar estes passos, feche todos os *documentos cshtml* no Visual Studio.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Declarar e inicializar serviços

Primeiro, vamos adicionar uma classe que contém a lógica para ligar e usar Azure Cosmos DB. Para este tutorial, vamos encapsular esta lógica numa classe chamada `CosmosDbService` e numa interface chamada `ICosmosDbService` . Este serviço faz as operações da CRUD. Também lê operações de feed, tais como listar itens incompletos, criar, editar e eliminar os itens.

1. No **Solution Explorer,** clique com o botão direito do seu projeto e selecione **Adicionar**  >  **Nova Pasta**. Nomeie os *Serviços de* pasta .

1. Clique com o botão **Add** direito na pasta **Serviços,** selecione  >  **Add Class**. Nomeie a nova classe *CosmosDbService* e **selecione Add**.

1. Substitua o conteúdo da *CosmosDbService.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Clique com o botão **Add** direito na pasta **Serviços,** selecione  >  **Add Class**. Nomeie a nova classe *ICosmosDbService* e selecione **Add**.

1. Adicione o seguinte código à classe *ICosmosDbService:*

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Abra o ficheiro *Startup.cs* na sua solução e adicione o seguinte método **InitializeCosmosClientInstanceAsync,** que lê a configuração e inicializa o cliente.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. No mesmo ficheiro, substitua o `ConfigureServices` método por:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   O código neste passo inicializa o cliente com base na configuração como um caso singleton a ser injetado através da [injeção de Dependência no ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).

   E certifique-se de alterar o Controlador MVC predefinido para `Item` editar as rotas no `Configure` método do mesmo ficheiro:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Defina a configuração noappsettings.jsdo projeto *em* arquivo, como mostrado no seguinte corte:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Adicionar um controlador

1. No **Solution Explorer,** clique com o botão direito na pasta **Controladores,** selecione **Add**  >  **Controller**.

1. In **Add Scaffold**, selecione Controlador **MVC - Esvazie** e selecione **Adicionar**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="Selecione controlador MVC - Esvazie em Andaime adicionar":::

1. Nomeie o seu novo controlador *ItemController*.

1. Substitua o conteúdo da *ItemController.cs* pelo seguinte código:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

O atributo **ValidateAntiForgeryToken** é utilizado aqui para ajudar a proteger esta aplicação contra ataques de falsificação de pedidos de transem locais. Os seus pontos de vista também devem funcionar com este símbolo anti-falsificação. Para obter mais informações e exemplos, consulte [ataques de falsificação de pedidos de prevenção (CSRF) em ASP.NET aplicação MVC][Preventing Cross-Site Request Forgery]. O código de origem fornecido no [GitHub][GitHub] tem a implementação completa no local.

Também usamos o atributo **Bind** no parâmetro do método para ajudar a proteger contra ataques de sobre-postagem. Para obter mais informações, consulte [Tutorial: Implementar a funcionalidade CRUD com o Quadro de Entidades em ASP.NET MVC.][Basic CRUD Operations in ASP.NET MVC]

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Passo 5: Executar a aplicação localmente

Para testar a aplicação no seu computador local, utilize os seguintes passos:

1. Prima F5 no Visual Studio para construir a aplicação em modo depuramento. Este deve compilar a aplicação e iniciar um browser com a página de grelha vazia que vimos anteriormente:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Screenshot da aplicação web de lista de todos criada por este tutorial":::
   
   Se a aplicação abrir para a página inicial, `/Item` apendam-se à url.

1. Selecione o link **Criar Novo** e adicione valores aos campos **Nome** e **Descrição.** Deixe a caixa de verificação **completa** não sersselhada. Se o selecionar, a aplicação adiciona o novo item num estado preenchido. O item já não aparece na lista inicial.

1. Selecione **Criar**. A aplicação envia-o de volta para a vista **'Índice'** e o seu item aparece na lista. Pode adicionar mais alguns itens à sua lista **de A-Fazer.**

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Screenshot da vista do Índice":::
  
1. **Selecione Editar** ao lado de um **Item** na lista. A aplicação abre a vista **Editar** onde pode atualizar qualquer propriedade do seu objeto, incluindo a bandeira **completa.** Se selecionar **Complete e** selecionar **Guardar,** a aplicação exibe o **Item** tal como concluído na lista.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Screenshot da vista do Índice com a caixa completa verificada":::

1. Verifique o estado dos dados no serviço DB Azure Cosmos utilizando o [Cosmos Explorer](https://cosmos.azure.com) ou o Explorador de Dados do Azure Cosmos DB Emulator.

1. Depois de testar a aplicação, selecione Ctrl+F5 para parar de depurar a aplicação. Está pronto para implementar!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Passo 6: Implementar a aplicação

Agora que a sua aplicação completa funciona corretamente no Azure Cosmos DB, iremos implementar esta aplicação Web no Serviço de Aplicações do Azure.  

1. Para publicar esta aplicação, clique com o botão direito no **Solution Explorer** e selecione **Publicar.**

1. In **Pick a publish target**, selecione App **Service**.

1. Para utilizar um perfil de Serviço de Aplicações existente, escolha **Select Existing**, em seguida, selecione **Publicar**.

1. No **Serviço de Aplicações,** selecione uma **Subscrição.** Utilize o filtro **'Ver'** para ordenar por grupo de recursos ou tipo de recurso.

1. Encontre o seu perfil e, em seguida, selecione **OK**. Em seguida, pesque o Serviço de Aplicações Azure necessário e selecione **OK**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Caixa de diálogo Serviço de Aplicações no Visual Studio":::

Outra opção é criar um novo perfil:

1. Como no procedimento anterior, clique com o botão direito no projeto no **Solution Explorer** e selecione **Publicar**.
  
1. In **Pick a publish target**, selecione App **Service**.

1. Em **Escolher um alvo de publicação,** selecione Create **New** e selecione **Publish**.

1. No **Serviço de Aplicações**, insira o nome da sua Aplicação Web e o plano de subscrição, grupo de recursos e hospedagem apropriados, selecione **Create**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Caixa de diálogo Criar Serviço de Aplicações no Visual Studio":::

Em poucos segundos, o Visual Studio publica a sua aplicação web e lança um browser onde pode ver o seu projeto a decorrer no Azure!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a construir uma aplicação web core MVC ASP.NET. A sua aplicação pode aceder aos dados armazenados na Azure Cosmos DB. Agora pode continuar com estes recursos:

* [Criação de partições no Azure Cosmos DB](./partitioning-overview.md)
* [Introdução às consultas SQL](./sql-query-getting-started.md)
* [Como modelar e criar partições de dados no Azure Cosmos DB com um exemplo do mundo real](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
