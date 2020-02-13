---
title: Adicionar entrada com a Microsoft a ASP.NET Core aplicativos Web-plataforma Microsoft Identity | Azure
description: Saiba como implementar a entrada da Microsoft em um aplicativo Web ASP.NET Core usando o OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c11f7daf68585d63d19fca282ef2f4a306303ac7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160734"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET Core

Neste guia de início rápido, você aprenderá como um aplicativo Web ASP.NET Core pode entrar em contas pessoais (hotmail.com, outlook.com, outros) e contas corporativas e de estudante de qualquer instância do Azure Active Directory (AD do Azure).

![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

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
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AspNetCore-Quickstart`.
>    - No **Redirect URI,** adicione `https://localhost:44321/`, e selecione **Register**.
> 1. Selecione o menu **de Autenticação** e, em seguida, adicione as seguintes informações:
>    - Em **Redirecionar uris,** adicione `https://localhost:44321/signin-oidc`, e selecione **Save**.
>    - Na secção **de definições Avançadas,** coloque o URL de **logout** para `https://localhost:44321/signout-oidc`.
>    - Sob **subvenção implícita,** verifique as **fichas de identificação.**
>    - Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure
> Para a amostra de código para este arranque rápido funcionar, você precisa adicionar URLs de resposta como `https://localhost:44321/` e `https://localhost:44321/signin-oidc`, adicionar o URL de Logout como `https://localhost:44321/signout-oidc`, e solicitar fichas de identificação para serem emitidas pelo ponto final de autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Etapa 2: baixar seu projeto de ASP.NET Core

- [Descarregue a solução Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Transferir o seu projeto do Visual Studio

1. Extrair o ficheiro zip para uma pasta local dentro da pasta raiz - por exemplo, **C:\Azure-Samples**
1. Se você usar o Visual Studio 2019, abra a solução no Visual Studio (opcional).
1. Editar o ficheiro **appsettings.json.** Encontre `ClientId` e atualize o valor da `ClientId` com o valor de ID da **aplicação (cliente)** da aplicação que registou. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este arranque rápido apoia Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here` - é o ID de **Aplicação (cliente)** para a aplicação que registou no portal Azure. Pode encontrar o ID de **Aplicação (cliente)** na página **de visão geral** da aplicação.
> - `Enter_the_Tenant_Info_Here` - é uma das seguintes opções:
>   - Se a sua candidatura apoiar **contas apenas neste diretório organizacional,** substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, contoso.microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Todos os utilizadores com contas Microsoft**, substitua este valor por `common`
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)** , o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

## <a name="more-information"></a>Mais informações

Esta seção fornece uma visão geral do código necessário para conectar usuários. Essa visão geral pode ser útil para entender como o código funciona, os argumentos principais e também se você deseja adicionar a entrada a um aplicativo ASP.NET Core existente.

### <a name="startup-class"></a>Classe de inicialização

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

O método `AddAuthentication` configura o serviço para adicionar autenticação baseada em cookies, que é usado em cenários de navegador e definir o desafio para OpenID Connect. 

A linha que contém `.AddAzureAd` adiciona a autenticação da plataforma de identidade da Microsoft à sua aplicação. Em seguida, ele é configurado para entrar usando o ponto de extremidade da plataforma Microsoft Identity.

> |Onde  |  |
> |---------|---------|
> | ClientId  | ID do aplicativo (cliente) do aplicativo registrado no portal do Azure. |
> | Autoridade | O ponto de extremidade STS para o usuário se autenticar. Normalmente, este é <https://login.microsoftonline.com/{tenant}/v2.0> para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id inquilino, ou *comum* para uma referência ao ponto final comum (usado para aplicações multi-inquilinos) |
> | TokenValidationParameters | Uma lista de parâmetros para a validação do token. Neste caso, `ValidateIssuer` deverá `false` indicar que pode aceitar inscrições a partir de quaisquer contas pessoais, ou de trabalho ou escolares. |


> [!NOTE]
> Definir `ValidateIssuer = false` é uma simplificação para este arranque rápido. Em aplicativos reais, você precisa validar o emissor.
> Consulte os exemplos para entender como fazer isso.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Pode proteger um controlador ou métodos controladores utilizando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou aos métodos permitindo somente usuários autenticados, o que significa que o desafio de autenticação pode ser iniciado para acessar o controlador se o usuário não estiver autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Confira o repositório GitHub para este ASP.NET Core tutorial para obter mais informações, incluindo instruções sobre como adicionar autenticação a um aplicativo Web totalmente novo ASP.NET Core, como chamar Microsoft Graph e outras APIs da Microsoft, como chamar suas próprias APIs, como adicionar autorização, como conectar usuários em nuvens nacionais ou com identidades sociais e muito mais:

> [!div class="nextstepaction"]
> [tutorial da Web App ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)