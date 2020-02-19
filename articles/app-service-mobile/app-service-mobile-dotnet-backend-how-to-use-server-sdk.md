---
title: Trabalhe com o servidor de backend .NET SDK
description: Saiba como trabalhar com o servidor de backend .NET SDK para aplicações móveis do Serviço de Aplicações Azure.
keywords: serviço de aplicações, serviço de aplicações azure, aplicação móvel, serviço móvel, escala, escala, implementação de aplicações, implementação de aplicações azure
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461543"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabalhe com o servidor de backend .NET SDK para aplicações móveis Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tópico mostra-lhe como utilizar o Servidor de backend .NET SDK nos cenários de aplicações móveis do Serviço de Aplicações Azure. O Azure Mobile Apps SDK ajuda-o a trabalhar com clientes móveis a partir da sua aplicação ASP.NET.

> [!TIP]
> O [servidor .NET SDK para Aplicações Móveis Azure][2] é fonte aberta no GitHub. O repositório contém todo o código fonte, incluindo todo o conjunto de testes da unidade SDK do servidor e alguns projetos de amostra.
>
>

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o servidor SDK está localizada aqui: [Azure Mobile Apps .NET Reference][1].

## <a name="create-app"></a>Como: Criar um backend de uma aplicação móvel .NET
Se estiver a iniciar um novo projeto, pode criar uma aplicação de Serviço de Aplicações utilizando o [Portal do Azure] ou o Visual Studio. Pode executar a aplicação do Serviço de Aplicações localmente ou publicar o projeto na sua aplicação móvel app Service baseada na nuvem.

