---
title: Aplicativo Web que chama APIs da Web (entrar)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs da Web (entrar)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720206"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Aplicativo Web que chama APIs da Web-entrar

Você já sabe como adicionar entrada ao seu aplicativo Web. Você aprende que, em um [aplicativo Web que entra com usuários-adicionar entrada](scenario-web-app-sign-user-sign-in.md).

O que há de diferente aqui é que, quando o usuário sai, desse aplicativo ou de qualquer aplicativo, você deseja remover do cache de tokens os tokens associados ao usuário.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Interceptando o retorno de chamada após a saída-saída única

Seu aplicativo pode interceptar o evento After `logout`, por exemplo, para limpar a entrada do cache de token associado à conta que foi desconectada. O aplicativo Web irá armazenar tokens de acesso para o usuário em um cache. Interceptar o retorno de chamada After `logout` permite que o aplicativo Web remova o usuário do cache de token.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Esse mecanismo é ilustrado no método `AddMsal()` de [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

A **URL de logout** que você registrou para seu aplicativo permite que você implemente o logout único. O ponto de extremidade `logout` da plataforma de identidade da Microsoft chamará a **URL de logout** registrada com seu aplicativo. Essa chamada ocorrerá se a saída tiver sido iniciada por meio de seu aplicativo Web ou de outro aplicativo Web ou navegador. Para obter mais informações, consulte [logout único](v2-protocols-oidc.md#single-sign-out).

```CSharp
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

O código para RemoveAccountAsync está disponível em [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O exemplo ASP.NET não remove contas do cache no logout global

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo de Java não remove contas do cache no logout global

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo de Python não remove as contas do cache na saída global

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirindo um token para o aplicativo Web](scenario-web-app-call-api-acquire-token.md)
