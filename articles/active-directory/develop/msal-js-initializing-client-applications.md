---
title: Inicialize MSAL.js aplicações de clientes | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como rubricar as aplicações do cliente utilizando a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a6d7b760ffd1931fa5dcdb3a67dd02f2798957a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365842"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicialize as aplicações do cliente utilizando MSAL.js

Este artigo descreve a inicialização da Biblioteca de Autenticação do Microsoft para o JavaScript (MSAL.js) com uma instância de uma aplicação de agente de utilizador.

A aplicação de agente de utilizador é uma forma de aplicação de cliente público na qual o código do cliente é executado num agente de utilizador, como um navegador web. Estes clientes não armazenam segredos porque o contexto do navegador é abertamente acessível.

Para saber mais sobre os tipos de aplicações do cliente e opções de configuração de aplicações, consulte [aplicações de clientes públicos e confidenciais no MSAL.](msal-client-applications.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de rubricar uma aplicação, primeiro precisa [de a registar no portal Azure](scenario-spa-app-registration.md), estabelecendo uma relação de confiança entre a sua aplicação e a plataforma de identidade microsoft.

Depois de registar a sua aplicação, vai precisar de alguns ou todos os seguintes valores que podem ser encontrados no portal Azure.

| Valor | Obrigatório | Descrição |
|:----- | :------: | :---------- |
| ID da Aplicação (cliente) | Necessário | Um GUID que identifica exclusivamente a sua aplicação dentro da plataforma de identidade da Microsoft. |
| Autoridade | Opcional | O URL do fornecedor de identidade (o *caso)* e o *público de inscrição* para a sua aplicação. O caso e o público de inscrição, quando concatenated, compõem a *autoridade*. |
| ID do Diretório (inquilino) | Opcional | Especifique isto se estiver a construir uma aplicação de linha de negócio exclusivamente para a sua organização, muitas vezes referida como uma *aplicação de um único inquilino.* |
| URI de Redirecionamento | Opcional | Se estiver a construir uma aplicação web, `redirectUri` especifica onde o fornecedor de identidade (a plataforma de identidade da Microsoft) deve devolver os fichas de segurança que emitiu. |

## <a name="initialize-msaljs-2x-apps"></a>Inicialize MSAL.js aplicações 2.x

Inicialize o contexto de autenticação MSAL instantaneamente através da instantiação de uma [Aplicação PublicClientApplication][msal-js-publicclientapplication] com um objeto [de Configuração.][msal-js-configuration] A propriedade de configuração mínima exigida é a `clientID` da sua aplicação, mostrada como o **ID da Aplicação (cliente)** na página **geral** do registo da aplicação no portal Azure.

Aqui está um objeto de configuração de exemplo e instantâneo de um `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Invoque [o handleRedirectPromise][msal-js-handleredirectpromise] quando a sua aplicação utilizar os fluxos de redirecionamento. Ao utilizar os fluxos de redireccionamento, `handleRedirectPromise` deve ser executado em cada carga de página.

Há três resultados possíveis da promessa:

- `.then` é invocado e `tokenResponse` é verdade: A aplicação está a regressar de uma operação de redirecionamento que foi bem sucedida.
- `.then` é invocado e `tokenResponse` é falsidade `null` (): O pedido não regressa de uma operação de redireccionamento.
- `.catch` é invocado: A aplicação está a regressar de uma operação de redirecionamento e houve um erro.

## <a name="initialize-msaljs-1x-apps"></a>Inicialize MSAL.js aplicações 1.x

Inicialize o contexto de autenticação MSAL 1.x instantaneamente através da instantânea [aplicação do UserAgentApplication][msal-js-useragentapplication] com um objeto de configuração. A propriedade de configuração mínima exigida é a `clientID` da sua aplicação, mostrada como o **ID da Aplicação (cliente)** na página **geral** do registo da aplicação no portal Azure.

Para métodos de autenticação com fluxos de redireccionamento[(loginRedirect][msal-js-loginredirect] e [adquirirTokenRedirect)][msal-js-acquiretokenredirect]em MSAL.js 1.2.x ou mais cedo, deve registar explicitamente uma chamada de retorno para sucesso ou erro através do `handleRedirectCallback()` método. O registo explícito da chamada é necessário em MSAL.js 1.2.x e mais cedo porque os fluxos de redirecionamento não devolvem promessas como os métodos com uma experiência pop-up fazem. Registar a chamada é *opcional* na versão 1.3.x MSAL.js e posterior.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Instância única e configuração

Ambos MSAL.js 1.x e 2.x são projetados para ter uma única instância e configuração do `UserAgentApplication` `PublicClientApplication` ou, respectivamente, para representar um único contexto de autenticação.

Várias instâncias de `UserAgentApplication` ou `PublicClientApplication` não são recomendadas, uma vez que causam entradas e comportamentos de cache conflituosos no navegador.

## <a name="next-steps"></a>Passos seguintes

Esta MSAL.js amostra de código de 2.x no GitHub demonstra a instantiação de uma [Aplicação deClient Público][msal-js-publicclientapplication] com um objeto [de configuração:][msal-js-configuration]

[Azure-Samples/ms-identidade-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
