---
title: Início de sessão único (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba mais sobre a criação de experiências de início de início de sessão únicas com a biblioteca de autenticação da Microsoft para JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
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
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075854"
---
# <a name="single-sign-on-with-msaljs"></a>Início de sessão único com msal

Único início de sessão (SSO) permite que os utilizadores introduzam as respetivas credenciais de uma vez para iniciar sessão e estabelecer uma sessão que pode ser reutilizada por vários aplicativos sem a necessidade de autenticar novamente. Isto fornece uma experiência perfeita ao usuário e reduz os prompts repetidos de credenciais.

O Azure AD fornece capacidades SSO para aplicações através da definição de um cookie de sessão quando o utilizador autentica pela primeira vez. A biblioteca de msal permite que os aplicativos para utilizá-la de diversas formas.

## <a name="sso-between-browser-tabs"></a>SSO entre separadores de browser

Quando seu aplicativo é aberto na vários separadores e entrar pela primeira vez o utilizador numa guia, o utilizador também é assinado nos outros separadores sem inserir. O token de ID para o utilizador no browser coloca em cache da msal `localStorage` e irá iniciar sessão do utilizador para a aplicação das outras guias abertas.

Por predefinição, utiliza a msal `sessionStorage` que não permite que a sessão seja compartilhado entre separadores. Para obter o SSO entre separadores, certifique-se de definir o `cacheLocation` em msal para `localStorage` conforme mostrado abaixo.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO entre aplicações

Quando um utilizador efetua a autenticação, um cookie de sessão é definido no domínio do Azure AD no browser. Msal conta com esse cookie de sessão para fornecer SSO para o usuário entre aplicativos diferentes. Msal também coloca em cache os tokens de ID e tokens de acesso do utilizador no armazenamento do navegador por domínio de aplicativo. Como resultado, o comportamento SSO varia para diferentes casos:  

### <a name="applications-on-the-same-domain"></a>Aplicações no mesmo domínio

Quando os aplicativos são hospedados no mesmo domínio, o utilizador pode iniciar sessão numa aplicação uma vez e, em seguida, autenticado para as outras aplicações sem uma linha de comandos. Os tokens em cache para o utilizador no domínio fornecer SSO tira partido de msal.

### <a name="applications-on-different-domain"></a>Aplicações no domínio diferente

Quando os aplicativos são hospedados em domínios diferentes, os tokens em cache no domínio A não podem ser acedidos por msal no domínio B.

Isso significa que quando os utilizadores iniciar sessão no domínio navegar até a uma aplicação no domínio B, eles serão redirecionados ou pedidos com a página do Azure AD. Uma vez que o Azure AD ainda tem o cookie de sessão do utilizador, irá iniciar a sessão do utilizador e não terão de reintroduzir as credenciais. Se o utilizador tiver várias contas de utilizador numa sessão com o Azure AD, o usuário será solicitado a escolher a conta relevante para iniciar sessão.

### <a name="automatically-select-account-on-azure-ad"></a>Selecionar automaticamente a conta no Azure AD

Em certos casos, o aplicativo tem acesso ao contexto de autenticação do usuário e quer evitar a linha de comandos de seleção de conta da Azure AD quando várias contas com sessão iniciadas.  Isso pode ser feito várias formas diferentes:

**Com o ID de sessão (SID)**

ID de sessão é um [afirmação opcional](active-directory-optional-claims.md) que podem ser configuradas nos tokens de ID. Esta afirmação permite que o aplicativo identificar Azure o utilizador sessão do AD independentemente do nome da conta do utilizador ou nome de utilizador. Pode passar o SID nos parâmetros do pedido para o `acquireTokenSilent` chamar. Isso permitirá que o Azure AD para ignorar a seleção de conta. SID está vinculado ao cookie de sessão e não para várias contextos de navegador.

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
> SID pode ser usado apenas com pedidos de autenticação silenciosa efetuados por `acquireTokenSilent` chamar msal.
Pode encontrar os passos para configurar afirmações opcionais no seu manifesto de aplicativo [aqui](active-directory-optional-claims.md).

**Utilizando a sugestão de início de sessão**

Se não tiver configurado de afirmação ou precisar de ignorar o prompt de seleção de conta em chamadas de autenticação interativa de SID, pode fazê-ao fornecer uma `login_hint` nos parâmetros do pedido e, opcionalmente, um `domain_hint` como `extraQueryParameters` na msal métodos interativos (`loginPopup`, `loginRedirect`, `acquireTokenPopup` e `acquireTokenRedirect`). Por exemplo:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Pode obter os valores para login_hint e domain_hint lendo as afirmações devolvidas num token de ID para o utilizador.

* **loginHint** deve ser definido como o `preferred_username` de afirmações no token de ID.

* **domain_hint** é necessário apenas para ser transmitidos ao utilizar o /common autoridade. A sugestão de domínio é determinada pelo inquilino ID(tid).  Se o `tid` declaração no token de ID é `9188040d-6c67-4c5b-b112-36a304b66dad` é consumidores. Caso contrário, é que as organizações.

Leia [aqui](v2-oauth2-implicit-grant-flow.md) para obter mais informações sobre os valores para a sugestão de início de sessão e a sugestão de domínio.

> [!Note]
> Não é possível passar o SID e login_hint ao mesmo tempo. Esta ação resulta na resposta de erro.

## <a name="sso-without-msaljs-login"></a>SSO sem início de sessão de msal

Por predefinição, a msal requer que um método de início de sessão é chamado para estabelecer um contexto de usuário antes de obter tokens para APIs. Uma vez que os métodos de início de sessão são interativos, o utilizador verá uma linha de comandos.

Há determinados casos em que os aplicativos têm acesso ao contexto do usuário autenticado ou token de ID através da autenticação iniciadas em outro aplicativo e pretendem tirar partido do SSO para adquirir tokens sem primeiro iniciar sessão através de msal.

Um exemplo disso é: Um usuário está conectado a uma aplicação web de principal que aloja outra aplicação do JavaScript em execução como um suplemento ou plug-in.

A experiência SSO neste cenário pode ser obtida da seguinte forma:

Passar o `sid` se estiver disponível (ou `login_hint` e, opcionalmente `domain_hint`) como parâmetros para a msal do pedido `acquireTokenSilent` chamar da seguinte forma:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO no ADAL.js para atualização de msal

Msal traz paridade de funcionalidades com ADAL.js para cenários de autenticação do Azure AD. Para facilitar a migração de ADAL.js a msal para evitar avisar os seus utilizadores para iniciar sessão novamente, a biblioteca lê o token de ID que representa a sessão do utilizador na cache de ADAL.js e perfeitamente inicia a sessão do utilizador no msal.  

Para tirar partido do comportamento do logon único (SSO), ao atualizar do ADAL.js, terá de garantir que estão a utilizar as bibliotecas `localStorage` para colocar em cache de tokens. Definir o `cacheLocation` para `localStorage` na configuração de msal e ADAL.js na inicialização da seguinte forma:


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
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Assim que este é configurado, msal será capaz de ler o estado em cache do usuário autenticado no ADAL.js e usá-lo para fornecer SSO no msal.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [único início de sessão em sessão e a duração do token](active-directory-configurable-token-lifetimes.md) valores no Azure AD.
