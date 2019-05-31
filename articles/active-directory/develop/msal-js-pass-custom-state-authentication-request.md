---
title: Passar o estado personalizado em pedidos de autenticação (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba como passar um valor de parâmetro de estado personalizado no pedido de autenticação usando a biblioteca de autenticação da Microsoft para JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420502"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passar o estado personalizado em pedidos de autenticação com msal
O *estado* parâmetro, conforme definido pela OAuth 2.0, que está incluído numa solicitação de autenticação e também é devolvido na resposta de token para impedir ataques de falsificação de solicitação entre sites. Por predefinição, a biblioteca de autenticação da Microsoft para JavaScript (msal) passa um gerada aleatoriamente exclusivo *estado* valor de parâmetro nos pedidos de autenticação.

O parâmetro de estado também pode ser utilizado para codificar as informações do Estado da aplicação antes de redirecionamento. Pode passar o estado do utilizador na aplicação, como a página ou a vista que se encontrava, como entrada para este parâmetro. A biblioteca de msal permite que passe o seu estado personalizado como parâmetro de estado no `Request` objeto:

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
};
```

Por exemplo:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

O estado com êxito no é acrescentado ao GUID exclusivo definido pelo msal ao enviar o pedido. Quando a resposta é devolvida, msal verifica a existência de uma correspondência de estado e, em seguida, devolve o personalizado passado no Estado no `Response` objeto como `accountState`.

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

Para saber mais, leia sobre [criando um aplicativo de página única (SPA)](scenario-spa-overview.md) através de msal.