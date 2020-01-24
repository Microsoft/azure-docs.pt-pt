---
title: Adquirir um token do cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Saiba como adquirir um token de acesso silenciosamente (do cache de token) usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: bb17ebf5afdc20eaa9db8b86a5643ad18a112eab
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695658"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obter um token do cache de token usando MSAL.NET

Quando você adquire um token de acesso usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET), o token é armazenado em cache. Quando o aplicativo precisa de um token, ele deve primeiro chamar o método `AcquireTokenSilent` para verificar se um token aceitável está no cache. Em muitos casos, é possível adquirir outro token com mais escopos com base em um token no cache. Também é possível atualizar um token quando ele está ficando próximo da expiração (pois o cache do token também contém um token de atualização).

O padrão recomendado é chamar o método `AcquireTokenSilent` primeiro.  Se `AcquireTokenSilent` falhar, adquira um token usando outros métodos.

No exemplo a seguir, o aplicativo tenta primeiro adquirir um token do cache de token.  Se uma exceção de `MsalUiRequiredException` for lançada, o aplicativo adquirirá um token interativamente. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
