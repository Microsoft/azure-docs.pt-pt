---
title: Adquirir um símbolo da cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Aprenda a adquirir um token de acesso silenciosamente (a partir da cache simbólica) utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084831"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obtenha um símbolo da cache simbólica usando MSAL.NET

Quando adquire um sinal de acesso utilizando a Microsoft Authentication Library para .NET (MSAL.NET), o token está em cache. Quando o pedido precisa de um símbolo, deve primeiro chamar o `AcquireTokenSilent` método para verificar se um token aceitável está na cache. Em muitos casos, é possível adquirir outro símbolo com mais âmbitos baseados num símbolo na cache. Também é possível refrescar um símbolo quando está perto da expiração (como a cache token também contém um token refrescante).

O padrão recomendado é `AcquireTokenSilent` chamar o método primeiro.  Se `AcquireTokenSilent` falhar, adquira um símbolo utilizando outros métodos.

No exemplo seguinte, a aplicação tenta primeiro adquirir um símbolo da cache simbólica.  Se `MsalUiRequiredException` for aberta uma exceção, a aplicação adquire um símbolo interactivamente. 

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
