---
title: Adquirir um símbolo da cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Aprenda a adquirir um símbolo de acesso silenciosamente (a partir da cache simbólica) utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 95cfb937ce236d0acd1a3369068afc6f3b505aed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166251"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obtenha um símbolo da cache simbólica usando MSAL.NET

Quando adquire um token de acesso utilizando a Microsoft Authentication Library para .NET (MSAL.NET), o token está em cache. Quando a aplicação precisa de um token, deve primeiro ligar para o `AcquireTokenSilent` método para verificar se um símbolo aceitável está na cache. Em muitos casos, é possível adquirir outro token com mais miras baseadas num símbolo na cache. Também é possível refrescar um token quando está perto de expirar (como a cache simbólica também contém um token de atualização).

O padrão recomendado é chamar o `AcquireTokenSilent` método primeiro.  Se `AcquireTokenSilent` falhar, então adquira um símbolo utilizando outros métodos.

No exemplo seguinte, a aplicação tenta adquirir um símbolo a partir da cache simbólica.  Se for lançada uma `MsalUiRequiredException` exceção, a aplicação adquire um símbolo interativamente. 

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
