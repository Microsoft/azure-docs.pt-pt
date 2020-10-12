---
title: Único sign-on (MSAL.js) / Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir experiências de assinatura única utilizando a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js).
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84690783"
---
# <a name="single-sign-on-with-msaljs"></a>Início de sessão único com MSAL.js

O single Sign-On (SSO) permite que os utilizadores introduzam as suas credenciais uma vez para iniciar sessão e estabelecer uma sessão que pode ser reutilizada em várias aplicações sem precisar de autenticar novamente. Isto proporciona uma experiência perfeita ao utilizador e reduz as repetidas solicitações para credenciais.

O Azure AD fornece capacidades SSO às aplicações, definindo um cookie de sessão quando o utilizador autenticar pela primeira vez. A biblioteca MSAL.js permite que as aplicações aproveitem isto de algumas formas.

## <a name="sso-between-browser-tabs"></a>SSO entre separadores de navegador

Quando a sua aplicação está aberta em vários separadores e inicia primeiro o seu súmis num separador, o utilizador também é inscrito nos outros separadores sem ser solicitado. MSAL.js caches o símbolo de ID para o utilizador no navegador `localStorage` e irá inscrever o utilizador na aplicação nos outros separadores abertos.

Por predefinição, MSAL.js utiliza `sessionStorage` que não permite que a sessão seja partilhada entre separadores. Para obter SSO entre separadores, certifique-se de definir o `cacheLocation` in MSAL.js para os `localStorage` mostrados abaixo.

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

Quando um utilizador autentica, é definido um cookie de sessão no domínio AZure AD no navegador. MSAL.js conta com este cookie de sessão para fornecer SSO para o utilizador entre diferentes aplicações. MSAL.js também caches os tokens de ID e tokens de acesso do utilizador no armazenamento do navegador por domínio de aplicação. Como resultado, o comportamento SSO varia para diferentes casos:  

### <a name="applications-on-the-same-domain"></a>Aplicações no mesmo domínio

Quando as aplicações são hospedadas no mesmo domínio, o utilizador pode iniciar sessão numa aplicação uma vez e depois autenticar-se nas outras aplicações sem uma solicitação. MSAL.js aproveita os tokens em cache para o utilizador no domínio fornecer SSO.

### <a name="applications-on-different-domain"></a>Aplicações em diferentes domínios

Quando as aplicações são hospedadas em diferentes domínios, os tokens em cache no domínio A não podem ser acedidos por MSAL.js no domínio B.

Isto significa que quando os utilizadores se inscrevem no domínio A navegar para uma aplicação no domínio B, serão redirecionados ou solicitados com a página AD do Azure. Uma vez que o Azure AD ainda tem o cookie de sessão de utilizador, ele irá assinar no utilizador e não terá de voltar a introduzir as credenciais. Se o utilizador tiver várias contas de utilizador em sessão com a Azure AD, o utilizador será solicitado a escolher a conta relevante para iniciar sessão.

### <a name="automatically-select-account-on-azure-ad"></a>Selecione automaticamente a conta no Azure AD

Em certos casos, a aplicação tem acesso ao contexto de autenticação do utilizador e pretende evitar o pedido de seleção de conta AZure AD quando várias contas são assinadas.  Isto pode ser feito de várias maneiras:

**Utilizando o ID da sessão (SID)**

O ID da sessão é uma [reivindicação opcional](active-directory-optional-claims.md) que pode ser configurada nos tokens de ID. Esta alegação permite que a aplicação identifique a sessão Azure AD do utilizador independentemente do nome de conta do utilizador ou nome de utilizador. Pode passar o SID nos parâmetros de pedido para a `acquireTokenSilent` chamada. Isto permitirá ao Azure AD contornar a seleção da conta. O SID está ligado ao cookie da sessão e não cruzará os contextos do navegador.

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
> SID só pode ser utilizado com pedidos de autenticação silenciosa esquisitices feitos por `acquireTokenSilent` chamada em MSAL.js.
Pode encontrar aqui os passos para configurar reclamações opcionais no seu [manifesto](active-directory-optional-claims.md)de candidatura.

**Usando dica de login**

Se não tiver a reclamação sid configurada ou precisar de contornar o pedido de seleção de conta em chamadas de autenticação interativa, pode fazê-lo fornecendo um `login_hint` nos parâmetros de pedido e opcionalmente como `domain_hint` nos `extraQueryParameters` métodos interativos MSAL.js `loginPopup` `loginRedirect` (, e `acquireTokenPopup` `acquireTokenRedirect` ). Por exemplo:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Pode obter os valores para login_hint e domain_hint através da leitura das reclamações devolvidas no token de ID para o utilizador.

* **loginHint** deve ser definido para a `preferred_username` reclamação no token de ID.

* **domain_hint** só é necessário passar quando se utiliza a autoridade /comum. A dica de domínio é determinada pelo ID do inquilino(tid).  Se a `tid` reclamação no símbolo de identificação é `9188040d-6c67-4c5b-b112-36a304b66dad` que são os consumidores. Caso contrário, são organizações.

Leia [aqui](v2-oauth2-implicit-grant-flow.md) mais informações sobre os valores para dica de login e sugestão de domínio.

> [!Note]
> Não pode passar sid e login_hint ao mesmo tempo. Isto resultará numa resposta de erro.

## <a name="sso-without-msaljs-login"></a>SSO sem MSAL.js login

Por design, MSAL.js requer que um método de login seja chamado para estabelecer um contexto de utilizador antes de obter fichas para APIs. Uma vez que os métodos de login são interativos, o utilizador vê uma solicitação.

Existem certos casos em que as aplicações têm acesso ao contexto do utilizador autenticado ou ficha de ID através da autenticação iniciada noutra aplicação e pretendem aproveitar a SSO para adquirir fichas sem iniciar a sua assinatura através de MSAL.js.

Um exemplo disso é: Um utilizador é assinado numa aplicação web dos pais que acolhe outra aplicação JavaScript em execução como um add-on ou plugin.

A experiência SSO neste cenário pode ser alcançada da seguinte forma:

Passe o `sid` se disponível (ou `login_hint` `domain_hint` opcionalmente) como parâmetros de pedido para a chamada MSAL.js `acquireTokenSilent` seguintes:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO em ADAL.js para MSAL.js atualização

MSAL.js traz paridade de funcionalidades com ADAL.js para cenários de autenticação AD Azure. Para tornar a migração de ADAL.js fácil para MSAL.js e para evitar que os seus utilizadores voltem a fazer sessão, a biblioteca lê o sinal de ID que representa a sessão do utilizador em cache ADAL.js e sinais perfeitamente no utilizador em MSAL.js.  

Para tirar partido do comportamento de assinatura única (SSO) ao atualizar a partir de ADAL.js, terá de garantir que as bibliotecas estão a usar `localStorage` para caching tokens. Desa esta medida `cacheLocation` `localStorage` na configuração MSAL.js e ADAL.js na inicialização da seguinte forma:


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

Uma vez configurado, MSAL.js poderão ler o estado em cache do utilizador autenticado em ADAL.js e usá-lo para fornecer SSO em MSAL.js.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [sessão de inscrição única e](active-directory-configurable-token-lifetimes.md) os valores de vida simbólicos em Azure AD.
