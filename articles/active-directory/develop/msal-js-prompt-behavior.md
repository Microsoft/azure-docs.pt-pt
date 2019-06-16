---
title: Solicitar o comportamento em pedidos interativos (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba mais sobre como personalizar o comportamento do prompt em chamadas interativos, usando a biblioteca de autenticação da Microsoft para JavaScript (msal).
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
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544273"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Comportamento do prompt em pedidos interativos de msal

Quando um utilizador estabeleceu um Azure Active Directory na sessão do AD com várias contas de utilizador, a página de início de sessão do Azure AD pedirá por predefinição ao utilizador para selecionar uma conta antes de avançar para iniciar sessão. Os utilizadores não verão uma seleção de conta experiência se existir apenas uma única sessão autenticada com o Azure AD.

A biblioteca de msal (a partir de v0.2.4) não envia um parâmetro de linha de comandos durante os pedidos interativos (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` e `acquireTokenPopup`) e, deste modo, não impõe qualquer comportamento do prompt. Para pedidos de token silenciosas, utilizando o `acquireTokenSilent` método, msal passa um parâmetro de linha de comandos definido como `none`.

Com base no seu cenário de aplicativo, pode controlar o comportamento do prompt para pedidos interativos por definição, o parâmetro de linha de comandos nos parâmetros do pedido é passado para o método. Por exemplo, se pretender invocar a experiência de seleção de conta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Os seguintes valores de linha de comandos podem ser transmitidos ao autenticar com o Azure AD:

**login:** Este valor irá forçar o utilizador para introduzir as credenciais no pedido de autenticação.

**select_account:** Este valor irá fornecer ao usuário uma experiência de seleção de conta a listagem de todas as contas numa sessão.

**consent:** Este valor invocará a caixa de diálogo de consentimento do OAuth que permite aos utilizadores conceder permissões à aplicação.

**Nenhum:** Este valor irá garantir que o utilizador não vê qualquer linha de comandos interativa. É recomendado não passar este valor para métodos interativos no msal como podem ter comportamentos inesperados. Em alternativa, utilize o `acquireTokenSilent` método conseguir chamadas silenciosas.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre o `prompt` parâmetro na [concessão implícita OAuth 2.0](v2-oauth2-implicit-grant-flow.md) protocolo que biblioteca de msal utiliza.
