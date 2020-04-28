---
title: Mova a aplicação de desktop chamando apis web para a produção - plataforma de identidade Microsoft / Azure
description: Saiba como mover uma aplicação de desktop que chama APIs web para a produção
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
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882884"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Aplicação de desktop que chama APIs web: Mover-se para a produção

Neste artigo, aprende-se a mover a sua aplicação de desktop que chama APIs web para produção.

## <a name="handle-errors-in-desktop-applications"></a>Lidar com erros em aplicações de ambiente de trabalho

Nos diferentes fluxos, aprendeste a lidar com os erros dos fluxos silenciosos, como mostra o código. Também viu que há casos em que a interação é necessária, como no consentimento incremental e no acesso condicional.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Tenha o consentimento do utilizador adiantado para vários recursos

> [!NOTE]
> Obter o consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para o Azure Ative Directory (Azure AD) B2C. O Azure AD B2C suporta apenas o consentimento do administrador, não o consentimento do utilizador.

Não é possível obter um símbolo para vários recursos ao mesmo tempo com a plataforma de identidade da Microsoft (v2.0) endpoint. O `scopes` parâmetro pode conter possibilidades para apenas um único recurso. Pode garantir que o utilizador consente com vários recursos utilizando o `extraScopesToConsent` parâmetro.

Por exemplo, pode ter dois recursos que têm dois âmbitos cada:

- `https://mytenant.onmicrosoft.com/customerapi`com os `customer.read` âmbitos e`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`com os `vendor.read` âmbitos e`vendor.write`

Neste exemplo, utilize `.WithAdditionalPromptToConsent` o modificador `extraScopesToConsent` que tem o parâmetro.

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
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>Em MSAL para iOS e macOS

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

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Esta chamada dá-lhe um sinal de acesso para a primeira API web.

Quando precisar de ligar para a segunda `AcquireTokenSilent` API web, ligue para a API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Conta pessoal da Microsoft requer reconsentimento cada vez que a app corre

Para os utilizadores de conta pessoal da Microsoft, repedir o consentimento em cada chamada de cliente nativo (desktop ou aplicativo móvel) para autorizar é o comportamento pretendido. A identidade do cliente nativo é inerentemente insegura, o que é contrário à identidade de aplicação de cliente confidencial. As aplicações confidenciais do cliente trocam um segredo com a plataforma Microsoft Identity para provar a sua identidade. A plataforma de identidade da Microsoft optou por mitigar esta insegurança para os serviços de consumo, solicitando ao utilizador o consentimento sempre que a aplicação for autorizada.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
