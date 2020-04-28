---
title: Passe estado personalizado em pedidos de autenticação (MSAL.js) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como passar um valor de parâmetro de estado personalizado no pedido de autenticação utilizando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084947"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passe estado personalizado em pedidos de autenticação usando MSAL.js

O parâmetro *estatal,* tal como definido pela OAuth 2.0, está incluído num pedido de autenticação e é também devolvido na resposta simbólica para evitar ataques de falsificação de pedidos de falsificação. Por predefinição, a Microsoft Authentication Library for JavaScript (MSAL.js) passa um valor único de parâmetro de *estado* gerado aleatoriamente nos pedidos de autenticação.

O parâmetro estatal também pode ser usado para codificar informações do estado da aplicação antes de redirecionar. Pode passar o estado do utilizador na aplicação, como a página ou visualização em que se encontrava, como entrada para este parâmetro. A biblioteca MSAL.js permite-lhe passar o seu `Request` estado personalizado como parâmetro de estado no objeto:

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
> Se quiser saltar um token em cache e ir ao servidor, `forceRefresh` por favor passe a booleana para o objeto AutenticaçãoParametros usado para fazer um pedido de login/token.
> `forceRefresh`Não deve ser utilizado por defeito, devido ao impacto de desempenho na sua aplicação.
> Confiar na cache dará aos seus utilizadores uma melhor experiência.
> Saltar a cache só deve ser utilizado em cenários em que os dados atualmente em cache não tenham informações atualizadas.
> Como uma ferramenta Admin que adiciona funções a um utilizador que precisa de obter um novo símbolo com funções atualizadas.

Por exemplo:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

O passado em estado é anexado ao único CONJUNTO GUID definido por MSAL.js ao enviar o pedido. Quando a resposta é devolvida, mSAL.js verifica por uma correspondência de `Response` estado `accountState`e, em seguida, devolve o costume passado em estado no objeto como .

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