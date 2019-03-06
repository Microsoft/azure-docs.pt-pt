---
title: 'Tutorial de ASP.NET MVC para o Azure Cosmos DB: Desenvolvimento de aplicativos Web'
description: Tutorial de ASP.NET MVC para criar uma aplicação Web de MVC com o Azure Cosmos DB. Irá armazenar dados de acesso e JSON a partir de uma aplicação de tarefas alojada no em Web Sites do Azure – tutorial ASP NET MVC passo a passo.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 778c5c50e2742dd7436f809be06c625254973b49
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440850"
---
# <a name="_Toc395809351"></a>Tutorial do ASP.NET MVC: Desenvolvimento de aplicativos Web com o Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Para realçar como pode de forma eficiente tirar partido do Azure Cosmos DB para armazenar e consultar documentos JSON, este artigo serve de orientação ponto a ponto e mostra-lhe como criar uma aplicação de tarefas através do Azure Cosmos DB. As tarefas serão armazenadas como documentos JSON no Azure Cosmos DB.

![Captura de ecrã da todo list criada por este tutorial – tutorial ASP NET MVC passo a passo de aplicativo web do MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Esta orientação mostra-lhe como utilizar o serviço do Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação Web ASP.NET MVC alojada no Azure. Se estiver à procura de um tutorial que se concentra apenas no Azure Cosmos DB e não nos componentes do ASP.NET MVC, veja [Build an Azure Cosmos DB C# console application](sql-api-get-started.md)(Criar uma aplicação de consola C# do Azure Cosmos DB).

> [!TIP]
> Este tutorial parte do pressuposto de que tem alguma experiência anterior na utilização do ASP.NET MVC e de Web Sites Azure. Se não estiver familiarizado com o ASP.NET ou com as [ferramentas dos pré-requisitos](#_Toc395637760), recomendamos que transfira todo o projeto de exemplo a partir do [GitHub][GitHub] e que siga as instruções neste exemplo. Assim que o tiver criado, pode rever este artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.
> 
> 

## <a name="_Toc395637760"></a>Pré-requisitos para este tutorial sobre bases de dados
Antes de seguir as instruções deste artigo, deve certificar-se de que tem o seguinte:

* Uma conta ativa do Azure.  Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* SDK do Microsoft Azure para .NET para Visual Studio 2017, disponível através do Instalador do Studio Visual.

Todas as capturas de ecrã neste artigo foram executadas com o Microsoft Visual Studio Community 2017. Se o sistema estiver configurado com uma versão diferente, é possível que as suas opções e ecrãs não coincidam na totalidade, mas se cumpre os pré-requisitos acima, esta solução deverá funcionar.

## <a name="_Toc395637761"></a>Passo 1: Criar uma conta de base de dados do Azure Cosmos DB
Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta SQL para o Azure Cosmos DB ou se estiver a utilizar o Emulador do Azure Cosmos DB para este tutorial, pode avançar para [Criar uma aplicação ASP.NET MVC nova](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Agora vamos orientá-lo na criação de uma nova aplicação ASP.NET MVC a partir do zero. 

## <a name="_Toc395637762"></a>Passo 2: Criar uma nova aplicação ASP.NET MVC

1. No Visual Studio, no menu **Ficheiro**, aponte para **Novo** e, em seguida, clique em **Projeto**. Aparece a caixa de diálogo **Novo Projeto**.

2. No painel **Tipos de projetos**, expanda **Modelos**, **Visual C#**, **Web** e, em seguida, selecione **Aplicação Web ASP.NET**.

      ![Captura de ecrã da caixa de diálogo novo projeto com o tipo de projeto de aplicação Web ASP.NET realçado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Na caixa **Nome**, escreva o nome do projeto. Este tutorial utiliza o nome “todo” (tarefas). Se optar por utilizar algo que não isto, sempre que este tutorial aborda o espaço de nomes de tarefas, terá de ajustar os exemplos de código fornecidos para utilizar o nome que deu à aplicação. 
4. Clique em **Procurar** para navegar para a pasta onde pretende criar o projeto e, em seguida, clique em **OK**.
   
      A caixa de diálogo **Nova aplicação Web do ASP.NET** é apresentada.
   
    ![Captura de ecrã da caixa de diálogo do novo aplicativo Web ASP.NET com o modelo de aplicação MVC realçado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. No painel de modelos, selecione **MVC**.

6. Clique em **OK** e permita que o Visual Studio estruture o modelo em branco do ASP.NET MVC. 

          
7. Assim que o Visual Studio tenha terminado de criar a aplicação MVC automática, fica com uma aplicação ASP.NET vazia que pode executar localmente.
   
    Vamos ignorar a execução local do projeto, pois tenho a certeza de que já todos vimos a aplicação “Olá, Mundo” do ASP.NET. Passemos diretamente à adição do Azure Cosmos DB a este projeto e à criação da nossa aplicação.

## <a name="_Toc395637767"></a>Passo 3: Adicionar o Azure Cosmos DB ao seu projeto de aplicação web MVC
Agora que temos a maior parte da estrutura do ASP.NET MVC necessária para esta solução, vamos abordar o verdadeiro objetivo deste tutorial: adicionar o Azure Cosmos DB à nossa aplicação Web MVC.

1. O SDK .NET do Azure Cosmos DB é compactado e distribuído como um pacote NuGet. Para colocar o pacote NuGet no Visual Studio, utilize o gestor de pacotes NuGet no Visual Studio ao clicar no projeto no **Explorador de Soluções** e, em seguida, clicar em **Gerir Pacotes NuGet**.
   
    ![Captura de ecrã das opções de contexto para o projeto de aplicativo web no Explorador de soluções, com gerir pacotes NuGet realçado.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    A caixa de diálogo **Gerir Pacotes NuGet** aparece.
2. Na caixa **Procurar** do NuGet, escreva ***Azure DocumentDB***. (O nome do pacote não foi atualizado para o Azure Cosmos DB.)
   
    Nos resultados, instale o pacote **Microsoft.Azure.DocumentDB da Microsoft**. É transferido e instalado o pacote do Azure Cosmos DB, bem como todas as dependências, como Newtonsoft.Json. Clique em **OK** na janela **Pré-visualizar** e em **Aceito** na janela **Aceitação de Licença** para concluir a instalação.
   
    ![Sreenshot da janela gerir pacotes NuGet, com o Azure Cosmos DB biblioteca cliente Microsoft realçado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      Em alternativa, pode utilizar a Consola do Gestor de Pacotes para instalar o pacote. Para tal, clique no menu **Ferramentas**, clique em **Gestor do Pacote NuGet** e, em seguida, clique em **Consola do Gestor de Pacotes**. Na linha de comandos, escreva o seguinte.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. Depois de o pacote estar instalado, a sua solução Visual Studio deve assemelhar-se aos seguinte com duas novas referências adicionadas, Microsoft.Azure.Documents.Client e Newtonsoft.Json.
   
    ![Captura de ecrã das duas referências adicionadas ao projeto de dados JSON no Explorador de soluções](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Passo 4: Configurar a aplicação ASP.NET MVC
Agora vamos adicionar os modelos, as vistas e os controladores a esta aplicação MVC:

* [Adicionar um modelo](#_Toc395637764).
* [Adicionar um controlador](#_Toc395637765).
* [Adicionar vistas](#_Toc395637766).

### <a name="_Toc395637764"></a>Adicionar um modelo de dados JSON
Vamos começar por criar o **M** no MVC, o modelo. 

1. No **Explorador de Soluções**, clique com o botão direito do rato na pasta **Modelos**, clique em **Adicionar** e, em seguida, em **Classe**.
   
      A caixa de diálogo **Adicionar Novo Item** é apresentada.
2. Atribua o nome **Item.cs** à sua nova classe e clique em **Adicionar**. 
3. Neste novo ficheiro **Item.cs**, adicione o seguinte após a última *instrução using*.
   
        using Newtonsoft.Json;
4. Agora substitua este código 
   
        public class Item
        {
        }
   
    pelo seguinte código.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Todos os dados no Azure Cosmos DB são transmitidos e armazenados como JSON. Para controlar a forma como os objetos são serializados/como lhes é anulada a serialização pelo JSON.NET, pode utilizar o atributo **JsonProperty** como é demonstrado na classe **Item** que acabamos de criar. Não **tem** de fazer isto, mas quero garantir que as minhas propriedades seguem as convenções de nomenclatura de camelCase JSON. 
   
    Não só pode controlar o formato do nome da propriedade quando passa para JSON, mas também pode mudar completamente o nome das suas propriedades .NET, tal como fiz na propriedade **Descrição**. 

### <a name="_Toc395637765"></a>Adicionar um controlador
O **M** está resolvido. Agora vamos criar o **C** no MVC, uma classe de controlador.

1. No **Explorador de Soluções**, clique com o botão direito do rato na pasta **Controladores**, clique em **Adicionar** e, em seguida, em **Controlador**.
   
    A caixa de diálogo **Adicionar Estrutura** é apresentada.
2. Selecione **Controlador MVC 5 – Vazio** e, em seguida, clique em **Adicionar**.
   
    ![Captura de ecrã da caixa de diálogo Adicionar estrutura com controlador MVC 5 – opção vazia realçada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Atribua o nome **ItemController** ao novo Controlador.
   
    ![Captura de ecrã da caixa de diálogo Adicionar controlador](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    Depois de o ficheiro estar criado, a sua solução do Visual Studio deve assemelhar-se ao seguinte com o novo ficheiro ItemController.cs no **Explorador de Soluções**. Também é apresentado o novo ficheiro Item.cs criado anteriormente.
   
    ![Captura de ecrã da solução do Visual Studio – Explorador de soluções com o novo ficheiro ItemController.cs e o ficheiro Item.cs realçado](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Pode fechar o ItemController.cs, voltaremos a ele mais tarde. 

### <a name="_Toc395637766"></a>Adicionar vistas
Agora, vamos criar o **V** no MVC, as vistas:

* [Adicionar uma vista de Índice de Itens](#AddItemIndexView).
* [Adicionar uma vista de Novo Item](#AddNewIndexView).
* [Adicionar uma vista de Editar Item](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Adicionar uma vista de Índice de Itens
1. No **Explorador de Soluções**, expanda a pasta **Vistas**, clique com o botão direito do rato na pasta **Item** vazia que o Visual Studio criou para si quando adicionou o **ItemController** anteriormente, clique em **Adicionar** e, em seguida, clique em **Vista**.
   
    ![Captura de ecrã do Explorador de soluções que mostra a pasta de itens que o Visual Studio criou com os comandos Adicionar vista realçados](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. Na caixa de diálogo **Adicionar Vista**, faça o seguinte:
   
   * Na caixa **Nome da vista**, escreva ***Índice***.
   * Na caixa **Modelo**, selecione ***Lista***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
     
   ![Captura de ecrã que mostra a caixa de diálogo Adicionar vista](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Depois de todos estes valores estarem definidos, clique em **Adicionar** e permita que o Visual Studio crie uma nova vista de modelo. Depois de estar concluído, abrirá o ficheiro cshtml que foi criado. Podemos fechar esse ficheiro no Visual Studio, dado que vamos voltar a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma vista de Novo Item
À semelhança da forma como criámos uma vista **Índice de Itens**, agora vamos criar uma nova vista para criar novos **Itens**.

1. No **Explorador de Soluções**, clique com o botão direito do rato novamente na pasta **Item**, clique em **Adicionar** e, em seguida, em **Vista**.
2. Na caixa de diálogo **Adicionar Vista**, faça o seguinte:
   
   * Na caixa **Nome da vista**, escreva ***Criar***.
   * Na caixa **Modelo**, selecione ***Criar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
   * Clique em **Adicionar**.
   
#### <a name="_Toc395888515"></a>Adicionar uma vista de Editar Item
Finalmente, adicione uma última vista para editar um **Item** da mesma forma que fez anteriormente.

1. No **Explorador de Soluções**, clique com o botão direito do rato novamente na pasta **Item**, clique em **Adicionar** e, em seguida, em **Vista**.
2. Na caixa de diálogo **Adicionar Vista**, faça o seguinte:
   
   * Na caixa **Nome da vista**, escreva ***Editar***.
   * Na caixa **Modelo**, selecione ***Editar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
   * Clique em **Adicionar**.

Assim que esta operação esteja concluída, feche todos os documentos cshtml no Visual Studio, pois podemos voltar a estas vistas mais tarde.

## <a name="_Toc395637769"></a>Passo 5: Preparar o Azure Cosmos DB
Agora que já tratámos das coisas básicas do MVC, vamos adicionar o código para o Azure Cosmos DB. 

Nesta secção, vamos adicionar código para tratar do seguinte:

* [Listar Itens incompletos](#_Toc395637770).
* [Adicionar Itens](#_Toc395637771).
* [Editar Itens](#_Toc395637772).

### <a name="_Toc395637770"></a>Listar Itens incompletos na aplicação Web MVC
A primeira coisa a fazer é adicionar uma classe que contém toda a lógica para ligar ao Azure Cosmos DB e utilizá-lo. Neste tutorial, vamos encapsular toda esta lógica numa classe de repositório com o nome DocumentDBRepository. 

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto, clique em **Adicionar** e, em seguida, em **Classe**. Atribua o nome **DocumentDBRepository** à nova classe e clique em **Adicionar**.
2. Na classe recém-criada **DocumentDBRepository** adicione as seguintes *instruções using* acima da declaração *espaço de nomes*
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net;
        
    Agora substitua este código 
   
        public class DocumentDBRepository
        {
        }
   
    pelo seguinte código.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Estamos a ler alguns valores da configuração, por isso, abra o ficheiro **Web.config** da sua aplicação e adicione as seguintes linhas na secção `<AppSettings>`.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Agora, atualize os valores de *ponto final* e *authKey* no painel Chaves do Portal do Azure. Utilize o **URI** a partir do painel Chaves como o valor da definição do ponto final e utilize a **CHAVE PRIMÁRIA** ou a **CHAVE SECUNDÁRIA** a partir do painel Chaves como o valor da definição authKey.

    A preparação do repositório do Azure Cosmos DB fica assim resolvida. Agora vamos adicionar a lógica da aplicação.

1. A primeira coisa que queremos conseguir fazer com uma aplicação de lista de tarefas é apresentar os itens incompletos.  Copie e cole o seguinte fragmento de código em qualquer lugar dentro da classe **DocumentDBRepository**.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Abra o **ItemController** que adicionamos anteriormente e adicione as seguintes *instruções using* acima da declaração de espaço de nomes.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Se o seu projeto não se chamar “todo” (tarefa), tem de atualizar com “todo.Models” de modo a refletir o nome do seu projeto.
   
    Agora substitua este código
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    pelo seguinte código.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Abra **Global.asax.cs** e adicione a seguinte linha ao método **Application_Start** 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

Neste momento, a sua solução deverá conseguir criar sem erros.

Se tiver executado a aplicação agora, poderá aceder ao **HomeController** e à vista **Índice** desse controlador. Este é o comportamento predefinido para o projeto de modelo MVC que escolhemos no início, mas não é isso que queremos! Vamos alterar o encaminhamento nesta aplicação MVC para alterar este comportamento.

Abra ***Aplicação\_Iniciar\RouteConfig.cs*** e localize a linha que começa por “defaults:” e altere-a de modo a assemelhar-se ao seguinte.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Esta alteração indica ao ASP.NET MVC que, se não tiver especificado um valor no URL para controlar o comportamento do encaminhamento, em vez de **Base**, deve ser utilizado **Item** como o controlador e **Índice** de utilizador como a vista.

Agora, se executar a aplicação, esta liga-se ao seu **ItemController** que irá ligar-se à classe de repositório e utilizará o método GetItems para devolver todos os itens incompletos para a vista **Vistas**\\**Item**\\**Índice**. 

Se criar e executar agora este projeto, deverá ver algo semelhante a isto.    

![Captura de ecrã da aplicação web da todo list criada por este tutorial de base de dados](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Adicionar Itens
Vamos colocar alguns itens na nossa base de dados para que tenhamos algo mais do que uma grelha.

Vamos adicionar algum código ao Azure Cosmos DBRepository e a ItemController para manter o registo no Azure Cosmos DB.

1. Adicione o método seguinte à sua classe **DocumentDBRepository**.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Este método simplesmente utiliza um objeto transmitido ao mesmo e mantém-no no Azure Cosmos DB.
2. Abra o ficheiro ItemController.cs e adicione o seguinte fragmento de código dentro da classe. É desta forma que o ASP.NET MVC sabe o que fazer relativamente à ação **Criar**. Neste caso, basta compor a vista Create.cshtml associada criada anteriormente.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Agora precisamos de mais algum código neste controlador para aceitar a submissão a partir da vista **Criar**.
3. Adicione o próximo bloco de código à classe ItemController.cs que indica ao ASP.NET MVC o que fazer com um POST de formulário para este controlador.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Este código liga-se ao DocumentDBRepository e utiliza o método CreateItemAsync para manter o novo item de tarefa na base de dados. 
   
    **Nota de segurança**: O **ValidateAntiForgeryToken** atributo é utilizado aqui para ajudar a proteger esta aplicação contra ataques de falsificação de solicitação entre sites. Não basta apenas adicionar este atributo, as suas vistas também têm de trabalhar com este token antifalsificação. Para obter mais informações sobre o assunto e exemplos sobre como implementar este atributo corretamente, veja [Preventing Cross-Site Request Forgery (Prevenir Falsificação de Pedidos Entre Sites)][Preventing Cross-Site Request Forgery]. O código de origem fornecido no [GitHub][GitHub] tem a implementação completa no local.
   
    **Nota de segurança**: Também utilizamos a **vincular** atributo no parâmetro do método para ajudar a proteger contra ataques de publicação excessiva. Para obter mais detalhes, veja [Basic CRUD Operations in ASP.NET MVC (Operações CRUD Básicas no ASP.NET MVC)][Basic CRUD Operations in ASP.NET MVC].

Assim concluímos o código necessário para adicionar novos Itens à nossa base de dados.

### <a name="_Toc395637772"></a>Editar Itens
É necessário fazer só mais uma coisa: adicionar a capacidade de editar **Itens** na base de dados e marcá-los como concluídos. A vista para edição já foi adicionada ao projeto, pelo que precisamos apenas de adicionar novamente algum código ao nosso controlador e à classe **DocumentDBRepository**.

1. Adicione o seguinte à sua classe **DocumentDBRepository**.
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    O primeiro destes métodos, **GetItem**, obtém um Item do Azure Cosmos DB que é reenviado para o **ItemController** e, em seguida, para a vista **Editar**.
   
    O segundo método que acabamos de adicionar substitui o **Documento** no Azure Cosmos DB com a versão do **Documento** passada a partir do **ItemController**.
2. Adicione o seguinte à classe **ItemController**.
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    O primeiro método processa o Http GET que ocorre quando o utilizador clica na ligação **Editar** a partir da vista **Índice**. Este método obtém um [**Documento**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) do Azure Cosmos DB e transmite-o para a vista **Editar**.
   
    A vista **Editar** efetua então um Http POST para o **IndexController**. 
   
    O segundo método que adicionamos processa a transmissão do objeto atualizado para o Azure Cosmos DB, de modo a manter-se na base de dados.

Já está. Isto é tudo o que é necessário para executar a nossa aplicação, indicar **Itens** incompletos numa lista, adicionar novos **Itens** e editar **Itens**.

## <a name="_Toc395637773"></a>Passo 6: Executar a aplicação localmente
Para testar a aplicação no seu computador local, faça o seguinte:

1. Prima F5 no Visual Studio para criar a aplicação no modo de depuração. Este deve compilar a aplicação e iniciar um browser com a página de grelha vazia que vimos anteriormente:
   
    ![Captura de ecrã da aplicação web da todo list criada por este tutorial de base de dados](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Clique na ligação **Criar Novo** e adicione valores aos campos **Nome** e **Descrição**. Deixe a caixa de verificação **Concluído** desmarcada, caso contrário, o novo **Item** será adicionado a um estado concluído e não aparece na lista inicial.
   
    ![Captura de ecrã da vista criar](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Clique em **Criar** para ser redirecionado para a vista **Índice** e para que o seu **Item** apareça na lista.
   
    ![Captura de ecrã da vista índice](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Pode adicionar mais alguns **Itens** à sua lista de tarefas.
    
4. Clique em **Editar** junto a um **Item** na lista para ser direcionado para a vista **Editar**, onde pode atualizar qualquer propriedade do seu objeto, incluindo o sinalizador **Concluído**. Se marcar o sinalizador **Concluído** e clicar em **Guardar**, o **Item** é removido da lista de tarefas incompletas.
   
    ![Captura de ecrã da vista índice com a caixa de concluído selecionada](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Assim que já tiver testado a aplicação, prima Ctrl+F5 para parar a depuração da aplicação. Está pronto para implementar!

## <a name="_Toc395637774"></a>Passo 7: Implementar a aplicação no App Service do Azure 
Agora que a sua aplicação completa funciona corretamente no Azure Cosmos DB, iremos implementar esta aplicação Web no Serviço de Aplicações do Azure.  

1. Para publicar esta aplicação tem apenas de clicar com o botão direito do rato no projeto no **Explorador de Soluções** e clicar em **Publicar**.
   
    ![Captura de ecrã da opção publicar no Explorador de soluções](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. Na caixa de diálogo **Publicar**, clique em **Serviço de Aplicações do Microsoft Azure** e, em seguida, selecione **Criar Novo** para criar um perfil do Serviço de Aplicações, ou clique em **Selecionar Existente** para utilizar um perfil existente.

    ![Caixa de diálogo Publicar no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Se tiver um perfil existente do Serviço de Aplicações do Azure, introduza o nome da sua subscrição. Utilize o filtro **Vista** para ordenar por grupo de recursos ou tipo de recurso e, em seguida, selecione o Serviço de Aplicações do Azure. 
   
    ![Caixa de diálogo Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Para criar um novo perfil do Serviço de Aplicações do Azure, clique em **Criar Novo** na caixa de diálogo **Publicar**. Na caixa de diálogo **Criar Serviço de Aplicações**, introduza o nome da Aplicação Web e a subscrição adequada, o grupo de recursos e o plano do Serviço de Aplicações e, em seguida, clique em **Criar**.

    ![Caixa de diálogo Criar Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Em alguns segundos, o Visual Studio irá concluir a publicação da sua aplicação Web e iniciar um browser, onde poderá ver o seu trabalho em execução no Azure!



## <a name="_Toc395637775"></a>Passos seguintes
Parabéns! Acabou de criar a sua primeira aplicação Web ASP.NET MVC com o Azure Cosmos DB e de a publicar no Azure. Pode transferir ou clonar a partir do [GitHub][GitHub] o código fonte da aplicação completa, incluindo as funcionalidades de detalhe e eliminação que não foram incluídas neste tutorial. Por isso, se estiver interessado em adicioná-la à sua aplicação, copie o código e adicione-o a esta aplicação.

Para adicionar mais funcionalidades à sua aplicação, reveja as APIs disponíveis na [Biblioteca .NET do Azure Cosmos DB](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) e esteja à vontade para contribuir para a Biblioteca .NET do Azure Cosmos DB no [GitHub][GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
