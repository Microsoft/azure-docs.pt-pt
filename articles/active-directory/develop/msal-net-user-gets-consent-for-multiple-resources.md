---
title: Obtenha o consentimento de vários recursos (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como um utilizador pode obter o pré-consentimento de vários recursos usando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085838"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Utilizador obtém consentimento para vários recursos usando MSAL.NET
O ponto final da plataforma de identidade da Microsoft não lhe permite obter um símbolo para vários recursos ao mesmo tempo. Ao utilizar a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET), o parâmetro de âmbito no método do token adquirente só deve conter âmbitos para um único recurso. No entanto, pode pré-consentir antecipadamente vários recursos, especificando âmbitos adicionais utilizando o método do `.WithExtraScopeToConsent` construtor.

> [!NOTE]
> Obter o consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para o Azure AD B2C. O Azure AD B2C suporta apenas o consentimento do administrador, não o consentimento do utilizador.

Por exemplo, se tiver dois recursos que têm 2 âmbitos cada:

- https:\//mytenant.onmicrosoft.com/customerapi (com `customer.read` 2 `customer.write`âmbitos e)
- https:\//mytenant.onmicrosoft.com/vendorapi (com `vendor.read` 2 `vendor.write`âmbitos e)

Deve utilizar `.WithExtraScopeToConsent` o modificador que tem o parâmetro *extraScopesToConsent,* como mostra o seguinte exemplo:

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

Isto lhe dará um sinal de acesso para a primeira API web. Em seguida, quando precisa de aceder à segunda API web, pode adquirir silenciosamente o símbolo da cache simbólica:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
