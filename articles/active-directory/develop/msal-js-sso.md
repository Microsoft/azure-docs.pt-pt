---
title: Logon único (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre como criar experiências de logon único usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 921c02e682c722a4e96f98fb0fc54d7fcbb82220
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916269"
---
# <a name="single-sign-on-with-msaljs"></a>Início de sessão único com MSAL.js

O SSO (logon único) permite que os usuários insiram suas credenciais uma vez para entrar e estabelecer uma sessão que pode ser reutilizada em vários aplicativos sem a necessidade de se autenticar novamente. Isso fornece uma experiência direta ao usuário e reduz as solicitações repetidas de credenciais.

O Azure AD fornece recursos de SSO para aplicativos definindo um cookie de sessão quando o usuário é autenticado pela primeira vez. A biblioteca MSAL. js permite que os aplicativos aproveitem isso de algumas maneiras.

## <a name="sso-between-browser-tabs"></a>SSO entre guias do navegador

Quando seu aplicativo estiver aberto em várias guias e você entrar primeiro no usuário em uma guia, o usuário também será conectado nas outras guias sem ser solicitado. O MSAL. js armazena em cache o token de ID para o usuário no navegador `localStorage` e o conectará ao aplicativo nas outras guias abertas.

Por padrão, o MSAL. js usa `sessionStorage` que não permite que a sessão seja compartilhada entre as guias. Para obter o SSO entre as guias, certifique-se de definir o `cacheLocation` em MSAL. js como `localStorage` conforme mostrado abaixo.

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

## <a name="sso-between-apps"></a>SSO entre aplicativos

Quando um usuário é autenticado, um cookie de sessão é definido no domínio do Azure AD no navegador. O MSAL. js conta com esse cookie de sessão para fornecer o SSO para o usuário entre diferentes aplicativos. O MSAL. js também armazena em cache os tokens de ID e os tokens de acesso do usuário no armazenamento do navegador por domínio do aplicativo. Como resultado, o comportamento de SSO varia para casos diferentes:  

### <a name="applications-on-the-same-domain"></a>Aplicativos no mesmo domínio

Quando os aplicativos são hospedados no mesmo domínio, o usuário pode entrar em um aplicativo uma vez e, em seguida, ser autenticado para os outros aplicativos sem um prompt. O MSAL. js aproveita os tokens armazenados em cache para o usuário no domínio para fornecer o SSO.

### <a name="applications-on-different-domain"></a>Aplicativos em um domínio diferente

Quando os aplicativos são hospedados em domínios diferentes, os tokens armazenados em cache no domínio A não podem ser acessados pelo MSAL. js no domínio B.

Isso significa que, quando os usuários entrarem no domínio A navegarem para um aplicativo no domínio B, eles serão redirecionados ou solicitados com a página do Azure AD. Como o Azure AD ainda tem o cookie de sessão do usuário, ele entrará no usuário e não precisará inserir novamente as credenciais. Se o usuário tiver várias contas de usuário na sessão com o Azure AD, será solicitado que o usuário escolha a conta relevante para entrar.

### <a name="automatically-select-account-on-azure-ad"></a>Selecionar automaticamente a conta no Azure AD

Em determinados casos, o aplicativo tem acesso ao contexto de autenticação do usuário e deseja evitar o prompt de seleção de conta do Azure AD quando várias contas são conectadas.  Isso pode ser feito de algumas maneiras diferentes:

**Usando a ID de sessão (SID)**

A ID de sessão é uma [declaração opcional](active-directory-optional-claims.md) que pode ser configurada nos tokens de ID. Essa declaração permite que o aplicativo identifique a sessão do Azure AD do usuário independentemente do nome da conta do usuário ou do nome de usuário. Você pode passar o SID nos parâmetros de solicitação para a chamada `acquireTokenSilent`. Isso permitirá que o Azure AD ignore a seleção de conta. O SID está associado ao cookie de sessão e não conterá contextos de navegador.

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
> O SID pode ser usado somente com solicitações de autenticação silenciosa feitas por `acquireTokenSilent` chamada em MSAL. js.
Você pode encontrar as etapas para configurar declarações opcionais no manifesto do aplicativo [aqui](active-directory-optional-claims.md).

**Usando a dica de logon**

Se você não tiver a declaração de SID configurada ou precisar ignorar o prompt de seleção de conta nas chamadas de autenticação interativas, poderá fazer isso fornecendo um `login_hint` nos parâmetros de solicitação e, opcionalmente, um `domain_hint` como `extraQueryParameters` nos métodos interativos do MSAL. js (`loginPopup`, `loginRedirect`, `acquireTokenPopup` e `acquireTokenRedirect`). Por exemplo:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Você pode obter os valores para login_hint e domain_hint lendo as declarações retornadas no token de ID para o usuário.

* **loginHint** deve ser definido como a declaração de `preferred_username` no token de ID.

* **domain_hint** só precisa ser passada ao usar a autoridade/Common. A dica de domínio é determinada pela ID de locatário (TID).  Se a declaração de `tid` no token de ID for `9188040d-6c67-4c5b-b112-36a304b66dad` os consumidores. Caso contrário, é organizações.

Leia [aqui](v2-oauth2-implicit-grant-flow.md) para obter mais informações sobre os valores de dica de logon e dica de domínio.

> [!Note]
> Não é possível passar SID e login_hint ao mesmo tempo. Isso resultará em resposta de erro.

## <a name="sso-without-msaljs-login"></a>SSO sem logon MSAL. js

Por design, o MSAL. js requer que um método de logon seja chamado para estabelecer um contexto de usuário antes de obter tokens para APIs. Como os métodos de logon são interativos, o usuário vê um prompt.

Há certos casos em que os aplicativos têm acesso ao contexto do usuário autenticado ou ao token de ID por meio da autenticação iniciada em outro aplicativo e desejam aproveitar o SSO para adquirir tokens sem primeiro entrar por meio de MSAL. js.

Um exemplo disso é: um usuário está conectado a um aplicativo Web pai que hospeda outro aplicativo JavaScript em execução como um complemento ou plug-in.

A experiência de SSO neste cenário pode ser obtida da seguinte maneira:

Passe o `sid` se disponível (ou `login_hint` e, opcionalmente, `domain_hint`) como parâmetros de solicitação para a chamada de MSAL. js `acquireTokenSilent` da seguinte maneira:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO na atualização do ADAL. js para o MSAL. js

O MSAL. js traz a paridade de recursos com o ADAL. js para cenários de autenticação do Azure AD. Para tornar a migração do ADAL. js para o MSAL. js fácil e evitar solicitar que os usuários entrem novamente, a biblioteca lê o token de ID que representa a sessão do usuário no cache ADAL. js e entra diretamente no usuário em MSAL. js.  

Para aproveitar o comportamento de SSO (logon único) ao atualizar do ADAL. js, você precisará garantir que as bibliotecas estejam usando `localStorage` para os tokens de cache. Defina o `cacheLocation` como `localStorage` na configuração MSAL. js e ADAL. js na inicialização da seguinte maneira:


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

Quando isso estiver configurado, o MSAL. js poderá ler o estado de cache do usuário autenticado no ADAL. js e usá-lo para fornecer o SSO no MSAL. js.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [sessão de logon único e](active-directory-configurable-token-lifetimes.md) os valores de tempo de vida do token no Azure AD.
