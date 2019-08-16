---
title: Passar o estado personalizado em solicitações de autenticação (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba como passar um valor de parâmetro de estado personalizado na solicitação de autenticação usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ae12624b3d897f05437f7795d1a1eee32ca37a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532743"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passar o estado personalizado em solicitações de autenticação usando MSAL. js
O parâmetro *State* , conforme definido pelo OAuth 2,0, está incluído em uma solicitação de autenticação e também é retornado na resposta do token para impedir ataques de solicitação entre sites forjado. Por padrão, a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) passa um valor de parâmetro de *estado* exclusivo gerado aleatoriamente nas solicitações de autenticação.

O parâmetro State também pode ser usado para codificar informações do estado do aplicativo antes do redirecionamento. Você pode passar o estado do usuário no aplicativo, como a página ou a exibição em que eles estavam, como entrada para esse parâmetro. A biblioteca MSAL. js permite que você passe seu estado personalizado como parâmetro de estado no `Request` objeto:

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

O estado transmitido é acrescentado ao GUID exclusivo definido por MSAL. js ao enviar a solicitação. Quando a resposta é retornada, o MSAL. js verifica uma correspondência de estado e retorna o estado `Response` personalizado passado no objeto como. `accountState`

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

Para saber mais, leia sobre como [criar um aplicativo de página única (Spa) usando o](scenario-spa-overview.md) MSAL. js.