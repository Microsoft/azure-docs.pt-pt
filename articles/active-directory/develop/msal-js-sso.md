---
title: Inscrição única (MSAL.js) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a construção de experiências de inscrição únicas utilizando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
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
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695879"
---
# <a name="single-sign-on-with-msaljs"></a>Início de sessão único com MSAL.js

O Single Sign-On (SSO) permite que os utilizadores introduzam as suas credenciais uma vez que assinem e estabeleçam uma sessão que pode ser reutilizada em várias aplicações sem exigir que se autentiquem novamente. Isto proporciona uma experiência perfeita ao utilizador e reduz as solicitações repetidas de credenciais.

A Azure AD fornece capacidades SSO às aplicações, definindo um cookie de sessão quando o utilizador autentica pela primeira vez. A biblioteca MSAL.js permite que as aplicações o aproveitem de várias maneiras.

## <a name="sso-between-browser-tabs"></a>SSO entre separadores de navegador

Quando a sua aplicação está aberta em vários separadores e assina pela primeira vez no utilizador num separador, o utilizador também é inscrito nos outros separadores sem ser solicitado. MSAL.js caches o token ID para o utilizador no navegador `localStorage` e irá assinar o utilizador na aplicação nos outros separadores abertos.

Por predefinição, a MSAL.js utiliza `sessionStorage` que não permite que a sessão seja partilhada entre separadores. Para obter SSO entre separadores, certifique-se de definir o `cacheLocation` em MSAL.js para `localStorage` como mostrado abaixo.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO entre apps

Quando um utilizador autentica, um cookie de sessão é definido no domínio AD Azure no navegador. MSAL.js conta com este cookie de sessão para fornecer SSO para o utilizador entre diferentes aplicações. MSAL.js também caches as fichas de ID e tokens de acesso do utilizador no armazenamento do navegador por domínio de aplicação. Como resultado, o comportamento sso varia para diferentes casos:  

### <a name="applications-on-the-same-domain"></a>Aplicações no mesmo domínio

Quando as aplicações são hospedadas no mesmo domínio, o utilizador pode iniciar sessão uma vez numa aplicação e depois ser autenticado nas outras aplicações sem qualquer aviso. MSAL.js aproveita as fichas em cache para o utilizador no domínio fornecer SSO.

### <a name="applications-on-different-domain"></a>Aplicações em domíniodiferente

Quando as aplicações são alojadas em diferentes domínios, as fichas emcache no domínio A não podem ser acedidas por MSAL.js no domínio B.

Isto significa que quando os utilizadores que assinaram no domínio A navegar para uma aplicação no domínio B, serão redirecionados ou solicitados com a página AD Azure. Uma vez que o Azure AD ainda tem o cookie de sessão de utilizador, irá assinar no utilizador e não terá de reintroduzir as credenciais. Se o utilizador tiver várias contas de utilizador em sessão com a AD Azure, o utilizador será solicitado a escolher a conta relevante para iniciar sessão.

### <a name="automatically-select-account-on-azure-ad"></a>Selecione automaticamente a conta em Azure AD

Em certos casos, a aplicação tem acesso ao contexto de autenticação do utilizador e pretende evitar o pedido de seleção de conta Azure AD quando várias contas são assinadas.  Isto pode ser feito de várias maneiras diferentes:

**Utilização do ID da sessão (SID)**

O ID da sessão é uma [reivindicação opcional](active-directory-optional-claims.md) que pode ser configurada nas fichas de identificação. Esta alegação permite que a aplicação identifique a sessão de AD Azure do utilizador independentemente do nome de conta ou nome de utilizador do utilizador. Pode passar o SID nos parâmetros de pedido para a chamada `acquireTokenSilent`. Isto permitirá que a Azure AD ignore a seleção da conta. Sid está ligado ao cookie da sessão e não vai cruzar os contextos do navegador.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> Sid só pode ser usado com pedidos de autenticação silenciosa feitos por `acquireTokenSilent` chamada em MSAL.js.
Pode encontrar aqui os passos para configurar reclamações opcionais no seu [manifesto](active-directory-optional-claims.md)de candidatura .

**Usando a dica de login**

Se não tiver a reclamação sid configurada ou precisar de contornar o pedido de seleção de conta em chamadas de autenticação interativa, pode fazê-lo fornecendo uma `login_hint` nos parâmetros de pedido e opcionalmente uma `domain_hint` como `extraQueryParameters` nos métodos interativos MSAL.js (`loginPopup`, `loginRedirect`, `acquireTokenPopup` e `acquireTokenRedirect`). Por exemplo:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Pode obter os valores para login_hint e domain_hint lendo as reclamações devolvidas no token de ID para o utilizador.

* **loginHint** deve ser definido para a `preferred_username` reclamação no token ID.

* **domain_hint** só é necessário passar quando utilizar a /autoridade comum. A dica de domínio é determinada pelo ID do inquilino(tid).  Se a alegação `tid` no token de identificação for `9188040d-6c67-4c5b-b112-36a304b66dad` são os consumidores. Caso contrário, são organizações.

Leia [aqui](v2-oauth2-implicit-grant-flow.md) mais informações sobre os valores para indicação de login e sugestão de domínio.

> [!Note]
> Não pode passar por SID e login_hint ao mesmo tempo. Isto resultará numa resposta de erro.

## <a name="sso-without-msaljs-login"></a>SSO sem login MSAL.js

Por design, MSAL.js requer que um método de login seja chamado para estabelecer um contexto de utilizador antes de obter fichas para APIs. Uma vez que os métodos de login são interativos, o utilizador vê um pedido.

Existem certos casos em que as aplicações têm acesso ao contexto ou ficha de identificação do utilizador autenticado através da autenticação iniciada noutra aplicação e pretendem alavancar o SSO para adquirir fichas sem primeiro assinar através do MSAL.js.

Um exemplo disso é: Um utilizador é assinado numa aplicação web-mãe que acolhe outra aplicação JavaScript funcionando como um addon ou plugin.

A experiência SSO neste cenário pode ser alcançada da seguinte forma:

Passe a `sid` se disponível (ou `login_hint` e opcionalmente `domain_hint`) como parâmetros de pedido para o MSAL.js `acquireTokenSilent` chamada da seguinte forma:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO em ADAL.js para atualização MSAL.js

MSAL.js traz paridade de recursos com ADAL.js para cenários de autenticação Azure AD. Para facilitar a migração de ADAL.js para MSAL.js e evitar que os seus utilizadores voltem a inscrever-se, a biblioteca lê o símbolo de identificação que representa a sessão do utilizador em cache ADAL.js e assina perfeitamente no utilizador em MSAL.js.  

Para aproveitar o comportamento de inscrição única (SSO) ao atualizar a partir de ADAL.js, terá de garantir que as bibliotecas estão a usar `localStorage` para fichas de cache. Deteto a `cacheLocation` para `localStorage` tanto na configuração MSAL.js como na configuração ADAL.js na inicialização da seguinte forma:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Uma vez configurado, o MSAL.js poderá ler o estado cached do utilizador autenticado em ADAL.js e usá-lo para fornecer SSO em MSAL.js.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [única sessão de inscrição e](active-directory-configurable-token-lifetimes.md) valores de vida simbólicos em Azure AD.
