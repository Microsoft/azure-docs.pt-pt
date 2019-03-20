---
title: Como trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis | Documentos da Microsoft
description: Saiba como trabalhar com o SDK do servidor de back-end de .NET para aplicações de Mobile do serviço de aplicações do Azure.
keywords: serviço de aplicações, serviço de aplicações do azure, aplicação móvel, serviços móveis, a implementação de aplicações para dimensionamento, dimensionável, aplicação de implementação, do azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 195a2dd88f443120f337ba441358389f0dc290f8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078793"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabalhe com o SDK do servidor de back-end .NET para Aplicações Móveis do Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tópico mostra-lhe como utilizar o servidor de back-end de .NET SDK no principais cenários de aplicações de Mobile do serviço de aplicações do Azure. O SDK de aplicações móveis do Azure ajuda-o a trabalhar com clientes móveis a partir do seu aplicativo ASP.NET.

> [!TIP]
> O [.NET SDK do servidor para aplicações móveis do Azure] [ 2] é open source no GitHub. O repositório contém todos os código de origem, incluindo o conjunto de testes de unidade SDK de servidor completo e alguns projetos de exemplo.
>
>

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o SDK do servidor está localizada aqui: [Referência de .NET de aplicações móveis do Azure][1].

## <a name="create-app"></a>Como: Criar um back-end de aplicação móvel de .NET
Se estiver a começar um novo projeto, pode criar uma aplicação de serviço de aplicações através da [portal do Azure] ou o Visual Studio. Pode executar a aplicação de serviço de aplicações localmente ou publicar o projeto à sua aplicação móvel de serviço de aplicações baseado na nuvem.

