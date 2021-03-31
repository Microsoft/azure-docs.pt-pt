---
title: 'Quickstart: ASP.NET web web do Core que assina nos utilizadores e chama o Microsoft Graph | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação aproveita o Microsoft.Identity.Web para implementar o início de sôs-in da Microsoft numa aplicação web core ASP.NET utilizando o OpenID Connect e chama o Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: efa9465adc13b50e6ae12628d21347152c3fc2c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578724"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Quickstart: ASP.NET web web do Core que assina nos utilizadores e chama o Microsoft Graph em seu nome

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação web core ASP.NET pode assinar em utilizadores de qualquer organização do Azure Ative Directory (Azure AD) e chama o Microsoft Graph.  

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Código de Estúdio Visual](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registe-se e descarregue a app quickstart
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `AspNetCoreWebAppCallsGraph-Quickstart` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Introduza um **URI de redirecionamento** de `https://localhost:44321/signin-oidc` .
> 1. Selecione **Registar**.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. Introduza um **URL de logout** de canal frontal de `https://localhost:44321/signout-oidc` .
> 1. Selecione **Guardar**.
> 1. Under **Manage**, selecione **Certificados & segredos**  >  **Novo segredo do cliente**.
> 1. Introduza uma **Descrição,** por exemplo `clientsecret1` .
> 1. Selecione **em 1 ano** para a expiração do segredo.
> 1. **Selecione Adicionar** e grave imediatamente o **valor** do segredo para ser usado num passo posterior. O valor secreto *nunca mais* é exibido e é irremediável por qualquer outro meio. Grave-o num local seguro como qualquer senha.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código neste arranque rápido funcione, adicione um URL de **logótipo de redirecionamento URI** `https://localhost:44321/signin-oidc` e de canal **frontal** no registo `https://localhost:44321/signout-oidc` da aplicação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-aspnet-core-project"></a>Passo 2: Descarregue o projeto ASP.NET Core

> [!div renderon="docs"]
> [Descarregue a solução ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para funcionar.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Passo 3: Configurar o seu projeto ASP.NET Core
> 1. Extraia o arquivo .zip numa pasta local perto da raiz da sua unidade. Por exemplo, em *C:\Azure-Samples*.
> 1. Abra a solução no Visual Studio 2019.
> 1. Abra a *appsettings.jsno* ficheiro e modifique o seguinte:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Substitua `Enter_the_Application_Id_here` pelo ID de **Aplicação (cliente)** da aplicação que registou no portal Azure. Pode encontrar **iD de aplicação (cliente)** na **página** geral da aplicação.
>    - Substitua `common` por uma das seguintes:
>       - Se a sua candidatura apoiar **apenas neste diretório organizacional,** substitua este valor pelo **ID do Diretório (inquilino)** (um **GUIADO)** ou nome de inquilino (por exemplo, `contoso.onmicrosoft.com` ). Pode encontrar o ID do **Diretório (inquilino)** na **página** geral da aplicação.
>       - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>       - Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** deixe este valor como `common`
>    - `Enter_the_Client_Secret_Here`Substitua-se pelo **segredo** do Cliente que criou e gravou num passo anterior.
> 
> Para este arranque rápido, não altere quaisquer outros valores no *appsettings.jsno* ficheiro.
>
> #### <a name="step-4-build-and-run-the-application"></a>Passo 4: compilar e executar a aplicação
>
> Construa e execute a aplicação no Visual Studio selecionando o menu **Debug** > **Start Debugging**, ou premindo a `F5` chave.
>
> É solicitado para as suas credenciais, e depois solicitado para consentir com as permissões que a sua aplicação requer. **Selecione Aceite** na solicitação de consentimento.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Diálogo de consentimento mostrando as permissões que a aplicação está solicitando do utilizador >":::
>
> Depois de consentir com as permissões solicitadas, a aplicação apresenta que fez login com sucesso usando as suas credenciais de Diretório Ativo Azure, e verá o seu endereço de e-mail na secção "Resultado Api" da página. Isto foi extraído usando o Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Web browser exibindo a aplicação web em execução e o utilizador assinou em":::

## <a name="about-the-code"></a>Sobre o código

Esta secção fornece uma visão geral do código necessário para iniciar seduções nos utilizadores e ligar para a API do Microsoft Graph em seu nome. Esta visão geral pode ser útil para entender como o código funciona, principais argumentos, e também se você quiser adicionar o início de sôr-in a uma aplicação core ASP.NET existente e ligar para o Microsoft Graph. Utiliza [o Microsoft.Identity.Web,](microsoft-identity-web.md)que é um invólucro em torno [MSAL.NET](msal-overview.md).

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de arranque

O middleware *Microsoft.AspNetCore.Authentication* utiliza uma `Startup` classe executada quando o processo de hospedagem iniciais:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

O `AddAuthentication()` método configura o serviço para adicionar autenticação baseada em cookies, que é usada em cenários de navegador e para definir o desafio para OpenID Connect.

A linha que contém `.AddMicrosoftIdentityWebApp` adiciona a autenticação da plataforma de identidade da Microsoft à sua aplicação. Isto é fornecido por [Microsoft.Identity.Web](microsoft-identity-web.md). Em seguida, é configurado para iniciar sedução usando a plataforma de identidade da Microsoft com base nas informações na `AzureAD` secção doappsettings.js *no* ficheiro de configuração:

| *appsettings.jsna* chave | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Identificação da aplicação (cliente)** da aplicação registada no portal Azure.                                                                                       |
| `Instance`             | Serviço de ficha de segurança (STS) para o utilizador autenticar. Este valor é tipicamente `https://login.microsoftonline.com/` , indicando a nuvem pública Azure. |
| `TenantId`             | Nome do seu inquilino ou do seu ID do inquilino (um GUID), ou *comum* para assinar em utilizadores com contas de trabalho ou escola ou contas pessoais da Microsoft.                             |

O `EnableTokenAcquisitionToCallDownstreamApi` método permite que a sua aplicação adquira um token para chamar APIs web protegidas. `AddMicrosoftGraph` permite que os seus controladores ou páginas Razor beneficiem diretamente da `GraphServiceClient` (por injeção de dependência) e os `AddInMemoryTokenCaches` métodos permitem que a sua aplicação beneficie de uma cache simbólica.

O `Configure()` método contém dois métodos importantes `app.UseAuthentication()` `app.UseAuthorization()` e, que permitem a sua funcionalidade nomeada. Também no método, deve registar as `Configure()` rotas da Microsoft Identity Web com pelo menos uma chamada ou uma chamada para `endpoints.MapControllerRoute()` `endpoints.MapControllers()` .

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Pode proteger um controlador ou os seus métodos aplicando o `[Authorize]` atributo à classe do controlador ou a um ou mais dos seus métodos. Este `[Authorize]` atributo restringe o acesso, permitindo apenas utilizadores autenticados. Se o utilizador ainda não estiver autenticado, poderá iniciar-se um desafio de autenticação para aceder ao controlador. Neste arranque rápido, os âmbitos são lidos a partir do ficheiro de configuração:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

O repo GitHub que contém a amostra de código core ASP.NET referenciada neste quickstart inclui instruções e mais amostras de código que mostram como:

- Adicionar autenticação a uma nova aplicação core web ASP.NET
- Ligue para o Microsoft Graph, outros APIs da Microsoft ou para as suas próprias APIs web
- Adicionar autorização
- Inscreva-se nos utilizadores em nuvens nacionais ou com identidades sociais

> [!div class="nextstepaction"]
> [ASP.NET tutoriais de aplicativos web Core no GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
