---
title: Mover o aplicativo de desktop chamando APIs da Web para produção-plataforma de identidade da Microsoft | Azure
description: Saiba como mover um aplicativo de área de trabalho que chama APIs da Web para produção
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a5e57d0ef37a3a2e758e42f122c8e014c94958
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919988"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Aplicativo de desktop que chama APIs da Web – mover para produção

Este artigo fornece detalhes para mover seu aplicativo de área de trabalho que chama APIs da Web para produção.

## <a name="handling-errors-in-desktop-applications"></a>Tratamento de erros em aplicativos de área de trabalho

Nos fluxos diferentes, você aprendeu como lidar com os erros dos fluxos silenciosos (conforme mostrado nos trechos de código). Você também viu que há casos em que a interação é necessária (consentimento incremental e acesso condicional).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Como fazer o consentimento do usuário antecipadamente para vários recursos

> [!NOTE]
> A obtenção de consentimento para vários recursos funciona para a plataforma de identidade da Microsoft, mas não para o Azure Active Directory (Azure AD) B2C. O Azure AD B2C dá suporte apenas ao consentimento do administrador, não ao consentimento do usuário.

O ponto de extremidade da plataforma Microsoft Identity (v 2.0) não permite que você obtenha um token para vários recursos de uma vez. Portanto, o parâmetro `scopes` pode conter apenas escopos para um único recurso. Você pode garantir que o usuário tenha o mesmo consentimento para vários recursos usando o parâmetro `extraScopesToConsent`.

Por exemplo, se você tiver dois recursos, que têm dois escopos cada:

- `https://mytenant.onmicrosoft.com/customerapi`-com 2 escopos `customer.read` e `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-com 2 escopos `vendor.read` e `vendor.write`

Você deve usar o modificador `.WithAdditionalPromptToConsent` que tem o parâmetro `extraScopesToConsent`.

Por exemplo:

### <a name="in-msalnet"></a>Em MSAL.NET

```CSharp
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

### <a name="in-msal-for-ios-and-macos"></a>No MSAL para iOS e macOS

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Essa chamada obterá um token de acesso para a primeira API Web.

Quando você precisar chamar a segunda API da Web, poderá chamar `AcquireTokenSilent` API:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>A conta pessoal da Microsoft requer o reconsentimento a cada vez que o aplicativo é executado

Para usuários de contas pessoais da Microsoft, solicitar o consentimento em cada chamada de cliente nativo (aplicativo móvel/Desktop) para autorizar é o comportamento pretendido. A identidade nativa do cliente é inerentemente insegura (ao contrário do aplicativo cliente confidencial que troca um segredo com a plataforma de identidade da Microsoft para provar sua identidade). A plataforma de identidade da Microsoft optou por mitigar essa insegurança para os serviços do consumidor solicitando o consentimento do usuário, sempre que o aplicativo for autorizado.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
