---
title: Passar o estado personalizado em solicitações de autenticação (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como passar um valor de parâmetro de estado personalizado na solicitação de autenticação usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084947"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passar o estado personalizado em solicitações de autenticação usando MSAL. js

O parâmetro *estatal,* tal como definido pela OAuth 2.0, está incluído num pedido de autenticação e é também devolvido na resposta simbólica para evitar ataques de falsificação de pedidos de falsificação. Por predefinição, a Microsoft Authentication Library for JavaScript (MSAL.js) passa um valor único de parâmetro de *estado* gerado aleatoriamente nos pedidos de autenticação.

O parâmetro State também pode ser usado para codificar informações do estado do aplicativo antes do redirecionamento. Você pode passar o estado do usuário no aplicativo, como a página ou a exibição em que eles estavam, como entrada para esse parâmetro. A biblioteca MSAL.js permite-lhe passar o seu estado personalizado como parâmetro de estado no objeto `Request`:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Se quiser saltar um token em cache e ir ao servidor, por favor passe a `forceRefresh` booleana para o objeto AutenticaçãoParametros usado para fazer um pedido de login/token.
> `forceRefresh` não devem ser utilizados por defeito, devido ao impacto de desempenho na sua aplicação.
> Depender do cache dará aos seus usuários uma experiência melhor.
> Ignorar o cache só deve ser usado em cenários em que você saiba que os dados atualmente armazenados em cache não têm informações atualizadas.
> Como uma ferramenta de administração que adiciona funções a um usuário que precisa obter um novo token com funções atualizadas.

Por exemplo:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

O estado transmitido é acrescentado ao GUID exclusivo definido por MSAL. js ao enviar a solicitação. Quando a resposta é devolvida, a MSAL.js verifica uma correspondência de Estado e devolve o costume passado em estado no `Response` objeto como `accountState`.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Para saber mais, leia sobre [a construção de uma aplicação de uma única página (SPA)](scenario-spa-overview.md) usando MSAL.js.