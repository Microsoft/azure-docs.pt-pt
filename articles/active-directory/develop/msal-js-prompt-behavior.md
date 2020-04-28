---
title: Comportamento de solicitação interativo (MSAL.js) / Azure
titleSuffix: Microsoft identity platform
description: Aprenda a personalizar o comportamento rápido em chamadas interativas utilizando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695981"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento rápido em pedidos interativos MSAL.js

Quando um utilizador tiver estabelecido uma sessão de AD Azure ativa com várias contas de utilizador, o sinal de AD Azure na página irá, por padrão, solicitar ao utilizador que selecione uma conta antes de iniciar o início. Os utilizadores não verão uma experiência de seleção de conta se houver apenas uma única sessão autenticada com a Azure AD.

A biblioteca MSAL.js (a partir de v0.2.4) não envia um`loginRedirect`parâmetro `loginPopup` `acquireTokenRedirect` rápido `acquireTokenPopup`durante os pedidos interativos ( e), portanto, não aplica qualquer comportamento rápido. Para pedidos de fichasilenciosa `acquireTokenSilent` silenciosa utilizando o método, mSAL.js passa um parâmetro rápido definido para `none`.

Com base no seu cenário de candidatura, pode controlar o comportamento rápido dos pedidos interativos, definindo o parâmetro de solicitação nos parâmetros de pedido passados para os métodos. Por exemplo, se quiser invocar a experiência de seleção de conta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Os seguintes valores de solicitação podem ser passados quando autenticados com Azure AD:

**login:** Este valor obrigará o utilizador a introduzir credenciais no pedido de autenticação.

**select_account:** Este valor proporcionará ao utilizador uma experiência de seleção de conta listando todas as contas em sessão.

**consentimento:** Este valor invocará o diálogo de consentimento da OAuth que permite aos utilizadores concederpermissões à app.

**nenhuma:** Este valor irá garantir que o utilizador não vê qualquer solicitação interativa. Recomenda-se que não passe este valor para métodos interativos em MSAL.js, uma vez que pode ter comportamentos inesperados. Em vez `acquireTokenSilent` disso, use o método para obter chamadas silenciosas.

## <a name="next-steps"></a>Passos seguintes

Leia mais `prompt` sobre o parâmetro no protocolo de [subvenção implícita OAuth 2.0](v2-oauth2-implicit-grant-flow.md) que a biblioteca MSAL.js utiliza.
