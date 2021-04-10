---
title: Lidar com erros e exceções no MSAL.js
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, o Acesso Condicional reclama desafios e retréis em aplicações MSAL.js.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761342"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Lidar com erros e exceções no MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Manipulação de erros em MSAL.js

MSAL.js fornece objetos de erro que abstraem e classificam os diferentes tipos de erros comuns. Também fornece uma interface para aceder a detalhes específicos dos erros, tais como mensagens de erro para manuseá-los adequadamente.

### <a name="error-object"></a>Objeto de erro

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Ao alargar a classe de erro, tem acesso às seguintes propriedades:
- `AuthError.message`: O mesmo que o `errorMessage` .
- `AuthError.stack`: Pilhar vestígios para erros arremessais.

### <a name="error-types"></a>Tipos de erros

Estão disponíveis os seguintes tipos de erro:

- `AuthError`: Classe de erro base para a biblioteca MSAL.js, também utilizada para erros inesperados.

- `ClientAuthError`: Classe de erro, que denota um problema com a autenticação do Cliente. A maioria dos erros que vêm da biblioteca serão ClientAuthErrors. Estes erros resultam de coisas como chamar um método de login quando o login já está em andamento, o utilizador cancela o login, e assim por diante.

- `ClientConfigurationError`: Classe de erro, estende-se `ClientAuthError` antes de os pedidos serem feitos quando os parâmetros de configuração do utilizador são mal formados ou em falta.

- `ServerError`: Classe de erro, representa as cadeias de erro enviadas pelo servidor de autenticação. Estes podem ser erros como formatos ou parâmetros de pedido inválidos, ou quaisquer outros erros que impeçam o servidor de autenticar ou autorizar o utilizador.

- `InteractionRequiredAuthError`: Classe de erro, estende-se `ServerError` para representar erros do servidor, que requerem uma chamada interativa. Este erro é lançado se `acquireTokenSilent` o utilizador for obrigado a interagir com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos de erro incluem `"interaction_required"` `"login_required"` , e `"consent_required"` .

Para o manuseamento de erros nos fluxos de autenticação com métodos de redireccionamento `loginRedirect` (, `acquireTokenRedirect` , terá de registar a chamada de retorno, que é chamada com sucesso ou falha após o redirecionamento utilizando o método da seguinte `handleRedirectCallback()` forma:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Os métodos para a experiência pop-up `loginPopup` (, `acquireTokenPopup` ) promessas de retorno, para que você possa usar o padrão de promessa (.em seguida e.catch) para manuseá-los como mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Erros que requerem interação

Um erro é devolvido quando se tenta usar um método não interativo de adquirir um símbolo como `acquireTokenSilent` , mas o MSAL não conseguiu fazê-lo silenciosamente.

As razões possíveis são:

- precisa de se inscrever
- você precisa consentir
- você precisa passar por uma experiência de autenticação multi-factor.

A remediação é chamar um método interativo `acquireTokenPopup` `acquireTokenRedirect` como:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Ao obter tokens silenciosamente (usando) `acquireTokenSilent` usando MSAL.js, a sua aplicação pode receber erros quando um [desafio de reclamações de acesso condicional,](../azuread-dev/conditional-access-dev-guide.md) como a política de MFA, é exigido por uma API que está a tentar aceder.

O padrão para lidar com este erro é fazer uma chamada interativa para adquirir token em MSAL.js como `acquireTokenPopup` ou como no exemplo `acquireTokenRedirect` seguinte:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

A aquisição interativa do símbolo leva o utilizador e dá-lhes a oportunidade de satisfazer a necessária política de Acesso Condicional.

Ao ligar para uma API que requer acesso condicional, pode receber um desafio de reclamações no erro da API. Neste caso, pode passar as reclamações devolvidas no erro para o `claimsRequest` campo da classe para satisfazer a política `AuthenticationParameters.ts` adequada. 

Consulte [solicitação de reclamações adicionais](active-directory-optional-claims.md) para obter mais detalhes.


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passos seguintes

Considere permitir o [início de Sessão em MSAL.js](msal-logging-js.md) para ajudá-lo a diagnosticar e depurar problemas.
