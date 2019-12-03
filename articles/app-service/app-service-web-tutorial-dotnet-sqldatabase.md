---
title: 'Tutorial: aplicativo ASP.NET com Banco de dados SQL'
description: Aprenda a implementar uma aplicação ASP.NET C# com uma base de dados do SQL Server no Azure.
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/25/2018
ms.custom: seodec18
ms.openlocfilehash: 2a0b3994af48372b8ed0a3c6eaecf4289d0980c1
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671384"
---
# <a name="tutorial-build-an-aspnet-app-in-azure-with-sql-database"></a>Tutorial: Criar uma aplicação ASP.NET no Azure com a Base de Dados SQL

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como implantar um aplicativo ASP.NET controlado por dados no serviço de aplicativo e conectá-lo ao [banco de dado SQL do Azure](../sql-database/sql-database-technical-overview.md). Quando tiver terminado, você terá um aplicativo ASP.NET em execução no Azure e conectado ao banco de dados SQL.

![Aplicativo ASP.NET publicado no serviço Azure App](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma Base de Dados SQL no Azure
> * Ligar uma aplicação ASP.NET à Base de Dados SQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de trabalho de **desenvolvimento de ASP.net e Web** .

Se já tiver instalado o Visual Studio, adicione as cargas de trabalho no Visual Studio clicando em **Ferramentas** > **Obter Ferramentas e Funcionalidades**.

## <a name="download-the-sample"></a>Transferir o exemplo

- [Transfira o projeto de exemplo](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).
- Extraia (deszipe) o ficheiro *dotnet-sqldb-tutorial-master.zip*.

O projeto de exemplo contém uma aplicação básica [ASP.NET MVC](https://www.asp.net/mvc) create-read-update-delete - criar-ler-atualizar-eliminar (CRUD) que utiliza o [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Executar a aplicação

Abra o ficheiro *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* no Visual Studio. 

Escreva `Ctrl+F5` para executar a aplicação sem a depurar. A aplicação é apresentada no browser predefinido. Selecione a ligação **Criar Novo** e criar alguns itens *a fazer*. 

![Caixa de diálogo Novo Projeto ASP.NET](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Teste as ligações **Editar**, **Detalhes** e **Eliminar**.

A aplicação utiliza um contexto de base de dados para se ligar à base de dados. Neste exemplo, o contexto de base de dados utiliza uma cadeia de ligação com o nome `MyDbConnection`. A cadeia de ligação é definida no ficheiro *Web.config* e é referenciado no ficheiro *Models/MyDatabaseContext.cs*. O nome da cadeia de conexão é usado posteriormente no tutorial para conectar o aplicativo do Azure a um banco de dados SQL do Azure. 

## <a name="publish-to-azure-with-sql-database"></a>Publicar no Azure com a Base de Dados SQL

No **Explorador de Soluções**, clique com o botão direito do rato no projeto **DotNetAppSqlDb** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Certifique-se de que o **Serviço de Aplicações do Microsoft Azure** está selecionado e clique em **Publicar**.

![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

A publicação abre a caixa de diálogo **Criar serviço de aplicativo** , que ajuda a criar todos os recursos do Azure necessários para executar seu aplicativo ASP.net no Azure.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **Criar Serviço de Aplicações**, clique em **Adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se já tiver sessão iniciada numa conta Microsoft, confirme que esta inclui a sua subscrição do Azure. Se a subscrição do Azure não estiver na conta Microsoft em que tem sessão iniciada, clique nesta para adicionar a conta correta. 

> [!NOTE]
> Se já tiver sessão iniciada, não selecione ainda **Criar**.
>
>
   
![Iniciar sessão no Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

### <a name="configure-the-web-app-name"></a>Configurar o nome da aplicação Web

Pode manter o nome da aplicação Web gerado ou alterá-lo para outro nome exclusivo (os carateres válidos são `a-z`, `0-9` e `-`). O nome da aplicação Web é utilizado como parte do URL predefinido da sua aplicação (`<app_name>.azurewebsites.net`, onde `<app_name>` é o nome da aplicação Web). O nome da aplicação Web tem de ser exclusivo em todas as aplicações no Azure. 

![Caixa de diálogo Criar Serviço de Aplicações](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Junto a **Grupo de recursos**, clique em **Novo**.

![Junto a Grupo de Recursos, clique em Novo.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

Dê o nome **myResourceGroup** ao grupo de recursos.

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Junto a **Plano do Serviço de Aplicações**, clique em **Novo**. 

Na caixa de diálogo **Configurar o Plano do Serviço de Aplicações**, configure o plano novo com as definições seguintes:

![Criar plano do App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Definição  | Valor sugerido | Para obter mais informações: |
| ----------------- | ------------ | ----|
|**App Service Plan** (Plano do Serviço de Aplicações)| myAppServicePlan | [Planos do Serviço de Aplicações](../app-service/overview-hosting-plans.md) |
|**Localização**| Europa Ocidental | [Regiões do Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**Tamanho**| Gratuito | [Escalões de preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>Criar uma instância do SQL Server

Antes de criar uma base de dados, precisa de um [servidor lógico da Base de Dados SQL do Azure](../sql-database/sql-database-features.md). Um servidor lógico contém um grupo de bases de dados geridas como um grupo.

Clique em **Criar uma Base de Dados SQL**.

![Criar uma Base de Dados SQL](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

Na caixa de diálogo **Configurar Base de Dados SQL**, clique em **Nova** junto a **SQL Server**. 

É gerado um nome de servidor exclusivo. Este nome é utilizado como parte do URL predefinido do servidor lógico, `<server_name>.database.windows.net`. Tem de ser exclusivo em todas as instâncias do servidor lógico no Azure. Pode alterar o nome do servidor, mas, para este tutorial, mantenha o valor gerado.

Adicione um nome de utilizador e uma palavra-passe de administrador. Para saber os requisitos de complexidade das palavras-passe, veja [Password Policy](/sql/relational-databases/security/password-policy) (Política de Palavras-passe).

Memorize o nome de utilizador e a palavra-passe. Precisa dos mesmos para gerir a instância do servidor lógico mais tarde.

> [!IMPORTANT]
> Embora a palavra-passe nas cadeias de ligação esteja ocultada (no Visual Studio e também no Serviço de Aplicações), o facto de ser mantida algures aumenta a superfície de ataque da sua aplicação. O Serviço de Aplicações pode utilizar [identidades de serviço geridas](overview-managed-identity.md) para eliminar este risco ao remover a necessidade de manter segredos na configuração do código ou da aplicação. Para obter mais informações, veja [Passos seguintes](#next-steps).

![Criar uma instância do SQL Server](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

Clique em **OK**. Não feche já a caixa de diálogo **Configurar Base de Dados SQL**.

### <a name="create-a-sql-database"></a>Criar uma Base de Dados SQL

Na caixa de diálogo **Configurar Base de Dados SQL**: 

* Mantenha o **Nome da Base de Dados** predefinido gerado.
* Em **Nome da Cadeia de Ligação**, escreva *MyDbConnection*. Este nome tem de corresponder à cadeia de ligação que é referenciada em *Models/MyDatabaseContext.cs*.
* Selecione **OK**.

![Configurar a Base de Dados SQL](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

A caixa de diálogo **Criar Serviço de Aplicações** mostra os recursos que configurou. Clique em **Criar**. 

![os recursos que criou](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

Quando o assistente concluir a criação dos recursos do Azure, publica a aplicação ASP.NET no Azure. O seu browser é iniciado com o URL para a aplicação implementada. 

Adicione alguns itens a fazer.

![Aplicativo ASP.NET publicado no aplicativo do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Parabéns! A aplicação ASP.NET condicionada por dados está em execução no Serviço de Aplicações do Azure.

## <a name="access-the-sql-database-locally"></a>Aceder à Base de Dados SQL localmente

O Visual Studio permite-lhe explorar e gerir a sua Base de Dados SQL nova facilmente no **SQL Server Object Explorer**.

### <a name="create-a-database-connection"></a>Criar uma ligação à base de dados

No menu **Ver**, selecione **SQL Server Object Explorer**.

Na parte superior do **SQL Server Object Explorer**, clique no botão **Adicionar SQL Server**.

### <a name="configure-the-database-connection"></a>Configurar a ligação à base de dados

Na caixa de diálogo **Ligar**, expanda o nó **Azure**. São apresentadas aqui todas as suas instâncias da Base de Dados SQL no Azure.

Selecione a Base de Dados SQL que criou anteriormente. A ligação que criou anteriormente é preenchida automaticamente na parte inferior.

Escreva a palavra-passe de administrador da base de dados que criou acima e clique em **Ligar**.

![Configurar a ligação à base de dados a partir do Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Permitir a ligação de cliente a partir do computador

É aberta a caixa de diálogo **Criar uma regra de firewall nova**. Por padrão, a instância do banco de dados SQL permite apenas conexões de serviços do Azure, como seu aplicativo do Azure. Para ligar à sua base de dados, crie uma regra de firewall na instância da Base de Dados SQL. A regra de firewall permite o endereço IP público do seu computador local.

A caixa de diálogo já está preenchida com o endereço IP público do seu computador.

Certifique-se de que **Adicionar o meu IP de cliente** está selecionado e clique em **OK**. 

![Definir a firewall para a instância da Base de Dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Quando o Visual Studio concluir a criação da definição de firewall para a sua instância da Base de Dados SQL, a ligação aparece em **SQL Server Object Explorer**.

Aqui, pode realizar as operações mais comuns de bases de dados, como executar consultas, criar vistas e procedimentos armazenados, entre outras. 

Expanda a ligação > **Bases de Dados** >  **&lt;a sua base de dados>**  > **Tabelas**. Clique com o botão direito do rato na tabela `Todoes` e selecione **Ver Dados**. 

![Explorar os objetos da Base de Dados SQL](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Atualizar a aplicação com Migrações Code First

Você pode usar as ferramentas familiares no Visual Studio para atualizar seu banco de dados e aplicativo no Azure. Neste passo, vai utilizar Migrações First Code em Entity Framework para fazer uma alteração ao esquema da base de dados e publicá-la no Azure.

Para obter mais informações sobre como utilizar o Entity Framework Code First Migrations, veja [Getting Started with Entity Framework 6 Code First using MVC 5](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (Introdução a Entity Framework 6 Code First com MVC 5).

### <a name="update-your-data-model"></a>Atualizar o modelo de dados

Abra _Models\Todo.cs_ no editor de código. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Executar Primeiras Migrações de Código localmente

Execute alguns comandos para fazer atualizações à base de dados local. 

No menu **Ferramentas**, clique em **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes**.

Na janela da Consola do Gestor de Pacotes, ative as Migrações Code First:

```powershell
Enable-Migrations
```

Adicione uma migração:

```powershell
Add-Migration AddProperty
```

Atualize a base de dados local:

```powershell
Update-Database
```

Escreva `Ctrl+F5` para executar a aplicação. Teste as ligações Editar, Detalhes e Criar.

Se a aplicação for carregada sem erros, as Migrações First Code foram bem-sucedidas. No entanto, a página continua igual, porque a lógica da aplicação ainda não está a utilizar esta propriedade. 

### <a name="use-the-new-property"></a>Utilizar a nova propriedade

Faça algumas alterações no seu código para utilizar a propriedade `Done`. Para simplificar, neste tutorial, apenas vai alterar as vistas `Index` e `Create` para ver a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localize o método `Create()` na linha 52 e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` é semelhante ao seguinte código:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código Razor, deverá ver um elemento `<div class="form-group">` que utiliza `model.Description` e outro elemento `<div class="form-group">` que utiliza `model.CreatedDate`. Imediatamente a seguir a estes dois elementos, adicione outro elemento `<div class="form-group">` que utilize `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Procure o elemento `<th></th>` vazio. Imediatamente acima deste elemento, adicione o seguinte código Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localize o elemento `<td>` que contém os métodos do programa auxiliar `Html.ActionLink()`. _Acima_ deste elemento `<td>`, adicione outro elemento `<td>` com o código Razor seguinte:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

É tudo o que precisa para ver as alterações nas vistas `Index` e `Create`. 

Escreva `Ctrl+F5` para executar a aplicação.

Agora, pode adicionar um item a fazer e marcar **Concluído**. Em seguida, deve ser apresentado na sua home page como um item concluído. Lembre-se de que a vista `Edit` não apresenta o campo `Done`, porque a vista `Edit` não foi alterada.

### <a name="enable-code-first-migrations-in-azure"></a>Ativar Migrações First Code no Azure

Agora que a alteração do código funciona, incluindo a migração de banco de dados, você a publica em seu aplicativo do Azure e também atualiza seu banco de dados SQL com o Migrações do Code First.

Tal como anteriormente, clique com o botão direito do rato no projeto e selecione **Publicar**.

Clique em **Configurar** para abrir as definições de publicação.

![Abrir as definições de publicação](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

No assistente, clique em **Seguinte**.

Confirme que a cadeia de ligação da Base de Dados SQL está preenchida em **MyDatabaseContext (MyDbConnection)** . Poderá ter de selecionar a base de dados **myToDoAppDb** na lista pendente. 

Selecione **Executar Migrações Code First (executadas quando a aplicação é iniciada)** e clique em **Guardar**.

![Habilitar Migrações do Code First no aplicativo do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publicar as alterações

Agora que você habilitou Migrações do Code First em seu aplicativo do Azure, publique as alterações de código.

Na página de publicação, clique em **Publicar**.

Experimente voltar a adicionar itens de tarefas a fazer e selecione **Concluído**. As tarefas devem agora aparecer na sua home page como itens concluídos.

![Aplicativo do Azure após Code First migração](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Todos os itens a fazer existentes continuam a ser apresentados. Quando voltar a publicar a aplicação ASP.NET, os dados existentes na Base de Dados SQL não são perdidos. Além disso, as Migrações Code First apenas alteram o esquema de dados e mantêm os dados existentes intactos.


## <a name="stream-application-logs"></a>Transmitir em fluxo registos de aplicações

Você pode transmitir mensagens de rastreamento diretamente do seu aplicativo do Azure para o Visual Studio.

Abra _Controllers\TodosController.cs_.

Cada ação começa com um método `Trace.WriteLine()`. Esse código é adicionado para mostrar como adicionar mensagens de rastreamento ao seu aplicativo do Azure.

### <a name="open-server-explorer"></a>Abrir o Explorador de Servidores

No menu **Ver**, selecione **Explorador de Servidores**. Você pode configurar o registro em log para seu aplicativo do Azure no **Gerenciador de servidores**. 

### <a name="enable-log-streaming"></a>Ativar a transmissão em fluxo de registos

No **Explorador de Servidores**, expanda **Azure** > **Serviço de Aplicações**.

Expanda o grupo de recursos **MyResource** Group, criado quando você criou o aplicativo do Azure pela primeira vez.

Clique com o botão direito do mouse no aplicativo do Azure e selecione **Exibir logs de streaming**.

![Ativar a transmissão em fluxo de registos](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Os registos são agora transmitidos em fluxo para a janela **Saída**. 

![Registo de transmissões em fluxo na janela Saída](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

No entanto, ainda não vai ver nenhuma das mensagens de rastreio. Isso ocorre porque, quando você seleciona a **exibição de logs de streaming**pela primeira vez, seu aplicativo do Azure define o nível de rastreamento como `Error`, que registra apenas os eventos de erro (com o método `Trace.TraceError()`).

### <a name="change-trace-levels"></a>Alterar os níveis de rastreio

Para alterar os níveis de rastreio para produzir outras mensagens de rastreio, regresse ao **Explorador de Servidores**.

Clique com o botão direito do mouse no aplicativo do Azure novamente e selecione **exibir configurações**.

No menu pendente **Registo da Aplicação (Sistema de Ficheiros)** , selecione **Verboso**. Clique em **Guardar**.

![Alterar o nível de rastreio para Verboso](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Pode experimentar diferentes níveis de rastreio para ver os tipos de mensagens que são apresentadas para cada nível. Por exemplo, o nível **Informação** inclui todos os registos criados por `Trace.TraceInformation()`, `Trace.TraceWarning()` e `Trace.TraceError()`, mas não os criados por `Trace.WriteLine()`.
>
>

No seu navegador, navegue até seu aplicativo novamente em *http://&lt;o nome do aplicativo >. azurewebsites. net*e tente clicar em volta do aplicativo de lista de tarefas pendentes no Azure. As mensagens de rastreio são agora transmitidas em fluxo para a janela **Saída** no Visual Studio.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Parar o registo de transmissão em fluxo

Para parar o serviço de transmissão em fluxo de registos, clique no botão **Parar a monitorização** na janela **Saída**.

![Parar o registo de transmissão em fluxo](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Gerenciar seu aplicativo do Azure

Aceda ao [portal do Azure](https://portal.azure.com) para gerir a aplicação Web. Pesquise e selecione **serviços de aplicativos**. 

![Pesquisar serviços de Azure App](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Selecione o nome do seu aplicativo do Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Você tem descarregou na página do seu aplicativo. 

Por predefinição, o portal mostra a página **Descrição Geral**. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir. 

![Página Serviço de Aplicações no portal do Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma Base de Dados SQL no Azure
> * Ligar uma aplicação ASP.NET à Base de Dados SQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avance para o tutorial seguinte para saber como melhorar facilmente a segurança da sua ligação à Base de Dados SQL do Azure.

> [!div class="nextstepaction"]
> [Aceder à Base de Dados SQL em segurança com identidades geridas dos recursos do Azure](app-service-web-tutorial-connect-msi.md)
