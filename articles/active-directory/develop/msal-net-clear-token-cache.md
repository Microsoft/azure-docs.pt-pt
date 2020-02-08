---
title: Limpe a cache simbólica (MSAL.NET)  Azure
titleSuffix: Microsoft identity platform
description: Aprenda a limpar a cache simbólica utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084763"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Limpe a cache simbólica usando MSAL.NET

Quando [adquire um sinal de acesso](msal-acquire-cache-tokens.md) utilizando a Microsoft Authentication Library para .NET (MSAL.NET), o token está em cache. Quando o pedido precisa de um símbolo, deve primeiro chamar o método `AcquireTokenSilent` para verificar se um token aceitável está na cache. 

A limpeza da cache é conseguida removendo as contas da cache. Isto não remove o cookie de sessão que está no navegador, no entanto.  O exemplo seguinte instantaneamente uma aplicação de cliente público, obtém as contas da aplicação e remove as contas.

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

Para saber mais sobre adquirir e cacher tokens, leia [adquirir um sinal de acesso.](msal-acquire-cache-tokens.md)
