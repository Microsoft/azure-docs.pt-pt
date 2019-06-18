---
title: Aplicação Web que inicia sessão dos utilizadores (entrar) - a plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que inicia sessão os usuários (início de sessão)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074624"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Aplicação que inicia sessão dos utilizadores da Web – iniciar sessão

Saiba como adicionar início de sessão para o código para a sua aplicação web que os utilizadores inicia sessão.

## <a name="sign-in"></a>Iniciar sessão

O código que temos no artigo anterior [configuração do código da aplicação](scenario-web-app-sign-user-app-configuration.md) é tudo o que precisa para implementar a fim de sessão. Depois do utilizador tem sessão iniciada para a sua aplicação, que provavelmente pretende ativá-las terminar sessão. ASP.NET core processa a fim de sessão para.

## <a name="what-sign-out-involves"></a>O que termine envolve

Terminar a sessão de uma aplicação web é sobre a remoção de mais do que as informações sobre a conta com sessão iniciada de estado da aplicação web.
A aplicação web também deve redirecionar o utilizador para a versão 2.0 do Microsoft identity platform `logout` ponto final para terminar sessão. Quando a sua aplicação web redireciona o utilizador para o `logout` ponto final, este ponto final limpa a sessão do utilizador do navegador. Se a sua aplicação não vá para o `logout` ponto final, o utilizador seria autenticar para a sua aplicação sem introduzir as respetivas credenciais novamente, uma vez que é preciso uma válido única início de sessão com o ponto de final de v2.0 do Microsoft Identity platform.

Para obter mais informações, consulte a [enviar um pedido de Sign-out](v2-protocols-oidc.md#send-a-sign-out-request) secção a [v2.0 de plataforma do Microsoft Identity e o protocolo OpenID Connect](v2-protocols-oidc.md) documentação conceitual.

## <a name="application-registration"></a>Registo da aplicação

Durante o registo de aplicação, irá registou um **publicar a fim de sessão URI**. No nosso tutorial, registou `https://localhost:44321/signout-oidc` no **URL de fim de sessão** campo a **definições avançadas** secção a **autenticação** página. Para obter detalhes, veja [ registar a aplicação de webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Código do ASP.NET Core

### <a name="signout-button"></a>Botão de fim de sessão

A fim de sessão botão está exposta no `Views\Shared\_LoginPartial.cshtml` e apresentada apenas quando existe uma conta autenticada (ou seja, quando o utilizador iniciou sessão anteriormente).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` ação do `AccountController`

Premir a **termine** botão sobre os acionadores da aplicação web a `SignOut` ação no `Account` controlador. Nas versões anteriores dos modelos de núcleo do ASP.NET, o `Account` controlador foi incorporado com a aplicação web, mas isso já não for o caso, pois agora é parte da estrutura ASP.NET Core em si. 

O código para o `AccountController` está disponível a partir do repositório de núcleo ASP.NET em partir [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle de conta:

- URI para de redirecionamento de conjuntos de um OpenID `/Account/SignedOut` para que o controlador é chamado quando o Azure AD efetuou a fim de sessão
- Chamadas `Signout()`, que permite o middleware de OpenIdConnect entre em contato com a plataforma de identidade do Microsoft `logout` ponto final que:

  - Limpa o cookie de sessão do navegador, e
  - Chamadas finalmente chama novamente o **URL de fim de sessão**, que) por predefinição, apresenta o assinado página vista [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) também é fornecido como parte do ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptar a chamada para o `logout` ponto final

O middleware do ASP.NET Core OpenIdConnect permite à sua aplicação interceptar a chamada para a plataforma de identidade da Microsoft `logout` ponto final ao fornecer um evento de OpenIdConnect chamado `OnRedirectToIdentityProviderForSignOut`. A aplicação web utiliza para tentar evitar a caixa de diálogo selecione conta a serem apresentados ao utilizador quando terminar a sessão. Essa interceptação é feita no `AddAzureAdV2Authentication` do `Microsoft.Identity.Web` biblioteca reutilizável. Consulte [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>Código do ASP.NET

No ASP.NET, terminar a sessão é acionado do método SignOut() num controlador (por exemplo AccountController). Esse método não faz parte da estrutura do ASP.NET (ao contrário do que acontece no ASP.NET Core) e não usa o async e é por isso que ele:

- envia um desafio de fim de sessão OpenId
- Limpe a cache
- redireciona para a página que ele deseja

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Se não quiser utilizar o ASP.NET Core ou ASP.NET, pode examinar a documentação do protocolo, o que está disponível a partir [abrir ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-app-sign-user-production.md)