Se estiver a adicionar recursos móveis a um projeto existente, consulte a [baixar e inicializar o SDK](#install-sdk) secção.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Criar um back-end de .NET com o portal do Azure
Para criar um back-end móvel do serviço de aplicações, optar por seguir a [tutorial de início rápido] [ 3] ou siga estes passos:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta o *começar* painel, em **criar uma API de tabela**, escolha **c#** como sua **linguagem do back-end**. Clique em **transferir**, extraia os ficheiros de projeto comprimido no computador local e abra a solução no Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Criar um back-end de .NET com o Visual Studio 2017

Instale a carga de trabalho do Azure através da instalação do Visual Studio para publicar no projeto de Mobile Apps do Azure a partir do Visual Studio. Depois de instalar o SDK, crie uma aplicação ASP.NET com os seguintes passos:

1. Abra o **novo projeto** caixa de diálogo (da **ficheiro** > **novo** > **Project...** ).
2. Expanda **em Visual C#** e selecione **Web**.
3. Selecione **aplicação Web do ASP.NET (.NET Framework)**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Selecione **aplicação móvel do Azure** na lista de modelos.
6. Clique em **OK** para criar a solução.
7. Com o botão direito no projeto no **Explorador de soluções** e escolha **publicar...** , em seguida, escolha **serviço de aplicações** como o destino de publicação.
8. Siga as instruções para autenticar e escolher um serviço de aplicações do Azure nova ou existente para publicar.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Criar um back-end de .NET usando o Visual Studio 2015

Instalar o [Azure SDK para .NET] [ 4] (versão 2.9.0 ou posterior) para criar um projeto de Mobile Apps do Azure no Visual Studio. Depois de instalar o SDK, crie uma aplicação ASP.NET com os seguintes passos:

1. Abra o **novo projeto** caixa de diálogo (da **ficheiro** > **novo** > **Project...** ).
2. Expanda **modelos** > **em Visual C#** e selecione **Web**.
3. Selecione **Aplicação Web ASP.NET**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Sob *ASP.NET 4.5.2 modelos*, selecione **aplicação móvel do Azure**. Verifique **alojar na nuvem** para criar um back-end móvel na cloud para o qual pode publicar este projeto.
6. Clique em **OK**.

## <a name="install-sdk"></a>Como: Transferir e inicializar o SDK
O SDK está disponível no [NuGet.org]. Este pacote inclui a funcionalidade básica necessária para começar a utilizar o SDK. Para inicializar o SDK, tem de efetuar ações no **HttpConfiguration** objeto.

### <a name="install-the-sdk"></a>Instalar o SDK
Para instalar o SDK, com o botão direito no projeto de servidor no Visual Studio, selecione **gerir pacotes NuGet**, procure o [Microsoft.Azure.Mobile.Server] empacotar, em seguida, clique em **instalar** .

### <a name="server-project-setup"></a> Inicializar o projeto de servidor
Um projeto de servidor de back-end do .NET é inicializado semelhante a outros projetos do ASP.NET, incluindo uma classe de startup da OWIN. Certifique-se de que tem referenciados o pacote NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar essa classe no Visual Studio, clique com o botão direito no seu projeto de servidor e selecione **Add** >
**Novo Item**, em seguida, **Web**  >  ** Gerais** > **classe de Startup da OWIN**.  Uma classe é gerada com o atributo seguinte:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Na `Configuration()` método de sua classe de startup da OWIN, utilize um **HttpConfiguration** objeto para configurar o ambiente de aplicações móveis do Azure.
O exemplo seguinte inicializa o projeto de servidor com nenhuma recursos adicionais:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para ativar funcionalidades individuais, deve chamar os métodos de extensão no **MobileAppConfiguration** objeto antes de chamar **ApplyTo**. Por exemplo, o código a seguir adiciona as rotas predefinidas para todos os controladores de API que tem o atributo `[MobileAppController]` durante a inicialização:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

O guia de introdução do servidor das chamadas do portais do Azure **UseDefaultConfiguration()**. Este equivalente à configuração do seguinte:

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

Os métodos de extensão usados são:

* `AddMobileAppHomeController()` Fornece a home page de aplicações móveis do Azure padrão.
* `MapApiControllers()` Fornece capacidades de API personalizadas para controladores de WebAPI decorada com o `[MobileAppController]` atributo.
* `AddTables()` Fornece um mapeamento do `/tables` pontos finais para controladores de tabela.
* `AddTablesWithEntityFramework()` é uma mão curto para o mapeamento do `/tables` pontos de extremidade usando o Entity Framework com base em controladores.
* `AddPushNotifications()` Fornece um método simples de registar os dispositivos para os Hubs de notificação.
* `MapLegacyCrossDomainController()` Fornece os cabeçalhos CORS padrão para o desenvolvimento local.

### <a name="sdk-extensions"></a>Extensões do SDK
Os seguintes pacotes de NuGet com base na extensão fornecem vários recursos móveis que podem ser utilizados pela sua aplicação. Ativar extensões durante a inicialização, utilizando o **MobileAppConfiguration** objeto.

* [Microsoft.Azure.Mobile.Server.Quickstart] suporta a configuração de aplicações móveis básica. Adicionado à configuração ao chamar o **UseDefaultConfiguration** método de extensão durante a inicialização. Esta extensão inclui extensões a seguir: Notificações, a autenticação, a entidade, tabelas, entre domínios e os pacotes de Home. Este pacote é utilizado pelo Guia de introdução do aplicações móveis disponível no portal do Azure.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa a predefinição *esta aplicação móvel está em execução página* para a raiz do web site. Adicione à configuração ao chamar o **AddMobileAppHomeController** método de extensão.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) inclui classes para trabalhar com dados e conjuntos de cópia de segurança o pipeline de dados. Adicione à configuração ao chamar o **AddTables** método de extensão.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Entity Framework para aceder a dados na base de dados SQL. Adicione à configuração ao chamar o **AddTablesWithEntityFramework** método de extensão.
* [Microsoft.Azure.Mobile.Server.Authentication] permite a autenticação e o middleware da OWIN utilizado para validar os tokens de segurança de conjuntos. Adicione à configuração ao chamar o **AddAppServiceAuthentication** e **IAppBuilder**. **UseAppServiceAuthentication** métodos de extensão.
* [Microsoft.Azure.Mobile.Server.Notifications] permite enviar notificações push e define um ponto de final do registo de push. Adicione à configuração ao chamar o **AddPushNotifications** método de extensão.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) cria um controlador que serve os dados para navegadores da web herdado da sua aplicação móvel. Adicione à configuração ao chamar o **MapLegacyCrossDomainController** método de extensão.
* [Microsoft.Azure.Mobile.Server.Login] fornece o método AppServiceLoginHandler.CreateToken(), que é um método estático usado durante a cenários de autenticação personalizado.

## <a name="publish-server-project"></a>Como: Publicar o projeto de servidor
Esta secção mostra-lhe como publicar o seu projeto de back-end de .NET do Visual Studio. Também pode implementar seu projeto de back-end com [Git](../app-service/deploy-local-git.md) ou qualquer um do daí dos outros métodos disponíveis.

