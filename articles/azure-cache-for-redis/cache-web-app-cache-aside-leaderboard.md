---
title: 'Tutorial: Criar uma Web App (cache-aside) - Cache Azure para Redis'
description: Saiba como criar uma Web App com Cache Azure para Redis que usa o padrão de cache-aside.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 71f1e2b50daf333e19bc11bce119f37cec28d146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209196"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Tutorial: Criar uma classificação cache-aside no ASP.NET

Neste tutorial irá atualizar a aplicação Web *ContosoTeamStats* ASP.NET, criada no [ASP.NET quickstart para Azure Cache for Redis,](cache-web-app-howto.md)para incluir um leaderboard que usa o [padrão de cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) com Azure Cache para Redis. A aplicação da amostra apresenta uma lista de estatísticas de equipas a partir de uma base de dados e demonstra diferentes formas de usar a Cache Azure para redis para armazenar e recolher dados da cache para melhorar o desempenho. Quando completa o tutorial tem uma aplicação web em execução que lê e escreve para uma base de dados, otimizada com Azure Cache para Redis, e hospedada no Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Melhorar a produção de dados e reduzir a carga da base de dados armazenando e recuperando dados usando a Cache Azure para Redis.
> * Utilizar um conjunto ordenado de Redis para obter as cinco melhores equipas.
> * Aprovisionar os recursos do Azure para a aplicação com um modelo do Resource Manager.
> * Publicar a aplicação no Azure com o Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter os seguintes pré-requisitos:

