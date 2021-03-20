---
title: Pedido interativo comportamento rápido (MSAL.js) | Rio Azure
titleSuffix: Microsoft identity platform
description: Aprenda a personalizar o comportamento rápido em chamadas interativas utilizando a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85477554"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento rápido em MSAL.js pedidos interativos

Quando um utilizador tiver estabelecido uma sessão AD Azure ativa com várias contas de utilizador, o sinal AD Azure na página irá, por predefinição, solicitar ao utilizador que selecione uma conta antes de iniciar a sessão. Os utilizadores não verão uma experiência de seleção de conta se houver apenas uma única sessão autenticada com Azure AD.

A biblioteca MSAL.js (a partir de v0.2.4) não envia um parâmetro rápido durante os pedidos interativos ( `loginRedirect` `loginPopup` `acquireTokenRedirect` `acquireTokenPopup` e, assim, não impõe qualquer comportamento rápido. Para pedidos de token silenciosos utilizando o `acquireTokenSilent` método, MSAL.js passa um parâmetro rápido definido para `none` .

Com base no seu cenário de aplicação, pode controlar o comportamento rápido dos pedidos interativos, definindo o parâmetro de solicitação nos parâmetros de pedido passados para os métodos. Por exemplo, se quiser invocar a experiência de seleção de conta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Os seguintes valores rápidos podem ser passados quando autenticados com Azure AD:

**login:** Este valor obrigará o utilizador a introduzir credenciais no pedido de autenticação.

**select_account:** Este valor proporcionará ao utilizador uma experiência de seleção de conta que lista todas as contas em sessão.

**consentimento:** Este valor irá invocar o diálogo de consentimento da OAuth que permite aos utilizadores conceder permissões à app.

**nenhum:** Este valor garantirá que o utilizador não vê qualquer solicitação interativa. Recomenda-se não passar este valor a métodos interativos em MSAL.js pois pode ter comportamentos inesperados. Em vez disso, use o `acquireTokenSilent` método para obter chamadas silenciosas.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre o `prompt` parâmetro no protocolo de [concessão implícita OAuth 2.0](v2-oauth2-implicit-grant-flow.md) que MSAL.js utiliza a biblioteca.
