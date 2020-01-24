---
title: Obtenha o consentimento de vários recursos (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como um utilizador pode obter o pré-consentimento de vários recursos usando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3bd23a1f14d5e3cbf9fc41ade47571c6689f3468
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695029"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Utilizador obtém consentimento para vários recursos usando MSAL.NET
O ponto final da plataforma de identidade da Microsoft não lhe permite obter um símbolo para vários recursos ao mesmo tempo. Ao utilizar a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET), o parâmetro de âmbito no método do token adquirente só deve conter âmbitos para um único recurso. No entanto, pode pré-consentir antecipadamente vários recursos, especificando âmbitos adicionais utilizando o método `.WithExtraScopeToConsent` construtor.

> [!NOTE]
> Obter o consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para o Azure AD B2C. O Azure AD B2C dá suporte apenas ao consentimento do administrador, não ao consentimento do usuário.

Por exemplo, se tiver dois recursos que têm 2 âmbitos cada:

- https:\//mytenant.onmicrosoft.com/customerapi (com 2 âmbitos `customer.read` e `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (com 2 âmbitos `vendor.read` e `vendor.write`)

Deve utilizar o modificador `.WithExtraScopeToConsent` que tenha o parâmetro *extraScopesToConsent,* como mostra o seguinte exemplo:

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