* Este tutorial continua onde você para fora em [ASP.NET quickstart para Azure Cache para Redis](cache-web-app-howto.md). Se ainda não o fez, siga este início rápido primeiro.
* Instalar [o Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    * Desenvolvimento ASP.NET e Web
    * Desenvolvimento do Azure
    * Desenvolvimento do ambiente de trabalho em .NET, com o SQL Server Express LocalDB ou o [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express).

## <a name="add-a-leaderboard-to-the-project"></a>Adicione uma classificação ao projeto

Nesta secção do tutorial, vai configurar o projeto *ContosoTeamStats* com uma classificação que comunica as estatísticas das vitorias, derrotas e empates de uma lista de equipas fictícias.

### <a name="add-the-entity-framework-to-the-project"></a>Adicionar o Entity Framework ao projeto

1. No Visual Studio, abra a solução *ContosoTeamStats* que criou no [ASP.NET quickstart para Azure Cache para Redis.](cache-web-app-howto.md)
2. Clique em **Ferramentas > Gestor de Pacotes NuGet >Consola do Gestor de Pacotes**.
3. Emita o comando seguinte na janela da **Consola do Gestor de Pacotes** para instalar o EntityFramework:

    ```powershell
    Install-Package EntityFramework
    ```

Para mais informações sobre este pacote, consulte a página NuGet [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Adicionar o Modelo de equipa

1. Clique com o botão direito do rato em **Modelos** no **Explorador de Soluções** e escolha **Adicionar**, **Classe**.

1. Introduza `Team` para o nome de classe e clique em **Adicionar**.

    ![Adicionar a classe de modelo](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Substitua as instruções `using` na parte superior do ficheiro *Team.cs* pelas seguintes instruções `using`:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Substitua a definição da classe `Team` pelo seguinte fragmento de código que contém uma definição da classe `Team` atualizada, bem como outras classes do programa auxiliar do Entity Framework. Neste tutorial, está a utilizar a primeira abordagem de código com o Entity Framework. Esta abordagem permite ao Entity Framework criar a base de dados a partir do código. Para obter mais informações sobre a abordagem Code First para o Entity Framework utilizado neste tutorial, veja [Code first to a new database (Code First para criar uma nova base de dados)](/ef/ef6/modeling/code-first/workflows/new-database).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. No **Explorador de Soluções**, faça duplo clique em **Web.config** para abri-lo.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Adicione a seguinte secção `connectionStrings` dentro da secção `configuration`. O nome da cadeia de ligação tem de corresponder ao nome da classe de contexto da base de dados do Entity Framework, que é `TeamContext`.

    Esta cadeia de ligação pressupõe que já cumpriu os [Pré-requisitos](#prerequisites) e instalou o SQL Server Express LocalDB, que faz parte da carga de trabalho de desenvolvimento do ambiente de *trabalho .NET* instalada com o Visual Studio 2019.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    O exemplo seguinte mostra a nova secção `connectionStrings` depois de `configSections` dentro da secção `configuration`:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Adicionar o TeamsController e as vistas

1. No Visual Studio, crie o projeto. 

1. No **Explorador de Soluções**, clique com o botão direito do rato na pasta **Controladores** e escolha **Adicionar**, **Controlador**.

1. Escolha **Controlador MVC 5 com vistas através do Entity Framework** e clique em **Adicionar**. Se for apresentado um erro depois de clicar em **Adicionar**, confirme que criou primeiro o projeto.

    ![Adicionar a classe de controlador](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Selecione **Team (ContosoTeamStats.Models)** na lista pendente **Classe de modelo**. Selecione **TeamContext (ContosoTeamStats.Models)** na lista pendente **Classe de contexto de dados**. Introduza `TeamsController` na caixa de texto **Nome do controlador** (se não for automaticamente povoado). Clique em **Adicionar** para criar a classe de controlador e adicionar vistas predefinidas.

    ![Configurar o controlador](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. No **Explorador de Soluções**, expanda **Global.asax** e faça duplo clique em **Global.asax.cs** para abri-lo.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Adicione as duas instruções `using` seguintes na parte superior do ficheiro, sob as outras declarações `using`:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Adicione a seguinte linha de código no final do método `Application_Start`:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. No **Explorador de Soluções**, expanda `App_Start` e faça duplo clique em `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. No método `RegisterRoutes`, substitua `controller = "Home"` na rota `Default` por `controller = "Teams"`, conforme mostrado no seguinte exemplo:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Configurar a vista do Esquema

1. No **Explorador de Soluções**, expanda a pasta **Vistas**, em seguida, a pasta **Partilhado** e faça duplo clique em **_Layout.cshtml**. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Altere o conteúdo do elemento `title` e substitua `My ASP.NET Application` por `Contoso Team Stats`, conforme mostrado no exemplo seguinte:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. Na `body` secção, adicione a seguinte nova `Html.ActionLink` declaração para *contoso Team Stats* logo abaixo do link para *Azure Cache para Redis Test*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Alterações do código](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Prima **Ctrl+F5** para criar e executar a aplicação. Esta versão da aplicação lê os resultados diretamente na base de dados. Tenha em atenção que as ações **Criar Nova**, **Editar**, **Detalhes** e **Eliminar** foram automaticamente adicionadas à aplicação pela estrutura do **Controlador 5 MVC com vistas através do Entity Framework**. Na secção seguinte do tutorial, irá adicionar Azure Cache para o Redis para otimizar o acesso aos dados e fornecer funcionalidades adicionais à aplicação.

    ![Aplicação de arranque](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Configure a app para Azure Cache para Redis

Nesta secção do tutorial, configura a aplicação da amostra para armazenar e recuperar as estatísticas da equipa contoso de um Azure Cache para redis, utilizando o cliente [cache StackExchange.Redis.](https://github.com/StackExchange/StackExchange.Redis)

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Adicionar uma ligação de cache ao Controlador de Equipas

Já instalou o pacote da biblioteca de cliente *StackExchange.Redis* no início rápido. Também já configurou a definição da aplicação *CacheConnection* para ser utilizada localmente e com o Serviço de Aplicações publicado. Utilize esta mesma biblioteca de cliente e as informações de *CacheConnection* no *TeamsController*.

1. No **Explorador de Soluções**, expanda a pasta **Controladores** e faça duplo clique em **TeamsController.cs** para abri-la.

    ![Controlador de equipas](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Adicione as seguintes duas `using` declarações a **TeamsController.cs:**

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Adicione as duas propriedades seguintes à classe `TeamsController`:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Atualizar o TeamsController para ler a partir da cache ou da base de dados

Neste exemplo, as estatísticas da equipa podem ser obtidas a partir da base de dados ou da cache. As estatísticas da equipa são armazenadas na cache como uma `List<Team>` serializada e também como um conjunto ordenado que utiliza tipos de dados de Redis. Ao obter itens de um conjunto ordenado, poderá obter alguns, todos ou consultar determinados itens. Neste exemplo, vai consultar o conjunto ordenado para obter as cinco melhores equipas classificadas por número de vitórias.

Não é necessário armazenar as estatísticas da equipa em vários formatos na cache para utilizar o Azure Cache para Redis. Este tutorial utiliza vários formatos para demonstrar as diferentes formas e os diferentes tipos de dados que pode utilizar para colocar dados em cache.

1. Adicione as seguintes instruções `using` na parte superior do ficheiro `TeamsController.cs`, juntamente com as outras instruções `using`:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Substitua a atual implementação de método `public ActionResult Index()` pela seguinte implementação:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Adicione os três métodos seguintes à classe `TeamsController` para implementar os tipos de ação `playGames`, `clearCache` e `rebuildDB` a partir da instrução switch adicionada ao fragmento de código anterior.

    O método `PlayGames` atualiza as estatísticas da equipa simulando uma época de jogos, guarda os resultados na base de dados e limpa os dados agora desatualizados da cache.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    O método `RebuildDB` reinicializa a base de dados com o conjunto de equipas predefinido, gera as estatísticas e limpa os dados agora desatualizados da cache.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    O método `ClearCachedTeams` remove quaisquer estatísticas de equipa em cache da cache.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Adicione os quatro métodos seguintes à classe `TeamsController` para implementar as várias formas de obter as estatísticas da equipa a partir da cache e da base de dados. Cada um destes métodos devolve uma `List<Team>`, que é depois apresentada pela vista.

    O método `GetFromDB` lê as estatísticas da equipa a partir da base de dados.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    O método `GetFromList` lê as estatísticas da equipa a partir da cache, como uma `List<Team>` serializada. Se as estatísticas não estiverem presentes na cache, ocorrerá uma falha de acerto na cache. Se existir uma falha de acerto na cache, as estatísticas da equipa serão lidas a partir da base de dados e, em seguida, armazenadas para o próximo pedido. Neste exemplo, a serialização JSON.NET é utilizada para serializar os objetos .NET de/para a cache. Para obter mais informações, consulte [Como trabalhar com objetos .NET em Cache Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    O método `GetFromSortedSet` lê as estatísticas da equipa a partir de um conjunto ordenado em cache. Se existir uma falha de acerto na cache, as estatísticas da equipa são lidos a partir da base de dados e armazenadas na cache como um conjunto ordenado.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    O método `GetFromSortedSetTop5` lê as cinco melhores equipas a partir do conjunto ordenado em cache. Começa por verificar se a chave `teamsSortedSet` existe na cache. Se esta chave não estiver presente, o método `GetFromSortedSet` é chamado para ler as estatísticas da equipa e para armazená-las na cache. Em seguida, o conjunto ordenado em cache é consultado relativamente às cinco melhores equipas que são devolvidas.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Atualizar os métodos Criar, Editar e Eliminar para trabalhar com a cache

O código da estrtura gerado como parte deste exemplo inclui métodos para adicionar, editar e eliminar equipas. Sempre que uma equipa é adicionada, editada ou removida, os dados na cache ficam desatualizados. Nesta secção, vai modificar estes três métodos para limpar as equipas em cache para que a cache seja atualizada.

1. Navegue para o método `Create(Team team)`, na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no seguinte exemplo:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Navegue para o método `Edit(Team team)`, na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no seguinte exemplo:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Navegue para o método `DeleteConfirmed(int id)`, na classe `TeamsController`. Adicione uma chamada ao método `ClearCachedTeams`, conforme mostrado no seguinte exemplo:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Adicionar métodos de colocação em cache para a vista Índice de Equipas

1. No **Explorador de Soluções**, expanda a pasta **Vistas**, em seguida, a pasta **Equipas** e faça duplo clique em **Index.cshtml**.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. Perto da parte superior do ficheiro, procure o seguinte elemento de parágrafo:

    ![Tabela de ações](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Esta ligação cria uma nova equipa. Substitua o elemento de parágrafo pela seguinte tabela. Esta tabela tem ligações de ação para criar uma nova equipa, reproduzir uma nova época de jogos, limpar a cache, obter as equipas a partir da cache em vários formatos, obter as equipas a partir da base de dados e reconstruir a base de dados com novos dados de exemplo.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Percorra a parte inferior do ficheiro **Index.cshtml** e adicione o seguinte `tr` elemento de modo a que seja a última linha da última tabela do ficheiro:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Esta linha apresenta o valor de `ViewBag.Msg`, que contém um relatório de estado sobre a operação atual. O `ViewBag.Msg` é definido quando clicar em qualquer uma das ligações de ação do passo anterior.

    ![Mensagem de estado](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Prima **F6** para criar o projeto.

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente no computador para verificar a funcionalidade que foi adicionada para suportar as equipas.

Neste teste, tanto a aplicação como a base de dados estão a ser executadas localmente. No entanto, o Azure Cache para Redis é hospedado remotamente em Azure. Por conseguinte, a cache pode provavelmente apresentar um desempenho ligeiramente inferior da base de dados. Para melhor desempenho, a aplicação do cliente e a cache Azure para a instância Redis devem estar no mesmo local. Na secção seguinte, vai implementar todos os recursos no Azure para ver o desempenho melhorado resultante da utilização de uma cache.

Para executar a aplicação localmente:

1. Prima **Ctrl+F5** para executar a aplicação.

    ![Aplicação em execução localmente](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Teste cada um dos novos métodos adicionados à vista. Uma vez que a cache nestes testes é remota, a base de dados deve superar ligeiramente o desempenho da cache.

## <a name="publish-and-run-in-azure"></a>Publicar e executar no Azure

### <a name="provision-a-database-for-the-app"></a>Provisionamento de uma base de dados para a aplicação

Nesta secção, irá providenciar uma nova base de dados na Base de Dados SQL para a aplicação a utilizar enquanto estiver hospedada no Azure.

1. No [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso**no canto superior esquerdo do portal do Azure.

1. Na página **Novo**, clique em **Bases de dados** > **base de dados SQL**.

1. Utilize as seguintes definições para a nova Base de Dados SQL:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome da base de dados** | *ContosoTeamsDatabase* | Para nomes de bases de dados válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). |
   | **Subscrição** | *A sua subscrição*  | Selecione a mesma subscrição que utilizou para criar a cache e alojar o Serviço de Aplicações. |
   | **Grupo de recursos**  | *TestResourceGroup* | Clique em **Utilizar existente** e utilize o mesmo grupo de recursos onde colocou a cache e o Serviço de Aplicações. |
   | **Selecionar origem** | **Base de dados vazia** | Começar com uma base de dados vazia. |

1. Em **Servidor**, clique em **Configurar definições necessárias** > **Criar um novo servidor**, forneça as seguintes informações e clique no botão **Selecionar**:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
   | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). |
   | **Palavra-passe** | Qualquer palavra-passe válida | A sua palavra-passe deve ter, pelo menos, oito carateres e deve conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
   | **Localização** | *E.U.A. Leste* | Selecione a mesma região onde criou a cache e o Serviço de Aplicações. |

1. Clique em **Afixar ao dashboard** e em **Criar** para criar a nova base de dados e o servidor.

1. Uma vez criada a nova base de dados, clique em **Mostrar cadeias de ligação da base de dados** e copie a cadeia de ligação **ADO.NET**.

    ![Mostrar cadeias de ligação](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. No portal do Azure, navegue até ao Serviço de Aplicações, clique em **Definições da Aplicação** e, em seguida, clique em **Adicionar nova cadeia de ligação** na secção Cadeia de ligação.

1. Adicione uma nova cadeia de ligação com o nome *TeamContext* para corresponder à classe de contexto da base de dados do Entity Framework. Cole a cadeia de ligação para a nova base de dados como o valor. Confirme que substitui os seguintes marcadores na cadeia de ligação e clique em **Guardar**:

    | Marcador de posição | Valor sugerido |
    | --- | --- |
    | *{o_seu_nome_de_utilizador}* | Utilize o **login de administração** do servidor para o servidor que acabou de criar. |
    | *{a_sua_palavra-passe}* | Use a palavra-passe para o servidor que acabou de criar. |

    Ao adicionar o nome de utilizador e a palavra-passe como uma Definição da Aplicação, o nome de utilizador e a palavra-passe não são incluídos no código. Esta abordagem ajuda a proteger essas credenciais.

### <a name="publish-the-application-updates-to-azure"></a>Publicar as atualizações da aplicação no Azure

Neste passo do tutorial, vai publicar as atualizações da aplicação no Azure e executá-las na cloud.

1. Clique com o botão direito do rato no projeto **ContosoTeamStats** do Visual Studio e escolha **Publicar**.

    ![Publicar](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Clique em **Publicar** para utilizar o mesmo perfil de publicação que criou no início rápido.

3. Após a conclusão da publicação, o Visual Studio inicia a aplicação no browser predefinido.

    ![Cache adicionada](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    A tabela seguinte descreve cada ligação de ação da aplicação de exemplo:

    | Ação | Descrição |
    | --- | --- |
    | Criar Novo |Crie uma nova Equipa. |
    | Reproduzir Época |Reproduza uma época de jogos, atualize as estatísticas da equipa e limpe quaisquer dados da equipa desatualizados da cache. |
    | Limpar Cache |Limpe as estatísticas da equipa na cache. |
    | Lista na Cache |Obtenha as estatísticas da equipa a partir da cache. Se existir uma falha de acerto na cache, carregue as estatísticas da base de dados e guarde-as na cache para a próxima utilização. |
    | Conjunto Ordenado na Cache |Obtenha as estatísticas da equipa a partir da cache com um conjunto ordenado. Se existir uma falha de acerto na cache, carregue as estatísticas da base de dados e guarde-as na cache utilizando um conjunto ordenado. |
    | Cinco Melhores Equipas na Cache |Obtenha as cinco melhores equipas a partir da cache com um conjunto ordenado. Se existir uma falha de acerto na cache, carregue as estatísticas da base de dados e guarde-as na cache utilizando um conjunto ordenado. |
    | Carregar da Base de Dados |Obtenha as estatísticas da equipa a partir da base de dados. |
    | Recriar Base de Dados |Recrie a base de dados e volte a carregá-la com os dados da equipa de exemplo. |
    | Editar/Detalhes/Eliminar |Edite uma equipa, veja os detalhes de uma equipa, elimine uma equipa. |

Clique em algumas das ações e experimente obter os dados a partir de diferentes fontes. Repare nas diferenças existentes no tempo de obtenção dos dados a partir da base de dados e da cache.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando tiver terminado o tutorial da aplicação de exemplo, poderá eliminar os recursos do Azure utilizados para conservar os custos e os recursos. Todos os recursos devem estar presentes no mesmo grupo de recursos para poder eliminá-los numa única operação ao eliminar o grupo de recursos. As instruções neste tópico utilizaram um grupo de recursos denominado *TestResources*.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis.
>

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.
2. Escreva o nome do seu grupo de recursos na caixa de texto **Filtrar itens…**
3. Clique em **...**, à direita do grupo de recursos, e em **Eliminar grupo de recursos**.

    ![Eliminar](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

    Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como escalar cache Azure para Redis](./cache-how-to-scale.md)
