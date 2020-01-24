---
title: Remover contas do cache de token na saída-plataforma de identidade da Microsoft | Azure
description: Saiba como remover uma conta do cache de token ao sair
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a77bb59afa753fa9d1655e787d4f7a18715ed2ca
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701591"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>Remover contas da cache no sign-out global

Já sabe como adicionar sessão de acesso à sua aplicação web. Aprende-se isso na [aplicação Web que faz sessão aos utilizadores - adicione sessão .](scenario-web-app-sign-user-sign-in.md)

O que é diferente aqui, é que quando o utilizador assinou, a partir desta aplicação, ou de qualquer aplicação, pretende remover da cache simbólica, as fichas associadas ao utilizador.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Intercetação da chamada após a inscrição - Single Sign out

A sua aplicação pode intercetar o evento após `logout`, por exemplo, para limpar a entrada da cache simbólica associada à conta que assinou. A aplicação web armazenará fichas de acesso para o utilizador numa cache. Intercetar o backback após `logout` permite que a sua aplicação web remova o utilizador da cache token.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Este mecanismo é ilustrado no método `AddMsal()` de [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

O **Url de Logout** que registou para a sua aplicação permite-lhe implementar uma única inscrição. A plataforma de identidade da Microsoft `logout` ponto final irá ligar para o **URL de Logout** registado com a sua aplicação. Esta chamada acontece se o sign-out foi iniciado a partir da sua aplicação web, ou de outra aplicação web ou do navegador. Para mais informações, consulte [a inscrição individual](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

O código para RemoveAccountAsync está disponível na [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A amostra ASP.NET não remove contas da cache no sign-out global

# <a name="javatabjava"></a>[Java](#tab/java)

A amostra de Java não remove contas da cache no sign-out global

# <a name="pythontabpython"></a>[Python](#tab/python)

A amostra python não remove contas da cache no sign-out global

---

## <a name="next-steps"></a>Passos seguintes

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
