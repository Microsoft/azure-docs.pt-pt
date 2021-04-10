---
title: Mover aplicativo de desktop chamando APIs web para produção | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como mover uma aplicação de desktop que chama APIs web à produção
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22e61ea767d781dc9da54d61143c1b2524e06e94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584419"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Aplicativo de desktop que chama APIs web: Mover para a produção

Neste artigo, aprende-se a mover a sua aplicação de desktop que chama APIs web à produção.

## <a name="handle-errors-in-desktop-applications"></a>Lidar com erros em aplicações de ambiente de trabalho

Nos diferentes fluxos, aprendeste a lidar com os erros para os fluxos silenciosos, como mostram os cortes de código. Também viu que há casos em que a interação é necessária, como no consentimento incremental e no acesso condicional.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Tenha o consentimento do utilizador antecipadamente para vários recursos

> [!NOTE]
> Obter consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para O Azure Ative Directory (Azure AD) B2C. O Azure AD B2C suporta apenas o consentimento administrativo, não o consentimento do utilizador.

Não é possível obter um símbolo de vários recursos ao mesmo tempo com a plataforma de identidade da Microsoft. O `scopes` parâmetro pode conter âmbitos para apenas um recurso. Pode assegurar-se de que o utilizador pré-consente com vários recursos utilizando o `extraScopesToConsent` parâmetro.

Por exemplo, pode ter dois recursos que têm dois âmbitos cada:

- `https://mytenant.onmicrosoft.com/customerapi` com os âmbitos `customer.read` e `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` com os âmbitos `vendor.read` e `vendor.write`

Neste exemplo, utilize o `.WithExtraScopesToConsent` modificador que tenha o `extraScopesToConsent` parâmetro.

Por exemplo:

### <a name="in-msalnet"></a>Em MSAL.NET

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
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>No MSAL para iOS e macOS

Objetivo C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Rápido:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Esta chamada dá-lhe um token de acesso para a primeira API web.

Ao ligar para a segunda API web, ligue para a `AcquireTokenSilent` API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>A conta pessoal da Microsoft requer reconhecimento cada vez que a aplicação é executado

Para os utilizadores de contas pessoais da Microsoft, repromundo o consentimento em cada cliente nativo (desktop ou aplicativo móvel) para autorizar é o comportamento pretendido. A identidade do cliente nativo é inerentemente insegura, o que é contrário à identidade confidencial da aplicação do cliente. As aplicações confidenciais de clientes trocam um segredo com a plataforma Microsoft Identity para provar a sua identidade. A plataforma de identidade da Microsoft optou por atenuar esta insegurança para os serviços ao consumidor, solicitando ao utilizador o consentimento sempre que a aplicação é autorizada.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

Para experimentar amostras adicionais, consulte [as aplicações de desktop e cliente público móvel.](sample-v2-code.md#desktop-and-mobile-public-client-apps)



