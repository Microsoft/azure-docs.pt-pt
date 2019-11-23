---
title: Como trabalhar com o SDK do servidor de back-end do .NET para aplicativos móveis | Microsoft Docs
description: Saiba como trabalhar com o SDK do servidor de back-end do .NET para aplicativos móveis do serviço Azure App.
keywords: serviço de aplicativo, serviço de aplicativo do Azure, aplicativo móvel, serviço móvel, escala, escalonável, implantação de aplicativo, implantação de aplicativo do Azure
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
ms.openlocfilehash: 5372385bd4de0f5592a7fff3e4a78fbb5e1648dc
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388780"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabalhe com o SDK do servidor de back-end .NET para Aplicações Móveis do Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

Este tópico mostra como usar o SDK do servidor de back-end do .NET em cenários de aplicativos móveis do Key Azure App Service. O SDK de aplicativos móveis do Azure ajuda você a trabalhar com clientes móveis do seu aplicativo ASP.NET.

> [!TIP]
> O [SDK do servidor .net para aplicativos móveis do Azure][2] é código-fonte aberto no github. O repositório contém todo o código-fonte, incluindo todo o conjunto de testes de unidade do SDK do servidor e alguns projetos de exemplo.
>
>

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o SDK do servidor está localizada aqui: [referência do .net de aplicativos móveis do Azure][1].

## <a name="create-app"></a>Como: criar um back-end de aplicativo móvel do .NET
Se você estiver iniciando um novo projeto, poderá criar um aplicativo do serviço de aplicativo usando o [portal do Azure] ou o Visual Studio. Você pode executar o aplicativo do serviço de aplicativo localmente ou publicar o projeto em seu aplicativo móvel do serviço de aplicativo baseado em nuvem.