1. No Visual Studio, reconstrua o projeto para restaurar pacotes NuGet.
2. No Solution Explorer, clique com botão direito no projeto, clique em **publicar**. Na primeira vez que publica, terá de definir um perfil de publicação. Quando já tiver um perfil definido, pode selecioná-lo e clique em **publicar**.
3. Se lhe for pedido para selecionar um destino da publicação, clique em **serviço de aplicações do Microsoft Azure** > **seguinte**, em seguida, (se necessário) início de sessão com as suas credenciais do Azure.
   Downloads do Visual Studio e armazena com segurança suas definições diretamente a partir do Azure de publicação.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Escolha sua **subscrição**, selecione **tipo de recurso** partir **vista**, expanda **aplicação móvel**e clique em back-end da aplicação móvel, em seguida, clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Verifique as informações de perfil de publicação e clique em **publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Quando o back-end de aplicação móvel publicou com êxito, verá uma página de aterrissagem indicando êxito.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Como: Definir um controlador de tabela
Defina um controlador de tabela para expor uma tabela SQL para clientes móveis.  Configurar um Table Controller requer três passos:

1. Crie uma classe de objeto de transferência de dados (DTO).
2. Configure uma referência de tabela na classe Mobile DbContext.
3. Crie um controlador de tabela.

Um objeto de transferência de dados (DTO) é um simples objeto c# que herda de `EntityData`.  Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

O DTO é utilizado para definir a tabela na base de dados SQL.  Para criar a entrada de base de dados, adicione um `DbSet<>` propriedade para a DbContext que está a utilizar.  No modelo de projeto padrão para aplicações móveis do Azure, a DbContext é chamada `Models\MobileServiceContext.cs`:

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

Se tiver instalado o SDK do Azure, pode criar um controlador de tabela de modelo da seguinte forma:

1. Com o botão direito na pasta Controllers e selecione **Add** > **controlador...** .
2. Selecione o **controlador de tabela do Azure Mobile Apps** opção, em seguida, clique em **Add**.
3. Na **Add Controller** caixa de diálogo:
   * Na **classe de modelo** lista pendente, selecione o seu novo DTO.
   * Na **DbContext** lista pendente, selecione a classe DbContext do serviço de Mobile.
   * O nome do controlador é criado para.
4. Clique em **Adicionar**.

O projeto de servidor de início rápido contém um exemplo de um simples **TodoItemController**.

### <a name="adjust-pagesize"></a>Como: Ajustar o tamanho de paginação de tabela
Por predefinição, as aplicações móveis do Azure devolve 50 registos por pedido.  Paginação garante que o cliente não bloqueia o thread de interface do Usuário nem o servidor durante muito tempo, garantir uma boa experiência de utilizador. Para alterar o tamanho de paginação de tabela, aumentar o "tamanho de consulta permitido" do lado do servidor e do lado do servidor de tamanho de página do lado do cliente "tamanho de consulta permitido" é ajustado usando o `EnableQuery` atributo:

    [EnableQuery(PageSize = 500)]

Certifique-se de que o PageSize é igual ou maior do que o tamanho pedido pelo cliente.  Consulte o documentação de tutoriais sobre como para obter detalhes sobre como alterar o tamanho da página de cliente de cliente específico.

## <a name="how-to-define-a-custom-api-controller"></a>Como: Definir um controlador da API personalizado
O controlador da API personalizado fornece a funcionalidade mais básica para o back-end de aplicação móvel ao expor um ponto de extremidade. Pode registrar um controlador da API móveis específicas usando o atributo [MobileAppController]. O `MobileAppController` atributo registra a rota, configura o serializador JSON de aplicações móveis e ativa [a verificação de versão do cliente](app-service-mobile-client-and-server-versioning.md).

1. No Visual Studio, clique com o botão direito na pasta controladores, em seguida, clique em **Add** > **controlador**, selecione **Web API 2 Controller&mdash;vazio** e Clique em **adicionar**.
2. Fornecer um **nome do controlador**, tal como `CustomController`e clique em **Add**.
3. No novo arquivo de classe do controlador, adicione o seguinte usando a instrução:

        using Microsoft.Azure.Mobile.Server.Config;
