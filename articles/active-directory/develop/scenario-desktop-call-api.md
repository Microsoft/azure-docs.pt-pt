---
title: Aplicativo de Desktop que chamadas de web APIs (chamar uma API web) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho que chama o web APIs (chamar uma API web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abaf234d3b216e0f67501e5d2f2f5c3f874c5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111252"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplicação de ambiente de trabalho que chama a APIs - web chamar uma API web

Agora que tem um token, pode chamar uma API web protegida.

## <a name="calling-a-web-api-from-net"></a>Chamar uma API web do .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Chamar várias APIs - consentimento Incremental e o acesso condicional

Se precisa chamar várias APIs para o mesmo utilizador, assim que obtém um token para a API do primeiro, pode chamar apenas `AcquireTokenSilent`, e obterá um token para as outras APIs silenciosamente a maior parte do tempo.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Os casos em que é necessária qualquer interação é quando:

- O utilizador dado consentimento para a primeira API, mas agora precisa dar consentimento para obter mais âmbitos (incremental consentimento)
- A primeira API não exige a autenticação de vários fatores, mas tem da próxima Sílaba.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-desktop-production.md)
