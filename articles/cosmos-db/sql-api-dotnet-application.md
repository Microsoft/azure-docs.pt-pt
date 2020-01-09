---
title: Tutorial de ASP.NET Core aplicativo Web MVC usando Azure Cosmos DB
description: ASP.NET Core tutorial do MVC para criar um aplicativo Web MVC usando Azure Cosmos DB. Você armazenará dados JSON e Access de um aplicativo de tarefas hospedados no tutorial Azure App Service-ASP NET Core MVC passo a passo.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: b0da9f2f2d14c0487e61c1927b5456d09052cff3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444919"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: desenvolver um aplicativo Web ASP.NET Core MVC com Azure Cosmos DB usando o SDK do .NET

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Este tutorial mostra-lhe como utilizar o Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação ASP.NET MVC alojada no Azure. Neste tutorial, você usa o SDK do .NET v3. A imagem a seguir mostra a página da Web que você criará usando o exemplo neste artigo:

![Captura de tela do aplicativo Web MVC da lista de tarefas criada por este tutorial-passo a passo do tutorial do ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Se você não tiver tempo para concluir o tutorial, poderá baixar o projeto de exemplo completo do [GitHub][GitHub].

Este tutorial aborda:

> [!div class="checklist"]
>
> * Criar uma conta do Cosmos do Azure
> * Criando um aplicativo MVC ASP.NET Core
> * Ligar a aplicação ao Azure Cosmos DB
> * Executando operações CRUD (criar, ler, atualizar e excluir) nos dados

> [!TIP]
> Este tutorial pressupõe que você tenha experiência anterior com o ASP.NET Core MVC e Azure App Service. Se você for novo no ASP.NET Core ou nas [ferramentas de pré-requisito](#prerequisites), é recomendável baixar o projeto de exemplo completo do [GitHub][GitHub], adicionar os pacotes do NuGet necessários e executá-lo. Depois de compilar o projeto, pode rever este artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções neste artigo, verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas as capturas de tela neste artigo são de Microsoft Visual Studio Community 2019. Se você usar uma versão diferente, suas telas e opções poderão não coincidir totalmente. A solução deverá funcionar se você atender aos pré-requisitos.

## <a name="create-an-azure-cosmos-account"></a>Passo 1: Criar uma conta do Cosmos do Azure

Vamos começar por criar uma conta do Cosmos do Azure. Se você já tiver uma conta da API do SQL Azure Cosmos DB ou se estiver usando o emulador de Azure Cosmos DB, pule para a [etapa 2: criar um novo aplicativo ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na próxima seção, você criará um novo aplicativo ASP.NET Core MVC.

## <a name="create-a-new-mvc-application"></a>Etapa 2: criar um novo aplicativo MVC ASP.NET Core

1. Abra o Visual Studio e selecione **criar um novo projeto**.

1. Em **criar um novo projeto**, localize e selecione **ASP.NET Core aplicativo Web** para C#. Selecione **Seguinte** para continuar.

   ![Criar novo projeto de aplicativo Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Em **configurar seu novo projeto**, nomeie o projeto *todo* e selecione **criar**.

1. Em **criar um novo aplicativo web ASP.NET Core**, escolha **aplicativo Web (Model-View-Controller)** . Selecione **criar** para continuar.

   O Visual Studio cria um aplicativo MVC vazio.

1. Selecione **depurar** > **Iniciar Depuração** ou F5 para executar o aplicativo ASP.net localmente.

## <a name="add-nuget-packages"></a>Passo 3: Adicionar o pacote NuGet do Azure Cosmos DB ao projeto

Agora que temos a maior parte do código ASP.NET Core MVC Framework que precisamos para essa solução, vamos adicionar os pacotes NuGet necessários para se conectar ao Azure Cosmos DB.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**.

1. No **Gerenciador de pacotes NuGet**, procure e selecione **Microsoft. Azure. Cosmos**. Selecione **Instalar**.

   ![Instalar o pacote NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   O Visual Studio baixa e instala o pacote Azure Cosmos DB e suas dependências.

   Você também pode usar o **console do Gerenciador de pacotes** para instalar o pacote NuGet. Para fazer isso, selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **console do Gerenciador de pacotes**. Na linha de comandos, escreva o seguinte comando:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Etapa 4: configurar o aplicativo MVC ASP.NET Core

Agora, vamos adicionar os modelos, as exibições e os controladores a esse aplicativo MVC.

### <a name="add-a-model"></a> Adicionar um modelo

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **modelos** , selecione **Adicionar** > **classe**.

1. Em **Adicionar novo item**, nomeie sua nova classe *Item.cs* e selecione **Adicionar**.

1. Substitua o conteúdo da classe *Item.cs* pelo código a seguir:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]

Azure Cosmos DB usa JSON para mover e armazenar dados. Você pode usar o atributo `JsonProperty` para controlar como o JSON serializa e desserializa objetos. A classe `Item` demonstra o atributo `JsonProperty`. Esse código controla o formato do nome da propriedade que entra em JSON. Ele também renomeia a propriedade .NET `Completed`.

### <a name="add-views"></a>Adicionar vistas

Em seguida, vamos criar as três exibições a seguir.

* Adicionar um modo de exibição de item de lista
* Adicionar um novo modo de exibição de item
* Adicionar uma exibição Editar Item

#### <a name="AddItemIndexView"></a>Adicionar uma vista de item de lista

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **exibições** e selecione **Adicionar** > **nova pasta**. Nomeie o *Item*de pasta.

1. Clique com o botão direito do mouse na pasta **Item** vazio e selecione **Adicionar** > **exibição**.

1. Em **Adicionar exibição MVC**, forneça os seguintes valores:

   * Em **nome da exibição**, insira *índice*.
   * Em **modelo**, selecione **lista**.
   * Em **classe de modelo**, selecione **Item (todo. Modelos)** .
   * Selecione **usar uma página de layout** e insira *~/views/Shared/_Layout. cshtml*.

   ![Captura de tela mostrando a caixa de diálogo Adicionar exibição do MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Depois de adicionar estes valores, selecione **adicionar** e permita que o Visual Studio, criar uma nova vista de modelo.

Depois de concluído, o Visual Studio abre o arquivo *cshtml* que ele cria. Você pode fechar esse arquivo no Visual Studio. Voltaremos a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma nova vista de item

Semelhante à forma como criou uma vista para itens de lista, criar uma nova vista para criar itens com os seguintes passos:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **Item** novamente, selecione **Adicionar** > **exibição**.

1. Em **Adicionar exibição MVC**, faça as seguintes alterações:

   * Em **nome da exibição**, digite *criar*.
   * Em **modelo**, selecione **criar**.
   * Em **classe de modelo**, selecione **Item (todo. Modelos)** .
   * Selecione **usar uma página de layout** e insira *~/views/Shared/_Layout. cshtml*.
   * Selecione **Adicionar**.

#### <a name="AddEditIndexView"></a>Adicionar uma vista de item de edição

E, finalmente, adicione uma vista para editar um item com os seguintes passos:

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **Item** novamente, selecione **Adicionar** > **exibição**.

1. Em **Adicionar exibição MVC**, faça as seguintes alterações:

   * Na caixa **Nome da vista**, escreva *Editar*.
   * Na caixa **Modelo**, selecione **Editar**.
   * Na caixa **Classe de modelo**, selecione **Item (todo.Models)** .
   * Selecione **usar uma página de layout** e insira *~/views/Shared/_Layout. cshtml*.
   * Selecione **Adicionar**.

Depois de concluir essas etapas, feche todos os documentos *cshtml* no Visual Studio conforme retornar a esses modos de exibição posteriormente.

### <a name="add-a-controller"></a>Adicionar um controlador

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **controladores** , selecione **Adicionar** > **controlador**.

1. Em **Adicionar Scaffold**, selecione **controlador MVC-vazio** e selecione **Adicionar**.

   ![Selecione controlador MVC-vazio em Adicionar Scaffold](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nomeie seu *novo controlador do controlador.*

1. Substitua o conteúdo de *ItemController.cs* pelo código a seguir:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

O **ValidateAntiForgeryToken** atributo é utilizado aqui para ajudar a proteger esta aplicação contra ataques de falsificação de solicitação entre sites. Suas exibições também devem funcionar com esse token antifalsificação. Para obter mais informações e exemplos, consulte [impedindo ataques CSRF (solicitação intersite forjada) no aplicativo ASP.NET MVC][Preventing Cross-Site Request Forgery]. O código de origem fornecido no [GitHub][GitHub] tem a implementação completa no local.

Também utilizamos a **vincular** atributo no parâmetro do método para ajudar a proteger contra ataques de publicação excessiva. Para obter mais informações, consulte [tutorial: implementar a funcionalidade CRUD com o Entity Framework no ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Passo 5: Ligar ao Azure Cosmos DB

Agora que as coisas padrão do MVC são levadas em volta, vamos adicionar o código para se conectar ao Azure Cosmos DB e realizar operações CRUD.

### <a name="perform-crud-operations"></a>Executar operações CRUD nos dados

Primeiro, adicionaremos uma classe que contém a lógica para se conectar e usar Azure Cosmos DB. Para este tutorial, encapsularemos essa lógica em uma classe chamada `CosmosDBService` e uma interface chamada `ICosmosDBService`. Esse serviço faz as operações CRUD. Ele também faz operações de feed de leitura, como listar itens incompletos, criar, editar e excluir os itens.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em seu projeto e selecione **Adicionar** > **nova pasta**. Nomeie a pasta *Serviços*.

1. Clique com o botão direito do mouse na pasta **Serviços** , selecione **Adicionar** > **classe**. Nomeie a nova classe *CosmosDBService* e selecione **Adicionar**.

1. Substitua o conteúdo de *CosmosDBService.cs* pelo código a seguir:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Repita as duas etapas anteriores, mas desta vez, use o nome *ICosmosDBService*e use o seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]

1. No manipulador **configureservices** , adicione a seguinte linha:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    O código na etapa anterior recebe um `CosmosClient` como parte do construtor. A seguir ASP.NET Core pipeline, precisamos acessar o arquivo *Startup.cs* do projeto. O código nesta etapa Inicializa o cliente com base na configuração como uma instância singleton a ser injetada por meio [da injeção de dependência no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. No mesmo arquivo, adicione o seguinte método **InitializeCosmosClientInstanceAsync**, que lê a configuração e inicializa o cliente.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Defina a configuração no arquivo *appSettings. JSON* do projeto. Abra o arquivo e adicione uma seção chamada **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

Se você executar o aplicativo, o pipeline do ASP.NET Core instanciará **CosmosDbService** e manterá uma única instância como singleton. Quando o **controlador de domínio** processa solicitações do lado do cliente, ele recebe essa única instância e pode usá-la para operações CRUD.

Se você criar e executar este projeto agora, você verá algo parecido com este:

![Captura de tela do aplicativo Web de lista de tarefas criadas por este tutorial de banco de dados](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>Passo 6: executar a aplicação localmente

Para testar o aplicativo no computador local, use as seguintes etapas:

1. Selecione F5 no Visual Studio para compilar o aplicativo no modo de depuração. Este deve compilar a aplicação e iniciar um browser com a página de grelha vazia que vimos anteriormente:

   ![Captura de tela do aplicativo Web da lista de tarefas criada por este tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. Selecione o link **criar novo** e adicione valores aos campos **nome** e **Descrição** . Deixe a caixa de seleção **concluído** desmarcada. Se você selecioná-lo, o aplicativo adicionará o novo item em um estado concluído. O item não aparece mais na lista inicial.

1. Selecione **Criar**. O aplicativo envia de volta para o modo de exibição de **índice** e seu item é exibido na lista. Você pode adicionar mais alguns itens à sua lista **de tarefas pendentes** .

    ![Captura de tela da exibição de índice](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Selecione **Editar** ao lado de um **Item** na lista. O aplicativo abre a exibição **Editar** , na qual você pode atualizar qualquer Propriedade do objeto, incluindo o sinalizador **concluído** . Se você selecionar **concluído** e selecionar **salvar**, o aplicativo exibirá o **Item** como concluído na lista.

   ![Captura de tela da exibição de índice com a caixa concluída marcada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Verifique o estado dos dados no serviço de Azure Cosmos DB usando o [Cosmos Explorer](https://cosmos.azure.com) ou o data Explorer do emulador de Azure Cosmos DB.

1. Depois de testar o aplicativo, selecione CTRL + F5 para parar a depuração do aplicativo. Está pronto para implementar!

## <a name="deploy-the-application-to-azure"></a>Passo 7: Implementar a aplicação

Agora que a sua aplicação completa funciona corretamente no Azure Cosmos DB, iremos implementar esta aplicação Web no Serviço de Aplicações do Azure.  

1. Para publicar este aplicativo, clique com o botão direito do mouse no projeto no **Gerenciador de soluções** e selecione **publicar**.

1. Em **escolher um destino de publicação**, selecione **serviço de aplicativo**.

1. Para usar um perfil de serviço de aplicativo existente, escolha **selecionar existente**e, em seguida, selecione **publicar**.

1. Em **serviço de aplicativo**, selecione uma **assinatura**. Utilize o **vista** filtro para ordenar por grupo de recursos ou tipo de recurso.

1. Localize seu perfil e selecione **OK**. Em seguida, procure o serviço de aplicações do Azure necessários e selecione **OK**.

   ![Caixa de diálogo Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Outra opção é criar um novo perfil:

1. Como no procedimento anterior, clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **publicar**.
  
1. Em **escolher um destino de publicação**, selecione **serviço de aplicativo**.

1. Em **escolher um destino de publicação**, selecione **criar novo** e selecione **publicar**.

1. No **serviço de aplicativo**, insira o nome do aplicativo Web e a assinatura, o grupo de recursos e o plano de hospedagem apropriados e, em seguida, selecione **criar**.

   ![Caixa de diálogo Criar Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Em alguns segundos, o Visual Studio publica seu aplicativo Web e inicia um navegador onde você pode ver seu projeto em execução no Azure!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a criar um aplicativo Web ASP.NET Core MVC. Seu aplicativo pode acessar dados armazenados no Azure Cosmos DB. Agora você pode continuar com estes recursos:

* [Criação de partições no Azure Cosmos DB](./partitioning-overview.md)
* [Introdução às consultas SQL](./how-to-sql-query.md)
* [Como modelar e criar partições de dados no Azure Cosmos DB com um exemplo do mundo real](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
