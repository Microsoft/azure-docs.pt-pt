---
title: Obtenha consentimento para vários recursos (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como um utilizador pode obter o pré-consentimento para vários recursos utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6333d935e1a902ba173017f8149c098f44398955
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165877"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>O utilizador obtém o consentimento de vários recursos utilizando MSAL.NET
O ponto final da plataforma de identidade da Microsoft não permite obter um token para vários recursos de uma só vez. Ao utilizar a Biblioteca de Autenticação do Microsoft para .NET (MSAL.NET), o parâmetro de âmbito no método de ficha de aquisição só deve conter âmbitos para um único recurso. No entanto, pode pré-consentir vários recursos antecipadamente, especificando âmbitos adicionais utilizando o `.WithExtraScopeToConsent` método de construtor.

> [!NOTE]
> Obter consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para Azure AD B2C. O Azure AD B2C suporta apenas o consentimento administrativo, não o consentimento do utilizador.

Por exemplo, se tiver dois recursos que têm 2 âmbitos cada:

- https: \/ /mytenant.onmicrosoft.com/customerapi (com 2 âmbitos `customer.read` `customer.write` e)
- https: \/ /mytenant.onmicrosoft.com/vendorapi (com 2 âmbitos `vendor.read` `vendor.write` e)

Deve utilizar o `.WithExtraScopeToConsent` modificador que tem o parâmetro *extraScopesToConsent,* como mostra o seguinte exemplo:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Isto lhe dará um token de acesso para a primeira API web. Em seguida, quando você precisa aceder à segunda web API você pode obter silenciosamente o token a partir da cache simbólica:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
