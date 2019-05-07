---
title: Adquirir um token silenciosamente (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba como adquirir um token de acesso silenciosamente (a partir da cache de tokens) com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e49dd85503475feab81a14148588981b04ad954
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158854"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obter um token da cache de tokens com MSAL.NET

Quando compra um token de acesso com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET), o token é colocado em cache. Quando o aplicativo precisa de um token, primeiro deve chamar o `AcquireTokenSilent` método para verificar se um token aceitável está no cache. Em muitos casos, é possível obter outro token com mais âmbitos com base num token na cache. Também é possível atualizar um token quando ele está ficando prestes a expirar (como o cache de tokens também contém um token de atualização).

O padrão recomendado é chamar o `AcquireTokenSilent` método primeiro.  Se `AcquireTokenSilent` falhar, em seguida, adquirir um token com outros métodos.

No exemplo seguinte, a aplicação tenta primeiro adquirir um token da cache de token.  Se um `MsalUiRequiredException` exceção é gerada, a aplicação recebe um token de forma interativa. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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