---
title: Obter o consentimento para vários recursos (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba como um utilizador pode obter o consentimento prévio para vários recursos com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
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
ms.openlocfilehash: e8bd9a86d5ec0d39a7f1c26adac52f41e6420283
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121975"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Utilizador obtém o consentimento para vários recursos com MSAL.NET
O ponto de extremidade de plataforma de identidade Microsoft não permite a obter um token para vários recursos de uma só vez. Ao utilizar a biblioteca de autenticação da Microsoft para .NET (MSAL.NET), o parâmetro de âmbitos no método de token de aquisição apenas deve conter âmbitos para um único recurso. No entanto, pode previamente dar consentimento a vários recursos de antemão especificando âmbitos adicionais usando o `.WithExtraScopeToConsent` método construtor.

> [!NOTE]
> A obter o consentimento para vários recursos works, para a plataforma de identidade da Microsoft, mas não para o Azure AD B2C. O Azure AD B2C suporta apenas consentimento de administrador, não consentimento do utilizador.

Por exemplo, se tiver dois recursos que tem 2 examina cada:

- https:\//mytenant.onmicrosoft.com/customerapi (com 2, os âmbitos `customer.read` e `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (com 2, os âmbitos `vendor.read` e `vendor.write`)

Deve utilizar o `.WithExtraScopeToConsent` Modificador que tem o *extraScopesToConsent* parâmetro conforme mostrado no exemplo a seguir:

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

Isto irá ajudá-lo um token de acesso para a API web primeiro. Em seguida, quando precisa aceder à API web segundo silenciosamente pode adquirir o token da cache de token:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