Se estiver a adicionar capacidades móveis a um projeto existente, consulte o Download e inicialize a secção [SDK.](#install-sdk)

### <a name="create-a-net-backend-using-the-azure-portal"></a>Crie um backend .NET utilizando o portal Azure
Para criar um backend móvel do App Service, siga o [tutorial Quickstart][3] ou siga estes passos:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta à lâmina *Get started,* sob **C#** Criar uma Tabela **API,** escolha como a sua língua **backend**. Clique em **Download,** extraia os ficheiros do projeto comprimido para o seu computador local e abra a solução no Estúdio Visual.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Crie um backend .NET usando o Visual Studio 2017

Instale a carga de trabalho azure através do Instalador de Estúdios Visuais para publicar no projeto De Aplicações Móveis Azure do Visual Studio. Depois de ter instalado o SDK, crie uma aplicação ASP.NET utilizando os seguintes passos:

1. Abra o diálogo **new project** (de **File** > **New** > **Project...** ).
2. Expandir  **C# visual** e selecionar **Web**.
3. Selecione **ASP.NET Aplicação Web (.QUADRO NET)** .
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Selecione **Aplicação Móvel Azure** da lista de modelos.
6. Clique em **OK** para criar a solução.
7. Clique no projeto no **Solution Explorer** e escolha **Publicar...** e, em seguida, escolha o **App Service** como alvo de publicação.
8. Siga as instruções para autenticar e escolha um novo ou já existente Serviço de Aplicações Azure para publicar.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Crie um backend .NET usando o Visual Studio 2015

Instale o [Azure SDK para .NET][4] (versão 2.9.0 ou posterior) para criar um projeto de Aplicações Móveis Azure no Estúdio Visual. Depois de ter instalado o SDK, crie uma aplicação ASP.NET utilizando os seguintes passos:

1. Abra o diálogo **new project** (de **File** > **New** > **Project...** ).
2. Expandir **modelos** > **visual, C#** e selecionar **Web**.
3. Selecione **Aplicação Web ASP.NET**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Em *ASP.NET modelos 4.5.2,* selecione **Azure Mobile App**. Consulte **o Host na nuvem** para criar um backend móvel na nuvem para onde pode publicar este projeto.
6. Clique em **OK**.

## <a name="install-sdk"></a>Como: Baixar e inicializar o SDK
O SDK está disponível em [NuGet.org]. Este pacote inclui a funcionalidade base necessária para começar a usar o SDK. Para inicializar o SDK, é necessário realizar ações no objeto **HttpConfiguration.**

### <a name="install-the-sdk"></a>Instalar o SDK
Para instalar o SDK, clique no direito do projeto do servidor no Estúdio Visual, selecione **Gerir pacotes NuGet,** procurar o pacote [Microsoft.Azure.Mobile.Server] e, em seguida, clique em **Instalar**.

### <a name="server-project-setup"></a>Inicializar o projeto do servidor
Um projeto de servidor de backend .NET é inicializado semelhante a outros projetos de ASP.NET, incluindo uma classe de startup oWIN. Certifique-se de que fez referência ao pacote NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar esta aula no Visual Studio, clique no seu projeto de servidor e selecione **Adicionar** >
**Novo Item,** em seguida, **web** > **classe De** Arranque Geral > **OWIN**.  Uma classe é gerada com o seguinte atributo:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

No método `Configuration()` da sua classe de startupo OWIN, utilize um objeto **HttpConfiguration** para configurar o ambiente de Aplicações Móveis Azure.
O exemplo seguinte inicializa o projeto do servidor sem funcionalidades adicionais:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para ativar as funcionalidades individuais, deve ligar para métodos de extensão no objeto **MobileAppConfiguration** antes de ligar **para O ApplyTo**. Por exemplo, o seguinte código adiciona as rotas predefinidas a todos os controladores API que tenham o atributo `[MobileAppController]` durante a inicialização:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

O servidor começa rapidamente a partir do portal Azure chama **De utilizaçãoDefaultConfigura()** . Isto equivale à seguinte configuração:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Os métodos de extensão utilizados são:

* `AddMobileAppHomeController()` fornece a página inicial de Aplicações Móveis Azure padrão.
* `MapApiControllers()` fornece capacidades aPI personalizadas para controladores WebAPI decorados com o atributo `[MobileAppController]`.
* `AddTables()` fornece um mapeamento dos pontos finais `/tables` aos controladores de mesa.
* `AddTablesWithEntityFramework()` é uma mão curta para mapear os pontos finais `/tables` utilizando controladores baseados em Quadro de Entidades.
* `AddPushNotifications()` fornece um método simples de registo de dispositivos para Centros de Notificação.
* `MapLegacyCrossDomainController()` fornece cabeçalhos CORS padrão para o desenvolvimento local.

### <a name="sdk-extensions"></a>Extensões SDK
Os seguintes pacotes de extensão baseados em NuGet fornecem várias funcionalidades móveis que podem ser usadas pela sua aplicação. Ativa as extensões durante a inicialização utilizando o objeto **MobileAppConfiguration.**

* [Microsoft.Azure.Mobile.Server.Quickstart] suporta a configuração básica de Aplicações Móveis. Adicionado à configuração, chamando o método de extensão **UseDefaultConfiguration** durante a inicialização. Esta extensão inclui seguintes extensões: Notificações, Autenticação, Entidade, Tabelas, Domínio Cruzado e Pacotes Domésticos. Este pacote é utilizado pelas Aplicações Móveis Quickstart disponíveis no portal Azure.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementa o padrão *desta aplicação móvel está em funcionamento* página para a raiz do site. Adicione à configuração ligando para o método de extensão **AddMobileAppHomeController.**
* [Microsoft.Azure.Mobile.Server.As tabelas](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) incluem aulas para trabalhar com dados e configurar o pipeline de dados. Adicione à configuração chamando o método de extensão **AddTables.**
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Quadro de Entidades aceda a dados na Base de Dados SQL. Adicione à configuração chamando o método de extensão **AddTablesWithEntityFramework.**
* [Microsoft.Azure.Mobile.Server.Authentication] ativa a autenticação e configura o middleware OWIN usado para validar fichas. Adicione à configuração chamando o **AddAppServiceAuthentication** e **iAppBuilder**. Métodos de extensão de extensão **useAppServiceAuthentication.**
* [Microsoft.Azure.Mobile.Server.Notificações] permite notificações push e define um ponto final de registo push. Adicione à configuração ligando para o método de extensão **AddPushNotifications.**
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Cria um controlador que serve dados para navegadores web legados a partir da sua Aplicação Móvel. Adicione à configuração chamando o método de extensão **MapLegacyCrossDomainController.**
* [Microsoft.Azure.Mobile.Server.Login] fornece o método AppServiceLoginHandler.CreateToken() que é um método estático utilizado durante cenários de autenticação personalizada.

## <a name="publish-server-project"></a>Como: Publicar o projeto do servidor
Esta secção mostra-lhe como publicar o seu projeto de backend .NET do Visual Studio. Também pode implementar o seu projeto backend usando [Git](../app-service/deploy-local-git.md) ou qualquer outro método disponível lá.

1. No Estúdio Visual, reconstrói o projeto para restaurar os pacotes NuGet.
2. No Solution Explorer, clique no projeto, clique em **Publicar**. A primeira vez que publicar, precisa definir um perfil editorial. Quando já tem um perfil definido, pode selecioná-lo e clicar em **Publicar**.
3. Se for solicitado a selecionar um alvo de publicação, clique no **Microsoft Azure App Service** > **Seguinte**, então (se necessário) iniciar sessão com as suas credenciais Azure.
   O Visual Studio descarrega e armazena de forma segura as suas definições de publicação diretamente do Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Escolha a sua **Subscrição,** selecione **O Tipo** de Recurso a partir de **'Ver',** expanda **a Aplicação Móvel**, e clique no backend da Aplicação Móvel e, em seguida, clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Verifique as informações de perfil de publicação e clique em **Publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Quando o seu backend da Aplicação Móvel tiver sido publicado com sucesso, vê uma página de aterragem indicando sucesso.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Como: Definir um controlador de mesa
Defina um Controlador de Mesa para expor uma tabela SQL a clientes móveis.  Configurar um controlador de mesa requer três passos:

1. Crie uma classe de Objeto de Transferência de Dados (DTO).
2. Configure uma referência de tabela na classe Mobile DbContext.
3. Crie um controlador de mesa.

Um objeto de transferência de dados C# (DTO) é um objeto simples que herda de `EntityData`.  Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

O DTO é utilizado para definir a tabela dentro da base de dados SQL.  Para criar a entrada na base de dados, adicione uma propriedade `DbSet<>` ao DbContext que está a utilizar.  No modelo de projeto padrão para aplicações móveis Azure, o DbContext chama-se `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Se tiver o SDK Azure instalado, pode agora criar um controlador de tabela de modelos da seguinte forma:

1. Clique à direita na pasta controladore **selecione Adicionar** > **Controlador...** .
2. Selecione a opção de controlador de tabela de **aplicações móveis Azure** e, em seguida, clique em **Adicionar**.
3. No diálogo do **Controlador adicionar:**
   * Na **classe Modelo,** selecione o seu novo DTO.
   * No **dropdown dbContext,** selecione a classe DbContext de Serviço Móvel.
   * O nome controlador é criado para si.
4. Clique em **Adicionar**.

O projeto do servidor quickstart contém um exemplo para um **simples TodoItemController**.

### <a name="adjust-pagesize"></a>Como: Ajustar o tamanho da tabela de paging
Por padrão, as Aplicações Móveis Azure devolvem 50 registos por pedido.  A Paging garante que o cliente não amarrou o seu fio UI nem o servidor por muito tempo, garantindo uma boa experiência do utilizador. Para alterar o tamanho da tabela, aumente o lado do servidor "tamanho de consulta permitido" e o tamanho da página do lado do cliente O lado do servidor "tamanho de consulta permitida" é ajustado utilizando o `EnableQuery` atributo:

    [EnableQuery(PageSize = 500)]

Certifique-se de que o PageSize é o mesmo ou maior do que o tamanho solicitado pelo cliente.  Consulte a documentação específica do cliente HOWTO para obter detalhes sobre a alteração do tamanho da página do cliente.

## <a name="how-to-define-a-custom-api-controller"></a>Como: Definir um controlador API personalizado
O controlador API personalizado fornece a funcionalidade mais básica para o seu backend de Aplicação Móvel expondo um ponto final. Pode registar um controlador API específico para dispositivos móveis utilizando o atributo [MobileAppController]. O atributo `MobileAppController` regista a rota, configura o serializador De Aplicações Móveis JSON e liga [a verificação da versão do cliente.](app-service-mobile-client-and-server-versioning.md)

1. No Estúdio Visual, clique na pasta controladora da direita e, em seguida, clique em **Adicionar** **controlador** > , selecione **Controlador Web API 2&mdash;Vazio** e clique em **Adicionar**.
2. Forneça um **nome**controlador , como `CustomController`, e clique em **Adicionar**.
3. No novo ficheiro da classe controladora, adicione o seguinte comunicado:

        using Microsoft.Azure.Mobile.Server.Config;
4. Aplicar o atributo **[MobileAppController]** à definição de classe de controlador API, como no exemplo seguinte:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. No ficheiro App_Start/Startup.MobileApp.cs, adicione uma chamada ao método de extensão **mapApiControllers,** como no exemplo seguinte:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Também pode utilizar o método de extensão `UseDefaultConfiguration()` em vez de `MapApiControllers()`. Qualquer controlador que não tenha **mobileAppControllerAttribute** aplicado ainda pode ser acedido pelos clientes, mas pode não ser corretamente consumido pelos clientes que usam qualquer cliente de Aplicação Móvel SDK.

## <a name="how-to-work-with-authentication"></a>Como: Trabalhar com autenticação
As Aplicações Móveis Azure utilizam a autenticação /autorização do serviço de aplicação para garantir o seu backend móvel.  Esta secção mostra-lhe como executar as seguintes tarefas relacionadas com a autenticação no seu projeto de servidor de backend .NET:

* [Como: Adicionar autenticação a um projeto de servidor](#add-auth)
* [Como: Utilizar a autenticação personalizada para a sua aplicação](#custom-auth)
* [Como: Recuperar informações autenticadas do utilizador](#user-info)
* [Como: Restringir o acesso de dados a utilizadores autorizados](#authorize)

### <a name="add-auth"></a>Como: Adicionar autenticação a um projeto de servidor
Pode adicionar autenticação ao seu projeto de servidor, alargando o objeto **MobileAppConfiguration** e configurando o middleware OWIN. Quando instalar o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e ligar para o método de extensão **UseDefaultConfiguration,** pode saltar para o passo 3.

1. No Estúdio Visual, instale o pacote [Microsoft.Azure.mobile.Server.Authentication]
2. No ficheiro Startup.cs projeto, adicione a seguinte linha de código no início do método de **Configuração:**

        app.UseAppServiceAuthentication(config);

    Este componente de middleware OWIN valida tokens emitidos pelo gateway do Serviço de Aplicações associado.
3. Adicione o atributo `[Authorize]` a qualquer controlador ou método que exija autenticação.

Para saber como autenticar clientes no backend das suas Aplicações Móveis, consulte [Adicionar autenticação à sua aplicação](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como: Utilizar a autenticação personalizada para a sua aplicação
> [!IMPORTANT]
> Para permitir a autenticação personalizada, tem primeiro de ativar a Autenticação do Serviço de Aplicações sem selecionar um fornecedor para o seu Serviço de Aplicações no portal Azure. Isto permitirá que a variável ambiente WEBSITE_AUTH_SIGNING_KEY quando alojada.
> 
> 
> Se não pretender utilizar um dos fornecedores de autenticação/autorização do serviço app, pode implementar o seu próprio sistema de login. Instale o pacote [Microsoft.Azure.Mobile.Server.Login] para ajudar na geração token de autenticação.  Forneça o seu próprio código para validar as credenciais do utilizador. Por exemplo, pode verificar palavras-passe salgadas e hashed numa base de dados. No exemplo abaixo, o método `isValidAssertion()` (definido noutros locais) é responsável por estes controlos.

A autenticação personalizada é exposta através da criação de um ApiController e expondo `register` e `login` ações. O cliente deve utilizar um UI personalizado para recolher a informação do utilizador.  A informação é então submetida à API com uma chamada http post padrão. Uma vez que o servidor valida a afirmação, um token é emitido usando o método `AppServiceLoginHandler.CreateToken()`.  O ApiController **não deve** utilizar o atributo `[MobileAppController]`.

Um exemplo `login` ação:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

No exemplo anterior, LoginResult e LoginResultUser são objetos serlizáveis expondo as propriedades necessárias. O cliente espera que as respostas de login sejam devolvidas como objetos JSON do formulário:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

O método `AppServiceLoginHandler.CreateToken()` inclui um *público* e um parâmetro *emitentes.* Ambos os parâmetros estão definidos para o URL da sua raiz de aplicação, utilizando o esquema HTTPS. Da mesma forma, deve definir *o segredoChave* para ser o valor da chave de assinatura da sua aplicação. Não distribua a chave de assinatura num cliente, pois pode ser utilizada para cunhar chaves e personificar os utilizadores. Pode obter a chave de assinatura enquanto está hospedado no Serviço de Aplicações, referindo-se ao *site\_AUTH\_assinar\_* variável de ambiente KEY. Se necessário num contexto de depuração local, siga as instruções na [depuração local com](#local-debug) secção de autenticação para recuperar a chave e armazená-la como uma definição de aplicação.

O token emitido também pode incluir outros pedidos e uma data de validade.  Minimamente, o símbolo emitido deve incluir uma alegação **(sub).**

Pode suportar o método `loginAsync()` cliente padrão sobrecarregando a rota de autenticação.  Se o cliente ligar `client.loginAsync('custom');` para fazer login, a sua rota deve ser `/.auth/login/custom`.  Pode definir a rota para o controlador de autenticação personalizado utilizando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> A utilização da abordagem `loginAsync()` garante que o símbolo de autenticação está ligado a todas as chamadas subsequentes ao serviço.
>
>

### <a name="user-info"></a>Como: Recuperar informações autenticadas do utilizador
Quando um utilizador é autenticado pelo App Service, pode aceder ao ID do utilizador e outras informações no seu código de backend .NET. As informações do utilizador podem ser utilizadas para tomar decisões de autorização no backend. O seguinte código obtém o ID do utilizador associado a um pedido:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

O SID é derivado do ID específico do utilizador do fornecedor e é estático para um determinado utilizador e fornecedor de login.  O SID é nulo para fichas de autenticação inválidas.

O Serviço de Aplicações também permite solicitar reclamações específicas ao seu fornecedor de login. Cada fornecedor de identidade pode fornecer mais informações usando o fornecedor de identidade SDK.  Por exemplo, pode utilizar a API do Facebook Graph para informação de amigos.  Pode especificar as reclamações solicitadas na lâmina do fornecedor no portal Azure. Algumas alegações requerem configuração adicional com o fornecedor de identidade.

O código seguinte chama o método de extensão **GetAppServiceIdentityAsync** para obter as credenciais de login, que incluem o sinal de acesso necessário para fazer pedidos contra a API do Gráfico do Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Adicione uma declaração de utilização para `System.Security.Principal` fornecer o método de extensão **GetAppServiceIdentityAsync.**

### <a name="authorize"></a>Como: Restringir o acesso de dados a utilizadores autorizados
Na secção anterior, mostrámos como recuperar a identificação do utilizador de um utilizador autenticado. Pode restringir o acesso a dados e outros recursos com base neste valor. Por exemplo, adicionar uma coluna userId às tabelas e filtrar os resultados da consulta pelo ID do utilizador é uma forma simples de limitar os dados devolvidos apenas aos utilizadores autorizados. O código seguinte devolve linhas de dados apenas quando o SID corresponde ao valor na coluna UserId na tabela TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

O método `Query()` devolve uma `IQueryable` que pode ser manipulada pelo LINQ para lidar com a filtragem.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Como: Adicionar notificações push a um projeto de servidor
Adicione notificações push ao seu projeto de servidor, alargando o objeto **de Configuração MobileApp** e criando um cliente de Centros de Notificação.

1. No Estúdio Visual, clique no projeto do servidor e clique em **Gerir pacotes NuGet,** procure `Microsoft.Azure.Mobile.Server.Notifications`, em seguida, clique em **Instalar**.
2. Repita este passo para instalar o pacote `Microsoft.Azure.NotificationHubs`, que inclui a biblioteca de clientes do Notification Hubs.
3. Em App_Start/Startup.MobileApp.cs e adicione uma chamada ao método de extensão **AddPushNotifications** durante a inicialização:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adicione o seguinte código que cria um cliente de Centros de Notificação:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Pode agora utilizar o cliente Do Centro de Notificação para enviar notificações push para dispositivos registados. Para mais informações, consulte [Adicionar notificações push à sua aplicação](app-service-mobile-ios-get-started-push.md). Para saber mais sobre centros de notificação, consulte a visão geral dos Centros de [Notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Como: Ativar o impulso direcionado utilizando tags
O Notification Hubs permite-lhe enviar notificações específicas para registos específicos utilizando tags. Várias tags são criadas automaticamente:

* O ID de instalação identifica um dispositivo específico.
* O Id do utilizador com base no SID autenticado identifica um utilizador específico.

O ID de instalação pode ser acedido a partir da propriedade **de instalaçãoId** no **MobileServiceClient**.  O exemplo que se segue mostra como utilizar um ID de instalação para adicionar uma etiqueta a uma instalação específica em Centros de Notificação:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Quaisquer etiquetas fornecidas pelo cliente durante o registo de notificação push são ignoradas pelo backend ao criar a instalação. Para permitir que um cliente adicione etiquetas à instalação, deve criar uma API personalizada que adicione tags utilizando o padrão anterior.

Consulte [as etiquetas de notificação push adicionadas][5] pelo Cliente nas Aplicações móveis do Serviço de Aplicação, a amostra de arranque rápido, por exemplo.

## <a name="push-user"></a>Como: Enviar notificações push a um utilizador autenticado
Quando um utilizador autenticado se regista para notificações push, uma etiqueta de identificação do utilizador é automaticamente adicionada ao registo. Ao utilizar esta etiqueta, pode enviar notificações push para todos os dispositivos registados por essa pessoa. O seguinte código obtém o SID do utilizador que faz o pedido e envia uma notificação de push modelo para cada registo do dispositivo para essa pessoa:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Ao registar-se para notificações push de um cliente autenticado, certifique-se de que a autenticação está completa antes de tentar o registo. Para mais informações, consulte [Push para os utilizadores][6] nas Aplicações De Aplicação Aplicações Móveis que completaram a amostra de arranque rápido para o backend .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Como: Depurar e resolver problemas o .NET Server SDK
O Azure App Service fornece várias técnicas de depuração e resolução de problemas para aplicações ASP.NET:

* [Monitorização de um Serviço de Aplicações Azure](../app-service/web-sites-monitor.md)
* [Ativar a exploração de diagnóstico no serviço de aplicações Azure](../app-service/troubleshoot-diagnostic-logs.md)
* [Troubleshoot um Serviço de Aplicações Azure no Estúdio Visual](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Registo
Pode escrever para os registos de diagnóstico do Serviço de Aplicações utilizando a escrita padrão ASP.NET vestígios. Antes de poder escrever para os registos, tem de ativar os diagnósticos no seu backend da Aplicação Móvel.

Para permitir diagnósticos e escrever para os registos:

1. Siga os passos em Ativação de registo de [aplicações (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Adicione o seguinte utilizando a declaração no seu ficheiro de código:

        using System.Web.Http.Tracing;
3. Crie um autor de vestígios para escrever a partir do backend .NET para os registos de diagnóstico, da seguinte forma:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Republique o seu projeto de servidor e aceda ao backend da Aplicação Móvel para executar o código com a exploração madeireira.
5. Faça o download e avalie os registos, conforme descrito nos [ficheiros](../app-service/troubleshoot-diagnostic-logs.md#access-log-files)de registo do Access .

### <a name="local-debug"></a>Depuração local com autenticação
Pode executar a sua aplicação localmente para testar alterações antes de publicá-las na nuvem. Para a maioria dos backends da Azure Mobile Apps, prima *F5* enquanto está no Estúdio Visual. No entanto, existem algumas considerações adicionais ao utilizar a autenticação.

Deve ter uma aplicação móvel baseada na nuvem com autenticação/autorização do serviço app configurado, e o seu cliente deve ter o ponto final da nuvem especificado como anfitrião de login alternativo. Consulte a documentação da sua plataforma cliente para obter os passos específicos necessários.

Certifique-se de que o seu backend móvel tem [microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, na aula de startup oWIN da sua aplicação, adicione o seguinte, depois de `MobileAppConfiguration` ter sido aplicado ao seu `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

No exemplo anterior, deve configurar as definições de aplicação *authAudience* e *authIssuer* dentro do seu ficheiro Web.config para cada um ser o URL da sua raiz de aplicação, utilizando o esquema HTTPS. Da mesma forma, deve definir a *uthSigningKey* como o valor da chave de assinatura da sua aplicação.
Para obter a chave de assinatura:

1. Navegue para a sua app dentro do [Portal do Azure]
2. Clique **em Ferramentas,** **Kudu,** **Go**.
3. No site da Gestão Kudu, clique em **Ambiente.**
4. Encontre o valor do *website\_AUTH\_assinar\_CHAVE*.

Utilize a chave de sinalização para o parâmetro *authSigningKey* no seu config de aplicação local.  O seu backend móvel está agora equipado para validar tokens ao correr localmente, que o cliente obtém o símbolo do ponto final baseado em nuvem.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portal do Azure]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notificações]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
