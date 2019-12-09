---
title: Guia de início rápido do aplicativo Web Microsoft Identity ASP.NET Core | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 832fb890a91c9e87c26d8facdcba49ef9d810117
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920740"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste guia de início rápido, você aprenderá como um aplicativo Web ASP.NET Core pode entrar em contas pessoais (hotmail.com, outlook.com, outros) e contas corporativas e de estudante de qualquer instância do Azure Active Directory (AD do Azure).

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá para o [registros de aplicativo de portal do Azure](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registrar seu aplicativo e adicionar manualmente as informações de registro do aplicativo à sua solução, siga estas etapas:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AspNetCore-Quickstart`.
>    - Em **URI de redirecionamento**, adicione `https://localhost:44321/`e selecione **registrar**.
> 1. Selecione o menu **autenticação** e, em seguida, adicione as seguintes informações:
>    - Em **URIs de redirecionamento**, adicione `https://localhost:44321/signin-oidc`e selecione **salvar**.
>    - Na seção **Configurações avançadas** , defina **URL de logoff** para `https://localhost:44321/signout-oidc`.
>    - Em **Concessão implícita**, selecione **Tokens de ID**.
>    - Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: configurar seu aplicativo no portal do Azure
> Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar URLs de resposta como `https://localhost:44321/` e `https://localhost:44321/signin-oidc`, adicionar a URL de logout como `https://localhost:44321/signout-oidc`e tokens de ID de solicitação a serem emitidos pelo ponto de extremidade de autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Etapa 2: baixar seu projeto de ASP.NET Core

- [Baixe a solução do Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o projeto do Visual Studio

1. Extraia o arquivo zip para uma pasta local dentro da pasta raiz – por exemplo, **C:\Azure-Samples**
1. Se você usar o Visual Studio 2019, abra a solução no Visual Studio (opcional).
1. Edite o arquivo **appSettings. JSON** . Localize `ClientId` e atualize o valor de `ClientId` com o valor de **ID do aplicativo (cliente)** do aplicativo registrado. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Onde:
> - `Enter_the_Application_Id_here`-é a **ID do aplicativo (cliente)** para o aplicativo que você registrou na portal do Azure. Você pode encontrar a **ID do aplicativo (cliente)** na página de **visão geral** do aplicativo.
> - `Enter_the_Tenant_Info_Here`-é uma das seguintes opções:
>   - Se seu aplicativo der suporte a **contas neste diretório organizacional apenas**, substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, contoso.Microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Todos os utilizadores com contas Microsoft**, substitua este valor por `common`
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)** , o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

## <a name="more-information"></a>Mais informações

Esta seção fornece uma visão geral do código necessário para conectar usuários. Essa visão geral pode ser útil para entender como o código funciona, os argumentos principais e também se você deseja adicionar a entrada a um aplicativo ASP.NET Core existente.

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft. AspNetCore. Authentication* usa uma classe de inicialização que é executada quando o processo de hospedagem é inicializado:

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

O método `AddAuthentication` configura o serviço para adicionar a autenticação baseada em cookie, que é usada em cenários de navegador e para definir o desafio para o OpenID Connect. 

A linha que contém `.AddAzureAd` adiciona a autenticação da plataforma de identidade da Microsoft ao seu aplicativo. Em seguida, ele é configurado para entrar usando o ponto de extremidade da plataforma Microsoft Identity.

> |Onde  |  |
> |---------|---------|
> | ClientId  | ID do aplicativo (cliente) do aplicativo registrado no portal do Azure. |
> | Autoridade | O ponto de extremidade STS para o usuário se autenticar. Normalmente, isso é <https://login.microsoftonline.com/{tenant}/v2.0> para a nuvem pública, em que {Tenant} é o nome do seu locatário ou sua ID de locatário, ou *comum* para uma referência ao ponto de extremidade comum (usado para aplicativos multilocatários) |
> | TokenValidationParameters | Uma lista de parâmetros para a validação do token. Nesse caso, `ValidateIssuer` é definido como `false` para indicar que ele pode aceitar entradas de qualquer conta pessoal ou corporativa ou de estudante. |


> [!NOTE]
> A configuração `ValidateIssuer = false` é uma simplificação para este guia de início rápido. Em aplicativos reais, você precisa validar o emissor.
> Consulte os exemplos para entender como fazer isso.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Você pode proteger um controlador ou métodos de controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou aos métodos permitindo somente usuários autenticados, o que significa que o desafio de autenticação pode ser iniciado para acessar o controlador se o usuário não estiver autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Confira o repositório GitHub para este ASP.NET Core tutorial para obter mais informações, incluindo instruções sobre como adicionar autenticação a um aplicativo Web totalmente novo ASP.NET Core, como chamar Microsoft Graph e outras APIs da Microsoft, como chamar suas próprias APIs, como adicionar autorização, como conectar usuários em nuvens nacionais ou com identidades sociais e muito mais:

> [!div class="nextstepaction"]
> [Tutorial do ASP.NET Core aplicativo Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)