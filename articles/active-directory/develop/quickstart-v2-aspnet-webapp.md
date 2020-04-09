---
title: Adicione o insessão da plataforma de identidade da Microsoft a uma aplicação web ASP.NET Azure
description: Saiba como implementar o acesso da Microsoft a uma aplicação web ASP.NET utilizando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 2946551233500a80c6fa7f02ad4c427f2d654b40
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883649"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Quickstart: Adicione o início da plataforma de identidade da Microsoft a uma aplicação web ASP.NET
Neste arranque rápido, você usa uma amostra de código para aprender como uma ASP.NET aplicação web para assinar em contas pessoais (hotmail.com, outlook.com, outros) e contas de trabalho e escola de qualquer instância do Azure Ative Directory (Azure AD).  (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao novo portal Azure - Painel de inscrições de [aplicativos.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)
> 1. Introduza um nome para a sua aplicação e clique em **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome,** introduza um nome de aplicação significativo que `ASPNET-Quickstart`será apresentado aos utilizadores da aplicação, por exemplo.
>      - Adicione `http://localhost:44368/` o **Redirecionamento URI,** clique em **Registar**.
>      - A partir do painel de navegação esquerdo sob a secção Gerir, **selecione Autenticação**
>          - Sob a subsecção **Implicit Grant,** selecione **fichas de identificação**.
>          - E, em seguida, selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
> Para o código de exemplo deste início rápido funcionar, terá de adicionar um URL de resposta como `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com este atributo

#### <a name="step-2-download-your-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> [Descarregue a solução Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal"]
> Executar o projeto usando o Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação. 

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Passo 3: Executar o seu projeto Estúdio Visual

1. Extraia o ficheiro zip para uma pasta local próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
1. Abra a solução no Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)
1. Dependendo da versão do Visual Studio, poderá ter `AppModelv2-WebApp-OpenIDConnect-DotNet` de clicar no projeto e **restaurar os pacotes NuGet**
1. Abra a consola do gestor de pacotes (ver -> Outras consolas de gestor de pacotes de > Windows) e executar`Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`

> [!div renderon="docs"]
> 5. Edite **Web.config** e substitua os parâmetros `ClientId` e `Tenant` por:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    Em que:
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Tenant_Info_Here` - é uma das opções abaixo:
>   - Se a sua candidatura apoiar **apenas a minha organização,** substitua este valor pelo nome **id do arrendatário** ou **pelo nome do inquilino** (por exemplo, contoso.onmicrosoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Todos os utilizadores com contas Microsoft**, substitua este valor por `common`
>
> > [!TIP]
> > - Para encontrar os valores do *ID da Aplicação*, o *ID de Diretório (inquilino)*, e os *Tipos de conta suportados*, vá para a página **Descrição geral**
> > - Certifique-se `redirectUri` de que o valor para o **Web.config** corresponde ao **Redirect URI** definido para o Registo de Aplicações em AD Azure (se não, navegue para o menu de **autenticação** para o Registo de Aplicações e atualize o **REDIRECT URI** para combinar)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Mais informações

Esta secção fornece uma descrição geral do código necessário para o início de sessão dos utilizadores. Esta visão geral pode ser útil para entender como o código funciona, os principais argumentos, e também se você quiser adicionar sessão a uma aplicação ASP.NET existente.

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Pacotes NuGet de middleware OWIN

Pode configurar o pipeline de autenticação com a autenticação baseada em cookies através do OpenID Connect em ASP.NET com pacotes de Middleware OWIN. Pode instalar estes pacotes ao executar os comandos seguintes na **Consola do Gestor de Pacotes** do Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb  
```

### <a name="owin-startup-class"></a>Classe de Arranque OWIN

O middleware OWIN usa uma *classe de startup* sintetiza quando o processo de hospedagem é inicializa. Neste arranque rápido, o ficheiro *startup.cs* localizado na pasta raiz. O código seguinte mostra o parâmetro utilizado por este início rápido:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Onde  |  |
> |---------|---------|
> | `ClientId`     | O ID de Aplicação da aplicação registada no portal do Azure |
> | `Authority`    | O ponto final STS para o utilizador autenticar. Normalmente <https://login.microsoftonline.com/{tenant}/v2.0> para a cloud pública, em que {tenant} é o nome do seu inquilino, o Id do seu inquilino, ou *common* para uma referência ao ponto final comum (utilizado para aplicações multi-inquilino) |
> | `RedirectUri`  | URL onde os utilizadores são enviados após autenticação contra o ponto final da plataforma de identidade da Microsoft |
> | `PostLogoutRedirectUri`     | URL para onde os utilizadores são enviados depois de terminarem sessão |
> | `Scope`     | A lista dos âmbitos que estão a ser solicitados, separados por espaços |
> | `ResponseType`     | Pedido de que a resposta da autenticação contenha um token de ID |
> | `TokenValidationParameters`     | Uma lista de parâmetros para a validação do token. Neste caso, `ValidateIssuer` está definido como `false` para indicar que pode aceitar inícios de sessão de todos os tipos de conta: pessoal, escolar ou profissional |
> | `Notifications`     | Uma lista de delegados que podem ser executados em diferentes mensagens *OpenIdConnect* |


> [!NOTE]
> A `ValidateIssuer = false` definição é uma simplificação para este arranque rápido. Em aplicações reais é necessário validar o emitente.
> Veja as amostras para entender como fazê-lo.

### <a name="initiate-an-authentication-challenge"></a>Iniciar um desafio de autenticação

Pode forçar um utilizador a iniciar sessão, solicitando um desafio de autenticação no seu controlador:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Pedir um desafio de autenticação com o método acima é opcional e, normalmente, utilizado quando pretende que uma vista seja acessível para utilizadores autenticados e não autenticados. Em alternativa, pode proteger os controladores utilizando o método descrito na secção seguinte.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Pode proteger um controlador ou as ações do controlador com o atributo `[Authorize]`. Este atributo restringe o acesso ao controlador ou às ações ao permitir que apenas os utilizadores autenticados acedam às ações no controlador, o que significa que o desafio de autenticação acontecerá automaticamente quando um utilizador *não autenticado* tentar aceder a uma das ações ou ao controlador decorado pelo atributo `[Authorize]`.

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial para ASP.NET para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

> [!div class="nextstepaction"]
> [Tutorial de início de sessão](./tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
