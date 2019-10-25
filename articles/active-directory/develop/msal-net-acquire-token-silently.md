---
title: Adquirir um token silenciosamente (biblioteca de autenticação da Microsoft para .NET)
titleSuffix: Microsoft identity platform
description: Saiba como adquirir um token de acesso silenciosamente (do cache de token) usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802937"
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
