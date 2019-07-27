---
title: 'ASP.NET Core tutorial do MVC para Azure Cosmos DB: Desenvolvimento de aplicativos Web'
description: ASP.NET Core tutorial do MVC para criar um aplicativo Web MVC usando Azure Cosmos DB. Você armazenará dados JSON e Access de um aplicativo de tarefas hospedados no tutorial Azure App Service-ASP NET Core MVC passo a passo.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67985907"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Desenvolver um aplicativo Web ASP.NET Core MVC com Azure Cosmos DB usando o SDK do .NET 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Este tutorial mostra-lhe como utilizar o Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação ASP.NET MVC alojada no Azure. Neste tutorial, você usa o SDK do .NET v3. A imagem seguinte mostra a página da web que criará utilizando o exemplo neste artigo:
 
![Captura de tela do aplicativo Web MVC da lista de tarefas criada por este tutorial-passo a passo do tutorial do ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Se você não tiver tempo para concluir o tutorial, poderá baixar o projeto de exemplo completo do [GitHub][GitHub].

Este tutorial aborda:

> [!div class="checklist"]
> * Criar uma conta do Cosmos do Azure
> * Criando um aplicativo MVC ASP.NET Core
> * Ligar a aplicação ao Azure Cosmos DB 
> * Execute as operações CRUD nos dados

> [!TIP]
> Este tutorial pressupõe que você tenha experiência anterior com o ASP.NET Core MVC e Azure App Service. Se você for novo no ASP.NET Core ou nas [ferramentas de pré-requisito](#prerequisites), recomendamos baixar o projeto de exemplo completo do [GitHub][GitHub], adicionar os pacotes NuGet necessários e executá-lo. Depois de compilar o projeto, pode rever este artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos:

* **Uma conta ativa do Azure:** Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Todas as capturas de tela neste artigo foram tiradas usando Microsoft Visual Studio Community 2019. Se o sistema estiver configurado com uma versão diferente, é possível que suas opções e ecrãs podem não coincidam na totalidade, mas se cumpre os pré-requisitos acima esta solução deverá funcionar.

## <a name="create-an-azure-cosmos-account"></a>Etapa 1: Criar uma conta do Azure Cosmos

Vamos começar por criar uma conta do Cosmos do Azure. Se já tiver uma conta do Azure Cosmos DB SQL API ou se estiver a utilizar o emulador do Azure Cosmos DB para este tutorial, pode avançar para [criar uma nova aplicação ASP.NET MVC](#create-a-new-mvc-application) secção.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Na próxima seção, você criará um novo aplicativo ASP.NET Core MVC. 

## <a name="create-a-new-mvc-application"></a>Etapa 2: Criar um novo aplicativo MVC ASP.NET Core

1. No Visual Studio, do **arquivo** menu, escolha **New**e, em seguida, selecione **projeto**. Aparece a caixa de diálogo **Novo Projeto**.

2. Na janela **novo projeto** , use a caixa **de entrada Pesquisar modelos** para procurar "Web" e, em seguida, selecione **ASP.NET Core aplicativo Web**. 

   ![Criar novo projeto de aplicativo Web ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Na caixa **Nome**, escreva o nome do projeto. Este tutorial utiliza o nome “todo” (tarefas). Se você optar por usar algo diferente desse nome, sempre que este tutorial falar sobre o namespace de todo, ajuste os exemplos de código fornecidos para usar o que você tiver nomeado para seu aplicativo. 

4. Selecione **procurar** para navegar até a pasta onde você gostaria de criar o projeto. Selecione **Criar**. 

5. A caixa de diálogo **criar um novo aplicativo Web ASP.NET Core** é exibida. Na lista modelos, selecione **aplicativo Web (Model-View-Controller)** .

6. Selecione **criar** e deixe que o Visual Studio faça o scaffolding em relação ao modelo ASP.NET Core MVC vazio. 

7. Assim que o Visual Studio tenha terminado de criar a aplicação MVC de texto clichê, terá uma aplicação ASP.NET vazia que pode ser executado localmente.

## <a name="add-nuget-packages"></a>Etapa 3: Adicionar Azure Cosmos DB pacote NuGet ao projeto

Agora que temos a maior parte do código ASP.NET Core MVC Framework que precisamos para essa solução, vamos adicionar os pacotes NuGet necessários para se conectar ao Azure Cosmos DB.

1. O SDK .NET do Azure Cosmos DB é compactado e distribuído como um pacote NuGet. Para obter o pacote NuGet no Visual Studio, utilize o Gestor de pacotes de NuGet no Visual Studio ao clicar com o botão direito no projeto no **Explorador de soluções** e, em seguida, selecione **gerir pacotes NuGet**.
   
   ![Captura de tela das opções de clique com o botão direito do mouse para o projeto de aplicativo Web no Gerenciador de Soluções, com gerenciar pacotes NuGet realçados.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. A caixa de diálogo **Gerir Pacotes NuGet** aparece. Do NuGet **navegue** , escreva **Microsoft.Azure.Cosmos**. Nos resultados, instale o pacote **Microsoft. Azure. Cosmos** . Ele baixa e instala o pacote Azure Cosmos DB e suas dependências. Selecione **aceito** na janela de **aceitação da licença** para concluir a instalação.
   
   Em alternativa, pode utilizar a consola de Gestor de pacotes para instalar o pacote de NuGet. Para fazer isso, sobre o **ferramentas** menu, selecione **Gestor de pacotes NuGet**e, em seguida, selecione **Package Manager Console**. Na linha de comandos, escreva o seguinte comando:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Depois que o pacote é instalado, seu projeto do Visual Studio deve conter a referência de biblioteca para Microsoft. Azure. Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Etapa 4: Configurar o aplicativo MVC ASP.NET Core

Agora vamos adicionar os modelos, vistas e os controladores a esta aplicação MVC:

* [Adicionar um modelo](#add-a-model).
* [Adicionar um controlador](#add-a-controller).
* [Adicionar vistas](#add-views).

### <a name="add-a-model"></a> Adicionar um modelo

1. Do **Explorador de soluções**, clique com botão direito a **modelos** pasta, selecione **adicionar**e, em seguida, selecione **classe**. A caixa de diálogo **Adicionar Novo Item** é apresentada.

1. Nomeie a nova classe **Item.cs** e selecione **Adicionar**. 

1. Em seguida, substitua o código na classe "Item.cs" pelo código a seguir:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Os dados armazenados no Azure Cosmos DB são transmitidos e armazenados como JSON. Para controlar a maneira como os objetos são serializados/desserializados pelo JSON.NET, você pode  usar o atributo jsonproperty conforme demonstrado na classe de **Item** que você criou. Você não só pode controlar o formato do nome da propriedade que vai para o JSON, também pode renomear suas propriedades do .NET como você fez com a propriedade **Completed** . 

### <a name="add-a-controller"></a>Adicionar um controlador

1. Do **Explorador de soluções**, com o botão direito a **controladores** pasta, selecione **Add**e, em seguida, selecione **controlador**. A caixa de diálogo **Adicionar Estrutura** é apresentada.

1. Selecione **controlador MVC-vazio** e selecione **Adicionar**.

   ![Captura de tela da caixa de diálogo Adicionar Scaffold com o controlador MVC – opção vazia realçada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Nomeie o novo controlador, o **controlador de domínio**e substitua o código desse arquivo pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   O **ValidateAntiForgeryToken** atributo é utilizado aqui para ajudar a proteger esta aplicação contra ataques de falsificação de solicitação entre sites. Há mais que apenas adicionar este atributo, seus modos de exibição devem funcionar com este token antifalsificação também. Para obter mais informações sobre o assunto e exemplos de como implementá-los corretamente, consulte impedindo falsificação de [solicitação entre sites][Preventing Cross-Site Request Forgery]. O código de origem fornecido no [GitHub][GitHub] tem a implementação completa no local.

   Também utilizamos a **vincular** atributo no parâmetro do método para ajudar a proteger contra ataques de publicação excessiva. Para obter mais detalhes, consulte [operações CRUD básicas no ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

### <a name="add-views"></a>Adicionar vistas

Em seguida, vamos criar as seguintes três vistas: 

* [Adicionar uma vista de item de lista](#AddItemIndexView).
* [Adicionar uma nova vista de item](#AddNewIndexView).
* [Adicionar uma vista de item de edição](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Adicionar uma vista de item de lista

1. Na **Explorador de soluções**, expanda o **vistas** pasta, vazio com o botão direito **Item** pasta que o Visual Studio criou por si quando adicionou o  **ItemController** anteriormente, clique em **Add**e, em seguida, clique em **vista**.
   
   ![Captura de tela de Gerenciador de Soluções mostrando a pasta de item que o Visual Studio criou com os comandos Add View realçados](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Na **Adicionar vista** diálogo caixa, atualize os valores seguintes:
   
   * Na caixa **Nome da vista**, escreva ***Índice***.
   * Na caixa **Modelo**, selecione ***Lista***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
     
   ![Captura de tela mostrando a caixa de diálogo Adicionar exibição](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Depois de adicionar estes valores, selecione **adicionar** e permita que o Visual Studio, criar uma nova vista de modelo. Quando tiver terminado, a mesma abre o ficheiro cshtml que é criado. Pode fechar esse ficheiro no Visual Studio, como irá voltar a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma nova vista de item

Semelhante à forma como criou uma vista para itens de lista, criar uma nova vista para criar itens com os seguintes passos:

1. Do **Explorador de soluções**, com o botão direito a **Item** pasta novamente, selecione **Add**e, em seguida, selecione **vista**.

1. Na **Adicionar vista** diálogo caixa, atualize os valores seguintes:
   
   * Na caixa **Nome da vista**, escreva ***Criar***.
   * Na caixa **Modelo**, selecione ***Criar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.
   
#### <a name="AddEditIndexView"></a>Adicionar uma vista de item de edição

E, finalmente, adicione uma vista para editar um item com os seguintes passos:

1. Do **Explorador de soluções**, com o botão direito a **Item** pasta novamente, selecione **Add**e, em seguida, selecione **vista**.

1. Na caixa de diálogo **Adicionar Vista**, faça o seguinte:
   
   * Na caixa **Nome da vista**, escreva ***Editar***.
   * Na caixa **Modelo**, selecione ***Editar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.

Em seguida, feche todos os documentos cshtml no Visual Studio como voltar a estas vistas mais tarde.

## <a name="connect-to-cosmosdb"></a>Etapa 5: Ligar ao Azure Cosmos DB 

Agora que as coisas básicas do MVC é resolvida, vamos adicionar o código para ligar ao Azure Cosmos DB e realizar operações CRUD. 

### <a name="perform-crud-operations"></a> Execute as operações CRUD nos dados

A primeira coisa a fazer aqui é adicionar uma classe que contém a lógica para ligar e utilizar o Azure Cosmos DB. Para este tutorial, encapsularemos essa lógica em uma classe chamada `CosmosDBService` e uma interface chamada. `ICosmosDBService` Esse serviço executa as operações CRUD e de feed de leitura, como listar itens incompletos, criar, editar e excluir os itens. 

1. Partir **Explorador de soluções**, crie uma nova pasta no seu projeto com o nome **serviços**.

1. Com o botão direito a **serviços** pasta, selecione **Add**e, em seguida, selecione **classe**. Nomeie a nova classe **CosmosDBService** e selecione **Adicionar**.

1. Adicione o código a seguir à classe **CosmosDBService** e substitua o código nesse arquivo pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Repita as etapas de 2-3, mas desta vez, para uma classe chamada **ICosmosDBService**e adicione o seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. O código anterior recebe uma `CosmosClient` como parte do construtor. A seguir ASP.NET Core pipeline, precisamos ir para o **Startup.cs** do projeto e inicializar o cliente com base na configuração como uma instância singleton a ser injetada por meio da [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). No manipulador **configureservices** , definimos:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. Dentro do mesmo arquivo, definimos nosso método auxiliar **InitializeCosmosClientInstanceAsync**, que lerá a configuração e inicializará o cliente.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. A configuração é definida no arquivo appSettings **. JSON** do projeto. Abra-o e adicione uma seção chamada **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Agora, se você executar o aplicativo, o pipeline do ASP.NET Core instanciará **CosmosDbService** e manterá uma única instância como singleton; Quando o **controlador de domínio** é usado para processar solicitações do lado do cliente, ele receberá essa única instância e poderá usá-la para executar operações CRUD.

Se você criar e executar este projeto agora, você verá algo parecido com este:

![Captura de tela do aplicativo Web de lista de tarefas criadas por este tutorial de banco de dados](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Etapa 6: Executar a aplicação localmente

Para testar a aplicação no seu computador local, utilize os seguintes passos:

1. Pressione F5 no Visual Studio para criar a aplicação no modo de depuração. Este deve compilar a aplicação e iniciar um browser com a página de grelha vazia que vimos anteriormente:
   
   ![Captura de tela do aplicativo Web da lista de tarefas criada por este tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Clique na ligação **Criar Novo** e adicione valores aos campos **Nome** e **Descrição**. Deixe o **concluído** caixa de verificação desmarcada caso contrário, o novo item é adicionado um estado concluído e não aparecerá na lista inicial.
   
3. Clique em **Create** e será redirecionado novamente para o **índice** vista e seu item aparece na lista. Pode adicionar alguns itens à sua lista de tarefas.

    ![Captura de tela da exibição de índice](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Clique em **Editar** junto a um **Item** na lista para ser direcionado para a vista **Editar**, onde pode atualizar qualquer propriedade do seu objeto, incluindo o sinalizador **Concluído**. Se você marcar o sinalizador **completo** e clicar em **salvar**, o **Item** será exibido como concluído na lista.
   
   ![Captura de tela da exibição de índice com a caixa concluída marcada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Você pode verificar a qualquer momento o estado dos dados no serviço de Azure Cosmos DB usando o [Cosmos Explorer](https://cosmos.azure.com) ou o data Explorer do emulador de Azure Cosmos DB.

6. Assim que já tiver testado a aplicação, prima Ctrl+F5 para parar a depuração da aplicação. Está pronto para implementar!

## <a name="deploy-the-application-to-azure"></a>Etapa 7: Implementar a aplicação 
Agora que a sua aplicação completa funciona corretamente no Azure Cosmos DB, iremos implementar esta aplicação Web no Serviço de Aplicações do Azure.  

1. Para publicar esta aplicação, com o botão direito no projeto no **Explorador de soluções** e selecione **Publish**.
   
2. Na caixa de diálogo **publicar** , selecione **serviço de aplicativo**, selecione **criar novo** para criar um perfil de serviço de aplicativo ou escolha **selecionar existente** para usar um perfil existente.

3. Se tiver um perfil de serviço de aplicações do Azure existente, selecione um **subscrição** na lista pendente. Utilize o **vista** filtro para ordenar por grupo de recursos ou tipo de recurso. Em seguida, procure o serviço de aplicações do Azure necessários e selecione **OK**.
   
   ![Caixa de diálogo Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Para criar um novo perfil do Serviço de Aplicações do Azure, clique em **Criar Novo** na caixa de diálogo **Publicar**. Na **criar serviço de aplicações** caixa de diálogo, introduza o nome da sua aplicação Web e a subscrição adequada, o grupo de recursos e o plano do serviço de aplicações, em seguida, selecione **criar**.

   ![Caixa de diálogo Criar Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Em alguns segundos, o Visual Studio publica seu aplicativo Web e inicia um navegador onde você pode ver seu projeto em execução no Azure!

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, você aprendeu a criar um aplicativo Web ASP.NET Core MVC que pode acessar dados armazenados no Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

* [Saiba mais sobre como particionar seus dados no Azure Cosmos DB](./partitioning-overview.md)
* [Saiba mais sobre como fazer consultas mais avançadas no Azure Cosmos DB](./how-to-sql-query.md)
* [Saiba mais sobre como modelar seus dados em um cenário mais avançado](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
