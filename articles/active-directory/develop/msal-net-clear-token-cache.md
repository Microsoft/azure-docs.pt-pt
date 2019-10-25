---
title: Limpar o cache de token usando a biblioteca de autenticação da Microsoft para .NET
titleSuffix: Microsoft identity platform
description: Saiba como limpar o cache de token usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd48078ec87b085a71ee23006d18917881894bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802923"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Limpar o cache de token usando MSAL.NET

Quando você [adquire um token de acesso](msal-acquire-cache-tokens.md) usando a MSAL.net (biblioteca de autenticação da Microsoft para .net), o token é armazenado em cache. Quando o aplicativo precisa de um token, ele deve primeiro chamar o método `AcquireTokenSilent` para verificar se um token aceitável está no cache. 

Limpar o cache é obtido removendo as contas do cache. No entanto, isso não remove o cookie de sessão que está no navegador.  O exemplo a seguir instancia um aplicativo cliente público, obtém as contas do aplicativo e remove as contas.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Para saber mais sobre como adquirir e armazenar em cache tokens, leia [adquirir um token de acesso](msal-acquire-cache-tokens.md).
