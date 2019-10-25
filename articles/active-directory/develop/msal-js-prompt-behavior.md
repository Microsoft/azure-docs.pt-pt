---
title: Comportamento do prompt em solicitações interativas (biblioteca de autenticação da Microsoft para JavaScript)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre como personalizar o comportamento de prompt em chamadas interativas usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803021"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento de prompt em solicitações interativas do MSAL. js

Quando um usuário estabeleceu uma sessão ativa do Azure AD com várias contas de usuário, a página de entrada do Azure AD solicitará, por padrão, que o usuário selecione uma conta antes de continuar a entrar. Os usuários não verão uma experiência de seleção de conta se houver apenas uma única sessão autenticada com o Azure AD.

A biblioteca MSAL. js (a partir do v 0.2.4) não envia um parâmetro de prompt durante as solicitações interativas (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` e `acquireTokenPopup`) e, portanto, não impõe nenhum comportamento de prompt. Para solicitações de token silenciosas usando o método `acquireTokenSilent`, MSAL. js passa um parâmetro de prompt definido como `none`.

Com base em seu cenário de aplicativo, você pode controlar o comportamento do prompt para as solicitações interativas definindo o parâmetro prompt nos parâmetros de solicitação passados para os métodos. Por exemplo, se você quiser invocar a experiência de seleção de conta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Os seguintes valores de prompt podem ser passados ao autenticar com o Azure AD:

**logon:** Esse valor forçará o usuário a inserir as credenciais na solicitação de autenticação.

**select_account:** Esse valor dará ao usuário uma experiência de seleção de conta listando todas as contas na sessão.

**consentimento:** Esse valor invocará a caixa de diálogo de consentimento do OAuth que permite aos usuários conceder permissões ao aplicativo.

**nenhum:** Esse valor garantirá que o usuário não veja nenhum prompt interativo. É recomendável não passar esse valor para métodos interativos no MSAL. js, pois ele pode ter comportamentos inesperados. Em vez disso, use o método `acquireTokenSilent` para obter chamadas silenciosas.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre o parâmetro `prompt` no protocolo de [concessão implícita OAuth 2,0](v2-oauth2-implicit-grant-flow.md) que a biblioteca MSAL. js usa.
