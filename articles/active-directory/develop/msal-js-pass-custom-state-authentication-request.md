---
title: Passe estado personalizado em pedidos de autenticação (MSAL.js) / Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como passar um valor de parâmetro de estado personalizado no pedido de autenticação utilizando a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063676"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passe o estado personalizado em pedidos de autenticação usando MSAL.js

O parâmetro *do estado,* tal como definido pela OAuth 2.0, está incluído num pedido de autenticação e é também devolvido na resposta simbólica para evitar ataques de falsificação de pedidos de trans-locais. Por predefinição, a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js) transmite um valor de parâmetro *único* gerado aleatoriamente nos pedidos de autenticação.

O parâmetro de estado também pode ser usado para codificar informações do estado da aplicação antes de redirecionar. Pode passar o estado do utilizador na aplicação, como a página ou a visualização em que se encontrava, como entrada para este parâmetro. A biblioteca MSAL.js permite-lhe passar o seu estado personalizado como parâmetro de estado no `Request` objeto:

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
> Se quiser saltar um token em cache e ir para o servidor, por favor passe o boolean `forceRefresh` para o objeto AuthenticationParameters usado para fazer um pedido de login/token.
> `forceRefresh` não deve ser utilizado por padrão, devido ao impacto de desempenho na sua aplicação.
> Confiar na cache dará aos seus utilizadores uma melhor experiência.
> Saltar a cache só deve ser usado em cenários em que sabe que os dados atualmente em cache não têm informações atualizadas.
> Tal como uma ferramenta Admin que adiciona funções a um utilizador que precisa de obter um novo token com funções atualizadas.

Por exemplo:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

O passado no estado é anexado ao conjunto único GUID por MSAL.js ao enviar o pedido. Quando a resposta é devolvida, MSAL.js verifica uma correspondência de Estado e, em seguida, devolve o costume passado no estado no `Response` objeto como `accountState` .

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

Para saber mais, leia sobre [a construção de uma aplicação de uma página (SPA)](scenario-spa-overview.md) utilizando MSAL.js.