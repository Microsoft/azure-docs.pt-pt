---
title: 'Quickstart: Adicione o início de sôs-in com a Microsoft a uma aplicação web ASP.NET | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, aprenda a implementar o início de sôs-in da Microsoft numa aplicação web ASP.NET utilizando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: eb57be94e460241e3cacbe2dd20c071504a9222a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209769"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Quickstart: Adicione o início da plataforma de identidade da Microsoft a uma aplicação web ASP.NET

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação web ASP.NET pode assinar em utilizadores de qualquer organização do Azure Ative Directory (Azure AD). 

> [!div renderon="docs"]
> O seguinte diagrama mostra como funciona a aplicação da amostra:
>
> ![Diagrama da interação entre o navegador web, a aplicação web e a plataforma de identidade da Microsoft na aplicação da amostra.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.Net Framework 4.7.2+](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Registe-se e descarregue a app
> Tem duas opções para começar a construir a sua aplicação: configuração automática ou manual.
>
> ### <a name="automatic-configuration"></a>Configuração automática
> Se quiser configurar automaticamente a sua aplicação e, em seguida, descarregar a amostra de código, siga estes passos:
>
> 1. Aceda à página do <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">portal Azure para o registo de aplicações.</a>
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação num clique.
>
> ### <a name="manual-configuration"></a>Configuração manual
> Se pretender configurar manualmente a sua aplicação e a sua amostra de código, utilize os seguintes procedimentos.
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar a inscrição.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Para **nome,** insira um nome para a sua candidatura. Por exemplo, insira **ASPNET-Quickstart**. Os utilizadores da sua aplicação verão este nome e poderão alterá-lo mais tarde.
> 1. Adicione **https://localhost:44368/** em **Redirecionamento URI** e selecione **Registar.**
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. Na secção **de fluxos implícitos e híbridos,** selecione **fichas de identificação**.
> 1. Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código neste arranque rápido funcione, **https://localhost:44368/** introduza para **Redirecionar URI**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado ](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com este atributo.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> [Descarregue a solução Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto utilizando o Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configuramos o seu projeto com valores das propriedades da sua aplicação.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Passo 3: Executar o seu projeto Visual Studio

1. Extraia o ficheiro .zip para uma pasta local próxima da pasta raiz. Por exemplo, extrair para *C:\Azure-Samples*.
   
   Recomendamos extrair o arquivo num diretório perto da raiz da sua unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
2. Abra a solução em Visual Studio *(AppModelv2-WebApp-OpenIDConnect-DotNet.sln*).
3. Dependendo da versão do Visual Studio, poderá ter de clicar com razão no projeto **AppModelv2-WebApp-OpenIDConnect-DotNet** e, em seguida, selecione **pacotes Restore NuGet**.
4. Abra a consola do gestor de pacotes selecionando **ver** outra consola do gestor de  >    >  **pacotes do** Windows . Em seguida, execute o `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Edite *Web.config* e substitua os `ClientId` parâmetros, e `Tenant` `redirectUri` com:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    Neste código:
>
>    - `Enter_the_Application_Id_here` é o ID de aplicação (cliente) do registo da aplicação que criou anteriormente. Encontre o ID da aplicação (cliente) na página **geral** da aplicação nas **inscrições** da App no portal Azure.
>    - `Enter_the_Tenant_Info_Here` é uma das seguintes opções:
>      - Se a sua candidatura apoiar **apenas a Minha organização,** substitua este valor pelo iD do diretório (inquilino) ou pelo nome do inquilino (por exemplo, `contoso.onmicrosoft.com` ). Encontre o ID do diretório (inquilino) na página **geral** da aplicação nas **inscrições** da App no portal Azure.
>      - Se a sua aplicação suportar **contas em qualquer diretório organizacional,** substitua este valor por `organizations` .
>      - Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** substitua este valor por `common` .
>    - `redirectUri` é o **URI redirecionado** que inseriu anteriormente nas **inscrições** da App no portal Azure.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Mais informações

Esta secção fornece uma visão geral do código necessário para iniciar seduções nos utilizadores. Esta visão geral pode ser útil para entender como o código funciona, quais são os principais argumentos, e como adicionar o início de sing-in a uma aplicação ASP.NET existente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Como funciona a amostra
>
> ![Diagrama da interação entre o navegador web, a aplicação web e a plataforma de identidade da Microsoft na aplicação da amostra.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Pacotes NuGet de middleware OWIN

Pode configurar o pipeline de autenticação com autenticação baseada em cookies utilizando o OpenID Connect em ASP.NET com pacotes de middleware OWIN. Pode instalar estes pacotes executando os seguintes comandos na Consola Package Manager dentro do Estúdio Visual:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Classe de startup owin

O middleware OWIN usa uma *classe de arranque* que funciona quando o processo de hospedagem começa. Neste arranque rápido, o *ficheiro startup.cs* está na pasta raiz. O seguinte código mostra os parâmetros que este arranque rápido utiliza:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Onde  | Descrição |
> |---------|---------|
> | `ClientId`     | Identificação da aplicação a partir da aplicação registada no portal Azure. |
> | `Authority`    | O ponto final do serviço de fichas de segurança (STS) para o utilizador autenticar. Normalmente é `https://login.microsoftonline.com/{tenant}/v2.0` para a nuvem pública. Nesse URL, *{tenant}* é o nome do seu inquilino, da sua identificação de inquilino, ou `common` para uma referência ao ponto final comum. (O ponto final comum é utilizado para aplicações multitenantes.) |
> | `RedirectUri`  | O URL onde os utilizadores são enviados após a autenticação contra a plataforma de identidade da Microsoft. |
> | `PostLogoutRedirectUri`     | O URL para onde os utilizadores são enviados após a assinatura. |
> | `Scope`     | A lista de âmbitos a ser solicitado, separada por espaços. |
> | `ResponseType`     | O pedido de que a resposta da autenticação contém um código de autorização e um token de identificação. |
> | `TokenValidationParameters`     | Uma lista de parâmetros para a validação do token. Neste caso, `ValidateIssuer` está definido para indicar que pode aceitar `false` inscrições de qualquer tipo pessoal, de trabalho ou de conta escolar. |
> | `Notifications`     | Uma lista de delegados que podem ser executados em `OpenIdConnect` mensagens. |


> [!NOTE]
> A `ValidateIssuer = false` definição é uma simplificação para este arranque rápido. Em aplicações reais, valide o emitente. Veja as amostras para entender como fazer isso.

### <a name="authentication-challenge"></a>Desafio de autenticação

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
> Solicitar um desafio de autenticação utilizando este método é opcional. Normalmente, utilizaria-o quando pretende que uma vista seja acessível tanto a utilizadores autenticados como não autenticados. Em alternativa, pode proteger os controladores utilizando o método descrito na secção seguinte.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Atributo para proteger um controlador ou uma ação de controlador

Pode proteger um controlador ou ações de controlador utilizando o `[Authorize]` atributo. Este atributo restringe o acesso ao controlador ou ações, permitindo apenas aos utilizadores autenticados acederem às ações do controlador. Um desafio de autenticação acontecerá automaticamente quando um utilizador não autenticado tentar aceder a uma das ações ou controladores decorados pelo `[Authorize]` atributo.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para um guia completo passo a passo sobre aplicações de construção e novas funcionalidades, incluindo uma explicação completa deste arranque rápido, experimente o tutorial ASP.NET.

> [!div class="nextstepaction"]
> [Adicione o s-in a uma aplicação web ASP.NET](tutorial-v2-asp-webapp.md)
