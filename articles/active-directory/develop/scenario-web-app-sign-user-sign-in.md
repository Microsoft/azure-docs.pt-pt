---
title: Aplicativo Web que conecta os usuários (entrar)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que faz logon de usuários (entrar)
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
ms.openlocfilehash: be7801515355452306cd5e7afa709a0681c7c314
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562198"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Aplicativo Web que conecta usuários-entrar

Saiba como adicionar entrada ao código para seu aplicativo Web que faz logon de usuários.

## <a name="sign-in"></a>Iniciar sessão

O código que temos na [configuração de código do aplicativo do](scenario-web-app-sign-user-app-configuration.md) artigo anterior é tudo o que você precisa para implementar o logout. Depois que o usuário entrar no seu aplicativo, você provavelmente desejará habilitá-lo a sair. O ASP.NET Core manipula a saída para você.

## <a name="what-sign-out-involves"></a>O que a saída envolve

Sair de um aplicativo Web é mais do que remover as informações sobre a conta conectada do estado do aplicativo Web.
O aplicativo Web também deve redirecionar o usuário para o ponto `logout` de extremidade da plataforma Microsoft Identity para sair. Quando seu aplicativo Web redireciona o usuário para o ponto de `logout` extremidade, esse ponto de extremidade limpa a sessão do usuário no navegador. Se seu aplicativo não for para o `logout` ponto de extremidade, o usuário se reautenticaria em seu aplicativo sem inserir suas credenciais novamente, pois ele teria uma sessão de logon único válida com o ponto de extremidade da plataforma de identidade da Microsoft.

Para saber mais, consulte a seção [Enviar uma solicitação de saída](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de [identidade da Microsoft e a documentação conceitual do protocolo OpenID Connect](v2-protocols-oidc.md) .

## <a name="application-registration"></a>Registo da aplicação

Durante o registro do aplicativo, você terá registrado um **URI de logout**de postagem. Em nosso tutorial, você se `https://localhost:44321/signout-oidc` registrou no campo **URL de logout** da seção **Configurações avançadas** na página **autenticação** . Para obter detalhes, consulte [registrar o aplicativo webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Código de ASP.NET Core

### <a name="signout-button"></a>Botão de saída

O botão sair é exposto no `Views\Shared\_LoginPartial.cshtml` e exibido somente quando há uma conta autenticada (ou seja, quando o usuário entrou anteriormente).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`ação do`AccountController`

Pressionar o botão **sair** no aplicativo Web dispara a `SignOut` ação no `Account` controlador. Nas versões anteriores dos modelos do ASP.NET Core, o `Account` controlador foi incorporado com o aplicativo Web, mas esse não é mais o caso, pois agora ele faz parte do próprio ASP.NET Core Framework. 

O código para o `AccountController` está disponível no repositório do ASP.NET Core em do [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle de conta:

- Define um URI de redirecionamento de OpenID para `/Account/SignedOut` para que o controlador seja chamado de volta quando o Azure ad tiver executado a saída
- Chamadas `Signout()`, que permitem que o middleware OpenIdConnect entre em contato com o `logout` ponto de extremidade da plataforma de identidade da Microsoft, que:

  - Limpa o cookie de sessão do navegador e
  - Finalmente, as chamadas retornam a **URL de logout**, que, por padrão, exibe a página de exibição desconectada [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) também fornecido como parte de ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptando a chamada para o `logout` ponto de extremidade

O middleware ASP.NET Core OpenIdConnect permite que seu aplicativo intercepte a chamada para o ponto de extremidade da `logout` plataforma de identidade da Microsoft fornecendo um `OnRedirectToIdentityProviderForSignOut`evento OpenIdConnect chamado. O aplicativo Web usa-o para tentar evitar que a caixa de diálogo Selecionar conta seja apresentada ao usuário ao sair. Essa interceptação é feita no `AddAzureAdV2Authentication` `Microsoft.Identity.Web` da biblioteca reutilizável. Consulte [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

## <a name="aspnet-code"></a>Código ASP.NET

No ASP.NET, a saída é disparada do método SignOut () em um controlador (para a instância AccountController). Esse método não faz parte da estrutura ASP.NET (ao contrário do que acontece em ASP.NET Core) e não usa Async, e é por isso que:

- envia um desafio de desconexão de OpenId
- limpa o cache
- redireciona para a página que deseja

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

Se você não quiser usar ASP.NET Core ou ASP.NET, poderá examinar a documentação do protocolo, que está disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-app-sign-user-production.md)
