---
title: Obter o consentimento para vários recursos (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba como um utilizador pode obter o consentimento prévio para vários recursos com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 45bad9a03e8eff6f22ebb99fd2ef4bcd5fecf9b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158824"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Utilizador obtém o consentimento para vários recursos com MSAL.NET
O ponto de extremidade de plataforma de identidade Microsoft não permite a obter um token para vários recursos de uma só vez. Ao utilizar a biblioteca de autenticação da Microsoft para .NET (MSAL.NET), o parâmetro de âmbitos no método de token de aquisição apenas deve conter âmbitos para um único recurso. No entanto, pode previamente dar consentimento a vários recursos de antemão especificando âmbitos adicionais usando o `.WithExtraScopeToConsent` método construtor.

> [!NOTE]
> A obter o consentimento para vários recursos works, para a plataforma de identidade da Microsoft, mas não para o Azure AD B2C. O Azure AD B2C suporta apenas consentimento de administrador, não consentimento do utilizador.

Por exemplo, se tiver dois recursos que tem 2 examina cada:

- https://mytenant.onmicrosoft.com/customerapi (com 2 âmbitos `customer.read` e `customer.write`)
- https://mytenant.onmicrosoft.com/vendorapi (com 2 âmbitos `vendor.read` e `vendor.write`)

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