Se você estiver adicionando recursos móveis a um projeto existente, consulte a seção [baixar e inicializar o SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Criar um back-end .NET usando o portal do Azure
Para criar um back-end móvel do serviço de aplicativo, siga o [tutorial de início rápido][3] ou siga estas etapas:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

*De volta à folha introdução* , em **criar uma API de tabela**, escolha **C#** como seu **idioma de back-end**. Clique em **baixar**, extraia os arquivos de projeto compactados para o computador local e abra a solução no Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Criar um back-end .NET usando o Visual Studio 2017

Instale a carga de trabalho do Azure por meio do Instalador do Visual Studio para publicar no projeto de aplicativos móveis do Azure por meio do Visual Studio. Depois de instalar o SDK, crie um aplicativo ASP.NET usando as seguintes etapas:

1. Abra a caixa de diálogo **novo projeto** (de **arquivo** > **novo** > **projeto...** ).
2. Expanda **Visual C#**  e selecione **Web**.
3. Selecione **aplicativo Web ASP.net (.NET Framework)** .
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Selecione **aplicativo móvel do Azure** na lista de modelos.
6. Clique em **OK** para criar a solução.
7. Clique com o botão direito do mouse no projeto na **Gerenciador de soluções** e escolha **publicar...** e, em seguida, escolha **serviço de aplicativo** como o destino de publicação.
8. Siga os prompts para autenticar e escolher um serviço de Azure App novo ou existente para publicar.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Criar um back-end .NET usando o Visual Studio 2015

Instale o [SDK do Azure para .net][4] (versão 2.9.0 ou posterior) para criar um projeto de aplicativos móveis do Azure no Visual Studio. Depois de instalar o SDK, crie um aplicativo ASP.NET usando as seguintes etapas:

1. Abra a caixa de diálogo **novo projeto** (de **arquivo** > **novo** > **projeto...** ).
2. Expanda **modelos** > **Visual C#** e selecione **Web**.
3. Selecione **Aplicação Web ASP.NET**.
4. Preencha o nome do projeto. Em seguida, clique em **OK**.
5. Em *modelos de ASP.NET 4.5.2*, selecione **aplicativo móvel do Azure**. Verifique o **host na nuvem** para criar um back-end móvel na nuvem na qual você pode publicar este projeto.
6. Clique em **OK**.

## <a name="install-sdk"></a>Como: baixar e inicializar o SDK
O SDK está disponível em [NuGet.org]. Esse pacote inclui a funcionalidade básica necessária para começar a usar o SDK. Para inicializar o SDK, você precisa executar ações no objeto **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalar o SDK
Para instalar o SDK, clique com o botão direito do mouse no projeto do servidor no Visual Studio, selecione **gerenciar pacotes NuGet**, procure o pacote [Microsoft.Azure.Mobile.Server] e clique em **instalar**.

### <a name="server-project-setup"></a>Inicializar o projeto do servidor
Um projeto de servidor de back-end do .NET é inicializado de forma semelhante a outros projetos do ASP.NET, incluindo uma classe de inicialização OWIN. Verifique se você referenciou o pacote NuGet `Microsoft.Owin.Host.SystemWeb`. Para adicionar essa classe no Visual Studio, clique com o botão direito do mouse no projeto do servidor e selecione **adicionar** >
**novo item**e, em seguida, **Web** > **geral** > **classe de inicialização OWIN**.  Uma classe é gerada com o seguinte atributo:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

No método `Configuration()` da sua classe de inicialização OWIN, use um objeto **HttpConfiguration** para configurar o ambiente de aplicativos móveis do Azure.
O exemplo a seguir inicializa o projeto do servidor sem recursos adicionados:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para habilitar recursos individuais, você deve chamar métodos de extensão no objeto **MobileAppConfiguration** antes de chamar **ApplyTo**. Por exemplo, o código a seguir adiciona as rotas padrão a todos os controladores de API que têm o atributo `[MobileAppController]` durante a inicialização:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

O início rápido do servidor da portal do Azure chama **UseDefaultConfiguration ()** . Isso equivale à seguinte configuração:

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

* `AddMobileAppHomeController()` fornece os aplicativos móveis do Azure padrão home page.
* `MapApiControllers()` fornece recursos de API personalizados para controladores WebAPI decorados com o atributo `[MobileAppController]`.
* `AddTables()` fornece um mapeamento dos pontos de extremidade de `/tables` para os controladores de tabela.
* `AddTablesWithEntityFramework()` é uma pequena mão para mapear os pontos de extremidade de `/tables` usando controladores baseados em Entity Framework.
* `AddPushNotifications()` fornece um método simples de registrar dispositivos para hubs de notificação.
* `MapLegacyCrossDomainController()` fornece cabeçalhos CORS padrão para o desenvolvimento local.

### <a name="sdk-extensions"></a>Extensões do SDK
Os pacotes de extensão baseados em NuGet a seguir fornecem vários recursos móveis que podem ser usados pelo seu aplicativo. Você habilita extensões durante a inicialização usando o objeto **MobileAppConfiguration** .

* [Microsoft.Azure.Mobile.Server.Quickstart] dá suporte à configuração básica de aplicativos móveis. Adicionado à configuração chamando o método de extensão **UseDefaultConfiguration** durante a inicialização. Essa extensão inclui as seguintes extensões: notificações, autenticação, entidade, tabelas, vários domínios e pacotes residenciais. Este pacote é usado pelo início rápido dos aplicativos móveis disponível no portal do Azure.
* [Microsoft. Azure. Mobile. Server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa a *página padrão este aplicativo móvel está em execução* para a raiz do site. Adicione à configuração chamando o método de extensão **AddMobileAppHomeController** .
* [Microsoft. Azure. Mobile. Server. Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) inclui classes para trabalhar com dados e define o pipeline de dados. Adicione à configuração chamando o método de extensão **AddTables** .
* [Microsoft. Azure. Mobile. Server. Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite que o Entity Framework acesse os dados no SQL Database. Adicione à configuração chamando o método de extensão **AddTablesWithEntityFramework** .
* [Microsoft.Azure.Mobile.Server.Authentication] habilita a autenticação e define o middleware OWIN usado para validar tokens. Adicione à configuração chamando **os** e **IAppBuilder**. Métodos de extensão **UseAppServiceAuthentication** .
* [Microsoft.Azure.Mobile.Server.Notifications] habilita notificações por push e define um ponto de extremidade de registro Push. Adicione à configuração chamando o método de extensão **AddPushNotifications** .
* [Microsoft. Azure. Mobile. Server. crossdomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) cria um controlador que serve dados para navegadores da Web herdados de seu aplicativo móvel. Adicione à configuração chamando o método de extensão **MapLegacyCrossDomainController** .
* [Microsoft.Azure.Mobile.Server.Login] fornece o método AppServiceLoginHandler. createtoken (), que é um método estático usado durante cenários de autenticação personalizada.

## <a name="publish-server-project"></a>Como publicar o projeto do servidor
Esta seção mostra como publicar seu projeto de back-end .NET do Visual Studio. Você também pode implantar seu projeto de back-end usando o [git](../app-service/deploy-local-git.md) ou qualquer um dos outros métodos disponíveis ali.

1. No Visual Studio, recompile o projeto para restaurar os pacotes NuGet.
2. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **publicar**. Na primeira vez que você publicar, precisará definir um perfil de publicação. Quando você já tiver um perfil definido, poderá selecioná-lo e clicar em **publicar**.
3. Se solicitado a selecionar um destino de publicação, clique em **Microsoft Azure serviço de aplicativo** > **Avançar**e, em seguida, (se necessário) entre com suas credenciais do Azure.
   O Visual Studio baixa e armazena com segurança suas configurações de publicação diretamente do Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Escolha sua **assinatura**, selecione **tipo de recurso** na **exibição**, expanda **aplicativo móvel**e clique no back-end do aplicativo móvel e clique em **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Verifique as informações do perfil de publicação e clique em **publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Quando o back-end do aplicativo móvel tiver sido publicado com êxito, você verá uma página de aterrissagem indicando êxito.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Como definir um controlador de tabela
Defina um controlador de tabela para expor uma tabela SQL para clientes móveis.  A configuração de um controlador de tabela requer três etapas:

1. Crie uma classe de DTO (objeto de Transferência de Dados).
2. Configure uma referência de tabela na classe DbContext móvel.
3. Crie um controlador de tabela.

Um objeto de Transferência de Dados (DTO) é um C# objeto simples que herda de `EntityData`.  Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

O DTO é usado para definir a tabela no banco de dados SQL.  Para criar a entrada de banco de dados, adicione uma propriedade `DbSet<>` ao DbContext que você está usando.  No modelo de projeto padrão para aplicativos móveis do Azure, o DbContext é chamado de `Models\MobileServiceContext.cs`:

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

Se você tiver o SDK do Azure instalado, agora você poderá criar um controlador de tabela de modelo da seguinte maneira:

1. Clique com o botão direito do mouse na pasta controladores e selecione **adicionar** > **controlador...** .
2. Selecione a opção **controlador de tabela de aplicativos móveis do Azure** e clique em **Adicionar**.
3. Na caixa de diálogo **Adicionar controlador** :
   * Na lista suspensa **classe de modelo** , selecione seu novo dto.
   * Na lista suspensa **DbContext** , selecione a classe DbContext do serviço móvel.
   * O nome do controlador é criado para você.
4. Clique em **Adicionar**.

O projeto de servidor de início rápido contém um exemplo para um **TodoItemController**simples.

### <a name="adjust-pagesize"></a>Como ajustar o tamanho de paginação da tabela
Por padrão, os aplicativos móveis do Azure retornam 50 registros por solicitação.  A paginação garante que o cliente não vincule seu thread de interface do usuário nem o servidor por muito tempo, garantindo uma boa experiência do usuário. Para alterar o tamanho da paginação da tabela, aumente o "tamanho de consulta permitido" do lado do servidor e o tamanho da página do lado do cliente do "tamanho de consulta permitido" do lado do servidor é ajustado usando o atributo `EnableQuery`:

    [EnableQuery(PageSize = 500)]

Verifique se PageSize é igual ou maior que o tamanho solicitado pelo cliente.  Consulte a documentação específica do cliente HOWTO para obter detalhes sobre como alterar o tamanho da página do cliente.

## <a name="how-to-define-a-custom-api-controller"></a>Como definir um controlador de API personalizado
O controlador de API personalizado fornece a funcionalidade mais básica para o back-end do aplicativo móvel expondo um ponto de extremidade. Você pode registrar um controlador de API específico do celular usando o atributo [MobileAppController]. O atributo `MobileAppController` registra a rota, configura o serializador JSON dos aplicativos móveis e ativa a [verificação de versão do cliente](app-service-mobile-client-and-server-versioning.md).

1. No Visual Studio, clique com o botão direito do mouse na pasta controladores, clique em **adicionar** > **controlador**, selecione **controlador Web API 2&mdash;vazio** e clique em **Adicionar**.
2. Forneça um **nome de controlador**, como `CustomController`, e clique em **Adicionar**.
3. No novo arquivo de classe de controlador, adicione a seguinte instrução Using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Aplique o atributo **[MobileAppController]** à definição de classe do controlador de API, como no exemplo a seguir:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. No arquivo App_Start/Startup.MobileApp.cs, adicione uma chamada para o método de extensão **MapApiControllers** , como no exemplo a seguir:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Você também pode usar o método de extensão `UseDefaultConfiguration()` em vez de `MapApiControllers()`. Qualquer controlador que não tenha **MobileAppControllerAttribute** aplicado ainda pode ser acessado por clientes, mas pode não ser consumido corretamente por clientes usando qualquer SDK de cliente de aplicativo móvel.

## <a name="how-to-work-with-authentication"></a>Como trabalhar com autenticação
Os aplicativos móveis do Azure usam a autenticação/autorização do serviço de aplicativo para proteger seu back-end móvel.  Esta seção mostra como executar as seguintes tarefas relacionadas à autenticação em seu projeto de servidor de back-end do .NET:

* [Como: Adicionar autenticação a um projeto de servidor](#add-auth)
* [Como: usar a autenticação personalizada para seu aplicativo](#custom-auth)
* [Como recuperar informações de usuário autenticado](#user-info)
* [Como restringir o acesso a dados para usuários autorizados](#authorize)

### <a name="add-auth"></a>Como: Adicionar autenticação a um projeto de servidor
Você pode adicionar autenticação ao seu projeto de servidor estendendo o objeto **MobileAppConfiguration** e Configurando o middleware OWIN. Ao instalar o pacote [Microsoft.Azure.Mobile.Server.Quickstart] e chamar o método de extensão **UseDefaultConfiguration** , você pode pular para a etapa 3.

1. No Visual Studio, instale o pacote [Microsoft.Azure.Mobile.Server.Authentication] .
2. No arquivo de projeto Startup.cs, adicione a seguinte linha de código no início do método de **configuração** :

        app.UseAppServiceAuthentication(config);

    Este componente de middleware OWIN valida tokens emitidos pelo gateway do serviço de aplicativo associado.
3. Adicione o atributo `[Authorize]` a qualquer controlador ou método que exija autenticação.

Para saber mais sobre como autenticar clientes para o back-end de aplicativos móveis, consulte [Adicionar autenticação ao seu aplicativo](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Como: usar a autenticação personalizada para seu aplicativo
> [!IMPORTANT]
> Para habilitar a autenticação personalizada, primeiro você deve habilitar a autenticação do serviço de aplicativo sem selecionar um provedor para o serviço de aplicativo no portal do Azure. Isso habilitará a variável de ambiente WEBSITE_AUTH_SIGNING_KEY quando hospedado.
> 
> 
> Se você não quiser usar um dos provedores de autenticação/autorização do serviço de aplicativo, poderá implementar seu próprio sistema de logon. Instale o pacote [Microsoft.Azure.Mobile.Server.Login] para auxiliar na geração de tokens de autenticação.  Forneça seu próprio código para validar as credenciais do usuário. Por exemplo, você pode verificar contra senhas com Salt e hash em um banco de dados. No exemplo a seguir, o método `isValidAssertion()` (definido em outro lugar) é responsável por essas verificações.

A autenticação personalizada é exposta criando um ApiController e expondo `register` e `login` ações. O cliente deve usar uma interface de usuário personalizada para coletar as informações do usuário.  As informações são então enviadas para a API com uma chamada HTTP POST padrão. Depois que o servidor valida a asserção, um token é emitido usando o método `AppServiceLoginHandler.CreateToken()`.  O ApiController **não deve** usar o atributo `[MobileAppController]`.

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

No exemplo anterior, LoginResult e LoginResultUser são objetos serializáveis expondo as propriedades necessárias. O cliente espera que as respostas de logon sejam retornadas como objetos JSON no formato:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

O método `AppServiceLoginHandler.CreateToken()` inclui um *público* e um parâmetro de *emissor* . Ambos os parâmetros são definidos como a URL da raiz do aplicativo, usando o esquema HTTPS. Da mesma forma, você deve definir *secretKey* para ser o valor da chave de assinatura do seu aplicativo. Não distribua a chave de assinatura em um cliente, pois ela pode ser usada para mentar chaves e representar usuários. Você pode obter a chave de assinatura enquanto estiver hospedada no serviço de aplicativo referenciando o *site\_autenticação\_assinatura\_* variável de ambiente de chave. Se necessário em um contexto de depuração local, siga as instruções na seção [depuração local com autenticação](#local-debug) para recuperar a chave e armazená-la como uma configuração de aplicativo.

O token emitido também pode incluir outras declarações e uma data de expiração.  No mínimo, o token emitido deve incluir uma declaração de Subject (**sub**).

Você pode dar suporte ao método de `loginAsync()` do cliente padrão sobrecarregando a rota de autenticação.  Se o cliente chamar `client.loginAsync('custom');` para fazer logon, sua rota deverá ser `/.auth/login/custom`.  Você pode definir a rota para o controlador de autenticação personalizada usando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Usar a abordagem `loginAsync()` garante que o token de autenticação seja anexado a todas as chamadas subsequentes para o serviço.
>
>

### <a name="user-info"></a>Como recuperar informações de usuário autenticado
Quando um usuário é autenticado pelo serviço de aplicativo, você pode acessar a ID de usuário atribuída e outras informações no código de back-end do .NET. As informações do usuário podem ser usadas para tomar decisões de autorização no back-end. O código a seguir obtém a ID de usuário associada a uma solicitação:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

O SID é derivado da ID de usuário específica do provedor e é estático para um determinado usuário e provedor de logon.  O SID é nulo para tokens de autenticação inválidos.

O serviço de aplicativo também permite que você solicite declarações específicas do seu provedor de logon. Cada provedor de identidade pode fornecer mais informações usando o SDK do provedor de identidade.  Por exemplo, você pode usar as informações de API do Graph do Facebook para amigos.  Você pode especificar as declarações que são solicitadas na folha do provedor no portal do Azure. Algumas declarações exigem configuração adicional com o provedor de identidade.

O código a seguir chama o método de extensão **GetAppServiceIdentityAsync** para obter as credenciais de logon, que incluem o token de acesso necessário para fazer solicitações no API do Graph do Facebook:

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

Adicione uma instrução using para `System.Security.Principal` para fornecer o método de extensão **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Como restringir o acesso a dados para usuários autorizados
Na seção anterior, mostramos como recuperar a ID de usuário de um usuário autenticado. Você pode restringir o acesso a dados e a outros recursos com base nesse valor. Por exemplo, adicionar uma coluna userId a tabelas e filtrar os resultados da consulta pela ID de usuário é uma maneira simples de limitar os dados retornados somente aos usuários autorizados. O código a seguir retorna linhas de dados somente quando o SID corresponde ao valor na coluna UserId na tabela TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

O método `Query()` retorna um `IQueryable` que pode ser manipulado pelo LINQ para manipular a filtragem.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Como: Adicionar notificações por push a um projeto de servidor
Adicione notificações por push ao seu projeto de servidor estendendo o objeto **MobileAppConfiguration** e criando um cliente de hubs de notificação.

1. No Visual Studio, clique com o botão direito do mouse no projeto do servidor e clique em **gerenciar pacotes NuGet**, procure `Microsoft.Azure.Mobile.Server.Notifications`e clique em **instalar**.
2. Repita essa etapa para instalar o pacote de `Microsoft.Azure.NotificationHubs`, que inclui a biblioteca de cliente dos hubs de notificação.
3. Em App_Start/Startup.MobileApp.cs e adicione uma chamada para o método de extensão **AddPushNotifications ()** durante a inicialização:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Adicione o seguinte código que cria um cliente de hubs de notificação:

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

Agora você pode usar o cliente de hubs de notificação para enviar notificações por push para dispositivos registrados. Para obter mais informações, consulte [Adicionar notificações por push ao seu aplicativo](app-service-mobile-ios-get-started-push.md). Para saber mais sobre os hubs de notificação, consulte [visão geral dos hubs de notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Como habilitar o envio por push direcionado usando marcas
Os hubs de notificação permitem que você envie notificações direcionadas para registros específicos usando marcas. Várias marcas são criadas automaticamente:

* A ID de instalação identifica um dispositivo específico.
* A ID de usuário com base no SID autenticado identifica um usuário específico.

A ID de instalação pode ser acessada da propriedade **InstallationID** no **MobileServiceClient**.  O exemplo a seguir mostra como usar uma ID de instalação para adicionar uma marca a uma instalação específica nos hubs de notificação:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Todas as marcas fornecidas pelo cliente durante o registro de notificação por push são ignoradas pelo back-end ao criar a instalação. Para permitir que um cliente Adicione marcas à instalação, você deve criar uma API personalizada que adiciona marcas usando o padrão anterior.

Confira as [marcas de notificação por push adicionadas pelo cliente][5] na amostra de início rápido de aplicativos móveis do serviço de aplicativo para obter um exemplo.

## <a name="push-user"></a>Como: enviar notificações por push para um usuário autenticado
Quando um usuário autenticado se registra para notificações por push, uma marca de ID de usuário é adicionada automaticamente ao registro. Usando essa marca, você pode enviar notificações por push para todos os dispositivos registrados por essa pessoa. O código a seguir obtém o SID do usuário que faz a solicitação e envia uma notificação por push de modelo para cada registro de dispositivo para essa pessoa:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Ao se registrar para notificações por push de um cliente autenticado, certifique-se de que a autenticação esteja concluída antes de tentar o registro. Para obter mais informações, consulte [enviar por push para os usuários][6] no serviço de aplicativo aplicativos móveis exemplo de início rápido para o back-end do .net.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Como: Depurar e solucionar problemas do SDK do servidor .NET
Azure App serviço fornece várias técnicas de depuração e solução de problemas para aplicativos ASP.NET:

* [Monitorando um serviço de Azure App](../app-service/web-sites-monitor.md)
* [Habilitar o log de diagnóstico no serviço Azure App](../app-service/troubleshoot-diagnostic-logs.md)
* [Solucionar problemas de um serviço de Azure App no Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Registo
Você pode gravar nos logs de diagnóstico do serviço de aplicativo usando a gravação de rastreamento ASP.NET padrão. Antes de poder gravar nos logs, você deve habilitar o diagnóstico no back-end do aplicativo móvel.

Para habilitar o diagnóstico e gravar nos logs:

1. Siga as etapas em [habilitar o log de aplicativo (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Adicione a seguinte instrução using em seu arquivo de código:

        using System.Web.Http.Tracing;
3. Crie um gravador de rastreamento para gravar do back-end do .NET para os logs de diagnóstico, da seguinte maneira:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Republique o projeto do servidor e acesse o back-end do aplicativo móvel para executar o caminho do código com o log.
5. Baixe e avalie os logs, conforme descrito em [acessar arquivos de log](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debug"></a>Depuração local com autenticação
Você pode executar o aplicativo localmente para testar as alterações antes de publicá-las na nuvem. Para a maioria dos back-ends dos aplicativos móveis do Azure, pressione *F5* enquanto estiver no Visual Studio. No entanto, há algumas considerações adicionais ao usar a autenticação do.

Você deve ter um aplicativo móvel baseado em nuvem com autenticação/autorização do serviço de aplicativo configurado e seu cliente deve ter o ponto de extremidade de nuvem especificado como o host de logon alternativo. Consulte a documentação da sua plataforma cliente para obter as etapas específicas necessárias.

Verifique se o back-end móvel tem [Microsoft.Azure.Mobile.Server.Authentication] instalado. Em seguida, na classe de inicialização OWIN do seu aplicativo, adicione o seguinte, depois que `MobileAppConfiguration` tiver sido aplicado ao seu `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

No exemplo anterior, você deve definir as configurações do aplicativo *as* e *AuthIssuer* no arquivo Web. config para cada URL da raiz do aplicativo, usando o esquema HTTPS. Da mesma forma, você deve definir *authSigningKey* para ser o valor da chave de assinatura do seu aplicativo.
Para obter a chave de assinatura:

1. Navegue até seu aplicativo dentro do [portal do Azure]
2. Clique em **ferramentas**, **kudu**, **go**.
3. No site de gerenciamento do kudu, clique em **ambiente**.
4. Localize o valor do *site\_AUTH\_assinatura\_chave*.

Use a chave de assinatura para o parâmetro *authSigningKey* na configuração do aplicativo local.  Seu back-end móvel agora está equipado para validar tokens ao executar localmente, que o cliente obtém o token do ponto de extremidade baseado em nuvem.

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
