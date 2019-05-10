---
title: Limpar a cache de token ao utilizar a biblioteca de autenticação da Microsoft para .NET - Azure
description: Saiba como limpar a cache de token com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1604d2833387b105fc7897a89f96ebcf9486d6a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468473"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Limpar a cache de token com MSAL.NET

Quando [adquirir um token de acesso](msal-acquire-cache-tokens.md) com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET), o token é colocado em cache. Quando o aplicativo precisa de um token, primeiro deve chamar o `AcquireTokenSilent` método para verificar se um token aceitável está no cache. 

Limpar a cache é obtida ao remover as contas de cache. Isto não remove o cookie de sessão que está no navegador, no entanto.  O exemplo seguinte cria uma instância de uma aplicação cliente público, obtém as contas para a aplicação e remove as contas.

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

Para saber mais sobre a adquirir e colocação em cache de tokens, leia [adquirir um token de acesso](msal-acquire-cache-tokens.md).
