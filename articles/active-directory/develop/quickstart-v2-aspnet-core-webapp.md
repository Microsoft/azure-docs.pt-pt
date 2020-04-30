---
title: Adicione o sessão com a Microsoft para ASP.NET aplicações web Core - plataforma de identidade Microsoft / Azure
description: Saiba como implementar o Microsoft Sign-In numa aplicação web ASP.NET Core utilizando o OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: db488e4a9ec9aa0f4f12c8de45f123dba1a93cdf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82112716"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Quickstart: Adicione o início de sessão com a Microsoft a uma aplicação web ASP.NET Core
Neste arranque rápido, você usa uma amostra de código para saber como uma aplicação web ASP.NET Core pode assinar em contas pessoais (hotmail.com, outlook.com, outros) e contas de trabalho e escola de qualquer instância do Azure Ative Directory (Azure AD). (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao [portal Azure - Registos de aplicações.](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar manualmente as informações de registo da aplicação à sua solução, siga estes passos:
>
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome,** introduza um nome de aplicação significativo que `AspNetCore-Quickstart`será apresentado aos utilizadores da aplicação, por exemplo.
>    - No **Redirecione URI,** adicione `https://localhost:44321/`, e selecione **Register**.
> 1. Selecione o menu **de Autenticação** e, em seguida, adicione as seguintes informações:
>    - Em **Redirecionar URIs,** adicione `https://localhost:44321/signin-oidc`, e selecione **Guardar**.
>    - Na secção **de definições avançadas,** coloque **o URL de logout** para `https://localhost:44321/signout-oidc`.
>    - Em **Concessão implícita**, selecione **Tokens de ID**.
>    - Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure
> Para a amostra de código para este arranque rápido funcionar, `https://localhost:44321/` `https://localhost:44321/signin-oidc`você precisa adicionar URLs de resposta como e , adicionar o URL de Logout como `https://localhost:44321/signout-oidc`, e solicitar tokens de ID para serem emitidos pelo ponto final de autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Passo 2: Descarregue o seu projeto ASP.NET Core

> [!div renderon="docs"]
> [Descarregue a solução Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para ser executado.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Passo 3: Executar o seu projeto Estúdio Visual
> 1. Extrair o ficheiro zip para uma pasta local dentro da pasta raiz - por exemplo, **C:\Azure-Samples**
> 1. Abra a solução no Estúdio Visual
> 1. Editar o ficheiro **appsettings.json.** Encontre `ClientId` e atualize `ClientId` o valor da **Aplicação (cliente) o** valor de ID da aplicação que registou.
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here`- é o ID de **Aplicação (cliente)** para a aplicação registada no portal Azure. Pode encontrar o ID de **Aplicação (cliente)** na página **de visão geral** da aplicação.
> - `Enter_the_Tenant_Info_Here`- é uma das seguintes opções:
>   - Se a sua candidatura apoiar **contas apenas neste diretório organizacional,** substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, contoso.microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Todos os utilizadores com contas Microsoft**, substitua este valor por `common`
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

## <a name="more-information"></a>Mais informações

Esta secção dá uma visão geral do código necessário para iniciar sessão nos utilizadores. Esta visão geral pode ser útil para entender como o código funciona, argumentos principais, e também se você quiser adicionar sessão a uma aplicação core ASP.NET existente.

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de startups

*Microsoft.AspNetCore.Authentication* middleware utiliza uma classe Startup que é executada quando o processo de hospedagem inicializa:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

O `AddAuthentication` método configura o serviço para adicionar autenticação baseada em cookies, que é usado em cenários de navegador e para definir o desafio para OpenID Connect.

A linha `.AddAzureAd` que contém adiciona a autenticação da plataforma de identidade da Microsoft à sua aplicação. É configurado para iniciar sessão utilizando o ponto final da plataforma de identidade da Microsoft.

> |Onde  |  |
> |---------|---------|
> | ClientId  | Id de aplicação (cliente) da aplicação registada no portal Azure. |
> | Autoridade | O ponto final do STS para o utilizador autenticar. Normalmente, <https://login.microsoftonline.com/{tenant}/v2.0> isto é para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id inquilino, ou *comum* para uma referência ao ponto final comum (usado para aplicações multi-inquilinos) |
> | TokenValidaçãoParâmetros | Uma lista de parâmetros para a validação do token. Neste caso, `ValidateIssuer` está `false` definido para indicar que pode aceitar inscrições de qualquer conta pessoal, ou de trabalho ou escolar. |


> [!NOTE]
> A `ValidateIssuer = false` definição é uma simplificação para este arranque rápido. Em aplicações reais é necessário validar o emitente.
> Veja as amostras para entender como fazê-lo.
>
> Note também `Configure` o método que `app.UserCookiePolicy()` contém dois métodos importantes: e`app.UseAuthentication()`

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more core
    app.UseCookiePolicy();
    app.UseAuthentication();
    // more core
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Pode proteger um controlador ou `[Authorize]` métodos controladores utilizando o atributo. Este atributo restringe o acesso ao controlador ou métodos, permitindo apenas utilizadores autenticados, o que significa que o desafio de autenticação pode ser iniciado para aceder ao controlador se o utilizador não for autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Consulte o repo do GitHub para este ASP.NET tutorial Core para mais informações, incluindo instruções sobre como adicionar autenticação a uma nova aplicação core Web ASP.NET, como ligar para o Microsoft Graph, e outras APIs da Microsoft, como ligar para as suas próprias APIs, como adicionar autorização, como assinar utilizadores em nuvens nacionais, ou com identidades sociais e muito mais :

> [!div class="nextstepaction"]
> [tutorial de aplicação web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
