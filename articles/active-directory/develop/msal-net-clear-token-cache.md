---
title: Limpe a cache simbólica (MSAL.NET) ! Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como limpar a cache simbólica utilizando a Biblioteca de Autenticação do Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 83c1dd43235dc7bccb322a484362b08544d54d11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477520"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Limpe a cache simbólica usando MSAL.NET

Quando [adquire um token](msal-acquire-cache-tokens.md) de acesso utilizando a Microsoft Authentication Library para .NET (MSAL.NET), o token está em cache. Quando a aplicação precisa de um token, deve primeiro ligar para o `AcquireTokenSilent` método para verificar se um símbolo aceitável está na cache. 

A limpeza da cache é conseguida removendo as contas da cache. No entanto, isto não remove o cookie de sessão que está no navegador.  O exemplo seguinte instantaneamente uma aplicação de cliente público, obtém as contas para o pedido, e remove as contas.

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

Para saber mais sobre a aquisição e caching tokens, leia [adquira um token de acesso.](msal-acquire-cache-tokens.md)
