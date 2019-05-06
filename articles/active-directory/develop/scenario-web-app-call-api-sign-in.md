---
title: Aplicação Web que chamadas de web APIs (entrar) - a plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação Web que chamadas de web APIs (início de sessão)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074564"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Aplicação Web que chama o web APIs - início de sessão

Já sabe como adicionar início de sessão na sua aplicação web. Saiba que, na [aplicação Web que inicia sessão de utilizadores - adicionar início de sessão](scenario-web-app-sign-user-sign-in.md).

O que é diferente aqui, é que quando o utilizador tem sessão iniciada horizontalmente, a partir desta aplicação ou a partir de qualquer aplicação, que pretende remover da cache de token, os tokens associados ao utilizador.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Interceptando o retorno de chamada após o fim de sessão - único terminar sessão

Seu aplicativo pode interceptar a depois `logout` evento, por exemplo para limpar a entrada de cache de tokens associado à conta que terminar a sessão. Vamos ver na segunda parte deste Tutorial (sobre a aplicação Web chamar uma API Web), que a aplicação web irá armazenar tokens de acesso para o utilizador em cache. Interceptando o depois `logout` retorno de chamada permite que seu aplicativo web para remover o utilizador a partir da cache de token. Esse mecanismo é ilustrado na `AddMsal()` método de [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

O **Url de fim de sessão** que se registra para a aplicação permite-lhe implementar a fim de sessão único. A plataforma de identidade da Microsoft `logout` ponto final chamará o **URL de fim de sessão** registado com a sua aplicação. Esta chamada acontece se o fim de sessão foi iniciada da sua aplicação web ou a partir de outra aplicação web ou o browser. Para obter mais informações, consulte [fim de sessão único](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) na documentação do conceitual.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter um token para a aplicação web](scenario-web-app-call-api-acquire-token.md)
