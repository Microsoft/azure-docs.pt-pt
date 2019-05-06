---
title: Aplicativo de Desktop que chamadas de APIs web (passar para produção) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho que chamadas de web APIs (passar para produção)
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca66a41f26c54bf04273682d14889a36b688c70
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075134"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Aplicação de ambiente de trabalho que chama o web APIs - mover para produção

Este artigo fornece detalhes para melhorar ainda mais seu aplicativo e mova-o para produção.

## <a name="handling-errors-in-desktop-applications"></a>Tratamento de erros em aplicativos de desktop

Os fluxos diferentes, aprendeu como lidar com os erros para os fluxos silenciosas (conforme mostrado na trechos de código). Também viu que existem casos em que é a interação necessário (incremental consentimento e acesso condicional).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Como o consentimento de utilizador inicialmente para vários recursos

> [!NOTE]
> A obter o consentimento para vários recursos works, para a plataforma de identidade da Microsoft, mas não para o Azure Active Directory (Azure AD) B2C. O Azure AD B2C suporta apenas consentimento de administrador, não consentimento do utilizador.

O ponto final do Microsoft identity platform (v2.0) não permite a obter um token para vários recursos de uma só vez. Por conseguinte, o `scopes` parâmetro só pode conter âmbitos para um único recurso. Pode garantir que o utilizador previamente dá consentimento a vários recursos, utilizando o `extraScopesToConsent` parâmetro.

Por exemplo, se tiver dois recursos, que possui dois âmbitos de cada um:

- `https://mytenant.onmicrosoft.com/customerapi` -com 2 âmbitos `customer.read` e `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -com 2 âmbitos `vendor.read` e `vendor.write`

Deve utilizar o `.WithAdditionalPromptToConsent` Modificador que tenha o `extraScopesToConsent` parâmetro.

Por exemplo:

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

Esta chamada irá obter um token de acesso para a API web primeiro.

Quando precisa chamar a API web segundo, pode chamar:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Conta pessoal da Microsoft requer reconsenting sempre que a aplicação é executada

Para os utilizadores de contas pessoais Microsoft, reprompting consentimento em cada chamada de cliente nativo (aplicação de ambiente de trabalho/móvel) para autorizar é o comportamento pretendido. Identidade do cliente nativo é insegura por natureza (ao contrário do aplicativo de cliente confidencial que um segredo com a plataforma do Microsoft Identity para provar a sua identidade do exchange). A plataforma de identidade da Microsoft escolheu mitigar este insegurança para serviços de consumidor ao pedir ao utilizador consentimento, sempre que a aplicação está autorizada.

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