4. Aplicar a **[MobileAppController]** atributo para a definição de classe de controlador de API, como no exemplo seguinte:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. No ficheiro de App_Start/Startup.MobileApp.cs, adicione uma chamada para o **MapApiControllers** método de extensão, como no exemplo seguinte:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Também pode utilizar o `UseDefaultConfiguration()` método de extensão em vez de `MapApiControllers()`. Qualquer controlador que não tenha **MobileAppControllerAttribute** aplicadas ainda podem ser acedidos por clientes, mas ele pode não ser corretamente consumido por clientes através de qualquer aplicação móvel de cliente SDK.

## <a name="how-to-work-with-authentication"></a>Como: Trabalhar com a autenticação
Aplicações móveis do Azure utiliza a autenticação de serviço de aplicação / autorização para proteger o seu back-end móvel.  Esta secção mostra como realizar as seguintes tarefas relacionadas com a autenticação no seu projeto de servidor de back-end do .NET:

* [How to: Adicionar autenticação a um projeto de servidor](#add-auth)
* [How to: Utilizar a autenticação personalizada para a sua aplicação](#custom-auth)
* [How to: Obter as informações de utilizador autenticado](#user-info)
* [How to: Restringir o acesso de dados para os utilizadores autorizados](#authorize)

### <a name="add-auth"></a>Como: Adicionar autenticação a um projeto de servidor
Pode adicionar autenticação ao seu projeto de servidor, estendendo o **MobileAppConfiguration** objeto e a configuração de middleware da OWIN. Ao instalar o [Microsoft.Azure.Mobile.Server.Quickstart] pacote e chamar os **UseDefaultConfiguration** método de extensão, pode avançar para o passo 3.

1. No Visual Studio, instalar o [Microsoft.Azure.Mobile.Server.Authentication] pacote.
2. No arquivo de projeto Startup.cs, adicione a seguinte linha de código no início da **configuração** método:

        app.UseAppServiceAuthentication(config);

    Este componente de middleware da OWIN valida tokens emitidos pelo gateway de serviço de aplicações associado.
3. Adicionar o `[Authorize]` qualquer método que requer autenticação ou controlador de atributo.

Para saber mais sobre como autenticar os clientes para o back-end de aplicações móveis, consulte [adicionar autenticação à sua aplicação](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como: Utilizar a autenticação personalizada para a sua aplicação
> [!IMPORTANT]
> Para ativar a autenticação personalizada, primeiro tem de ativar autenticação do serviço de aplicações sem selecionar um fornecedor para o serviço de aplicações no portal do Azure. Isso permitirá que a variável de ambiente de WEBSITE_AUTH_SIGNING_KEY quando hospedado.
> 
> 
> Se não pretender utilizar um dos fornecedores de autenticação/autorização do serviço da aplicação, pode implementar seu próprio sistema de início de sessão. Instalar o [Microsoft.Azure.Mobile.Server.Login] pacote para ajudar a geração de tokens de autenticação.  Fornece seu próprio código para validar as credenciais do utilizador. Por exemplo, pode verificar em relação a SALT e hash palavras-passe numa base de dados. No exemplo abaixo, o `isValidAssertion()` método (definido em outro lugar) é responsável por estas verificações.

A autenticação personalizada é exposta ao criar um ApiController e expor `register` e `login` ações. O cliente deve usar uma interface do Usuário personalizada para recolher as informações do usuário.  As informações, em seguida, foi submetidas para a API com uma chamada de POST de HTTP padrão. Assim que o servidor valida a asserção, é emitido um token com o `AppServiceLoginHandler.CreateToken()` método.  O ApiController **não deve** utilizar o `[MobileAppController]` atributo.

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

No exemplo anterior, LoginResult e LoginResultUser são serializáveis objetos que expõem propriedades necessárias. O cliente espera que as respostas de início de sessão serão retornados como objetos JSON do formulário:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

O `AppServiceLoginHandler.CreateToken()` método inclui uma *público-alvo* e um *emissor* parâmetro. Ambos os parâmetros são definidos para o URL de raiz do seu aplicativo, usando o esquema de HTTPS. Da mesma forma que deve definir *secretKey* para ser o valor da sua aplicação da chave de assinatura. Não distribua a chave de assinatura num cliente como ele pode ser usado para e menta chaves e representar os utilizadores. Pode obter a chave de assinatura enquanto alojado no serviço de aplicações, fazendo referência a *Web site\_AUTH\_assinatura\_chave* variável de ambiente. Se for necessário num contexto de depuração local, siga as instruções no [depuração Local com a autenticação](#local-debug) secção para obter a chave e armazená-lo como uma definição da aplicação.

O token emitido também pode incluir outras afirmações e uma data de expiração.  No mínimo, o token emitido tem de incluir um assunto (**sub**) de afirmação.

Pode oferecer suporte a standard client `loginAsync()` método ao sobrecarregar a rota de autenticação.  Se o cliente chama `client.loginAsync('custom');` para iniciar sessão, a rota tem de ser `/.auth/login/custom`.  Pode definir a rota para o controlador de autenticação personalizado utilizando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Usando o `loginAsync()` abordagem garante que o token de autenticação está ligado a todas as chamadas subsequentes ao serviço.
>
>

### <a name="user-info"></a>Como: Obter as informações de utilizador autenticado
Quando um utilizador é autenticado pelo serviço de aplicações, pode aceder o ID de utilizador atribuído e outras informações no seu código de back-end de .NET. As informações de utilizador podem ser utilizadas para tomar decisões de autorização no back-end. O código seguinte obtém o ID de utilizador associado um pedido:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

O SID é derivado do ID de utilizador específica do fornecedor e é estático para um determinado usuário e o fornecedor de início de sessão.  O SID é nulo para tokens de autenticação inválido.

Também o serviço de aplicações permite-lhe pedir afirmações específicas do fornecedor de início de sessão. Cada fornecedor de identidade pode fornecer mais informações utilizando o SDK do fornecedor de identidade.  Por exemplo, pode utilizar a Graph API do Facebook para informações de amigos.  Pode especificar as afirmações que são solicitadas no painel do fornecedor no portal do Azure. Algumas afirmações exigem configuração adicional com o fornecedor de identidade.

O seguinte código chamadas a **GetAppServiceIdentityAsync** método de extensão para obter as credenciais de início de sessão, que incluem o acesso ao token necessário para fazer pedidos com o Graph API do Facebook:

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

Adicionar uma instrução para `System.Security.Principal` para fornecer a **GetAppServiceIdentityAsync** método de extensão.

### <a name="authorize"></a>Como: Restringir o acesso de dados para os utilizadores autorizados
Na secção anterior, mostramos como obter o ID de utilizador de um usuário autenticado. Pode restringir o acesso a dados e outros recursos com base nesse valor. Por exemplo, adicionar uma coluna de ID de utilizador a tabelas e filtrar os resultados da consulta por ID de utilizador são uma forma simples de limitar dados retornados apenas aos utilizadores autorizados. O código a seguir retorna linhas de dados apenas quando o SID corresponde ao valor na coluna UserId na tabela TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

O `Query()` método devolve um `IQueryable` que pode ser manipulado pelo LINQ para processar a filtragem.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Como: Adicionar notificações push para um projeto de servidor
Adicionar notificações push para seu projeto de servidor ao expandir a **MobileAppConfiguration** objeto e a criação de um cliente de Hubs de notificação.

1. No Visual Studio, o projeto de servidor com o botão direito e clique em **gerir pacotes NuGet**, procure `Microsoft.Azure.Mobile.Server.Notifications`, em seguida, clique em **instalar**.
2. Repita este passo para instalar o `Microsoft.Azure.NotificationHubs` pacote, que inclui a biblioteca de cliente dos Hubs de notificação.
3. No App_Start/Startup.MobileApp.cs e adicione uma chamada para o **AddPushNotifications()** método de extensão durante a inicialização:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adicione o seguinte código que cria um cliente de Hubs de notificação:

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

Agora, pode utilizar o cliente dos Hubs de notificação para enviar notificações push para dispositivos registados. Para obter mais informações, consulte [adicionar notificações push à sua aplicação](app-service-mobile-ios-get-started-push.md). Para saber mais sobre os Hubs de notificação, veja [descrição geral dos Hubs de notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Como: Ative o push direcionada utilizando etiquetas
Os Hubs de notificação permite-lhe enviar notificações direcionadas para registos específicos utilizando etiquetas. As várias etiquetas são criadas automaticamente:

* O ID de instalação identifica um dispositivo específico.
* O Id de utilizador com base no SID autenticado identifica um utilizador específico.

A instalação ID pode ser acedido a partir da **installationId** propriedade no **MobileServiceClient**.  O exemplo seguinte mostra como utilizar um ID de instalação para adicionar uma etiqueta para uma instalação específica nos Hubs de notificação:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Todas as marcas fornecidas pelo cliente durante o registo de notificação push são ignoradas pelo back-end ao criar a instalação. Para ativar um cliente adicionar etiquetas para a instalação, tem de criar uma API personalizada que adiciona marcas usando o padrão anterior.

Ver [etiquetas de notificação push de cliente adicionasse] [ 5] no exemplo de início rápido concluído Mobile Apps do App Service para obter um exemplo.

## <a name="push-user"></a>Como: Enviar notificações push para um utilizador autenticado
Quando um usuário autenticado registra para notificações push, uma etiqueta de ID de utilizador é automaticamente adicionada ao registo. Ao utilizar esta etiqueta, pode enviar notificações push para todos os dispositivos registados por essa pessoa. O código seguinte obtém o SID do utilizador que efetua o pedido e envia uma notificação push de modelo para cada registo de dispositivos para essa pessoa:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Ao registar as notificações push a partir de um cliente autenticado, certifique-se de que a autenticação é concluída antes de tentar o registo. Para obter mais informações, consulte [Push para utilizadores] [ 6] no exemplo de início rápido concluído Mobile Apps do App Service para o back-end .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Como: Depurar e resolver problemas relacionados com o .NET Server SDK
Serviço de aplicações do Azure fornece vários depuração e resolução de problemas de técnicas para aplicativos ASP.NET:

* [Monitorização de um serviço de aplicações do Azure](../app-service/web-sites-monitor.md)
* [Ativar o registo de diagnóstico no serviço de aplicações do Azure](../app-service/troubleshoot-diagnostic-logs.md)
* [Resolver problemas de um serviço de aplicações do Azure no Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Registo
Pode escrever os registos de diagnóstico do serviço de aplicações ao utilizar a escrita de rastreamento do ASP.NET padrão. Antes de poder escrever nos registos, tem de ativar o diagnóstico no seu back-end de aplicação móvel.

Para ativar os diagnósticos e escrever nos registos:

1. Siga os passos em [como ativar o diagnóstico](../app-service/troubleshoot-diagnostic-logs.md#enablediag).
2. Adicione o seguinte usando a instrução em seu arquivo de código:

        using System.Web.Http.Tracing;
3. Crie um gravador de rastreamento para gravar do back-end do .NET para os registos de diagnóstico, da seguinte forma:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Voltar a publicar o projeto de servidor e o back-end de aplicação móvel para executar o caminho do código com o registo de acesso.
5. Baixe e avalie os registos, conforme descrito em [como: Transferir registos](../app-service/troubleshoot-diagnostic-logs.md#download).

### <a name="local-debug"></a>Depuração com a autenticação local
Pode executar a aplicação localmente para testar as alterações antes de publicá-los para a cloud. A maioria dos back-ends de aplicações móveis do Azure, prima *F5* enquanto estiver no Visual Studio. No entanto, existem algumas considerações adicionais ao utilizar a autenticação.

Tem de ter uma aplicação móvel com base na cloud com o serviço de autenticação/autorização da aplicação configurada e o cliente tem de ter o ponto final da cloud especificado como o anfitrião de início de sessão alternativo. Consulte a documentação para a sua plataforma de cliente para os passos específicos necessários.

Certifique-se de que o seu back-end móvel possui [Microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, na classe de startup da OWIN da sua aplicação, adicione o seguinte, depois `MobileAppConfiguration` foi aplicada ao seu `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

No exemplo anterior, deve configurar o *authAudience* e *authIssuer* as definições da aplicação na Web. config de ficheiros para cada ser o URL de raiz do seu aplicativo, usando o esquema de HTTPS. Da mesma forma que deve definir *authSigningKey* para ser o valor da sua aplicação da chave de assinatura.
Para obter a chave de assinatura:

1. Navegue para a aplicação dentro do [portal do Azure]
2. Clique em **ferramentas**, **Kudu**, **ir**.
3. No site de gestão do Kudu, clique em **ambiente**.
4. Localize o valor para *Web site\_AUTH\_assinatura\_chave*.

Utilizar a chave de assinatura para o *authSigningKey* parâmetro na sua configuração de aplicação local.  Seu back-end móvel está agora preparado para validar os tokens ao executar localmente, o cliente obtém o token do ponto final com base na cloud.

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
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
