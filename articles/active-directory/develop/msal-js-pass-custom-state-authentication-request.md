---
title: Passar o estado personalizado em solicitações de autenticação (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como passar um valor de parâmetro de estado personalizado na solicitação de autenticação usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4cb0f3d054f9afd0c606f80fd6fc5d553eff806
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916320"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passar o estado personalizado em solicitações de autenticação usando MSAL. js
O parâmetro *State* , conforme definido pelo OAuth 2,0, está incluído em uma solicitação de autenticação e também é retornado na resposta do token para impedir ataques de solicitação entre sites forjado. Por padrão, a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) passa um valor de parâmetro de *estado* exclusivo gerado aleatoriamente nas solicitações de autenticação.

O parâmetro State também pode ser usado para codificar informações do estado do aplicativo antes do redirecionamento. Você pode passar o estado do usuário no aplicativo, como a página ou a exibição em que eles estavam, como entrada para esse parâmetro. A biblioteca MSAL. js permite que você passe seu estado personalizado como parâmetro de estado no objeto `Request`:

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
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

O estado transmitido é acrescentado ao GUID exclusivo definido por MSAL. js ao enviar a solicitação. Quando a resposta é retornada, o MSAL. js verifica uma correspondência de estado e retorna o estado personalizado transmitido no objeto `Response` como `accountState`.

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