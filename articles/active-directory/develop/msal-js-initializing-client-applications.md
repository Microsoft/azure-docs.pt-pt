---
title: Inicializar aplicações de clientes MSAL.js / Azure
titleSuffix: Microsoft identity platform
description: Saiba sobre a inicialização das aplicações do cliente utilizando a Biblioteca de Autenticação da Microsoft para javaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fbd700c787a844fa7538ed198f76ed5c06af2c28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010159"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializar aplicações de clientes utilizando MSAL.js
Este artigo descreve a inicialização da Microsoft Authentication Library for JavaScript (MSAL.js) com uma instância de uma aplicação de agente de utilizador. A aplicação de agente de utilizador é uma forma de aplicação do cliente público na qual o código do cliente é executado num utilizador-agente, como um navegador web. Estes clientes não armazenam segredos, uma vez que o contexto do navegador é abertamente acessível. Para saber mais sobre os tipos de aplicação do cliente e as opções de configuração de aplicações, leia a [visão geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar uma aplicação, é necessário [registrá-la](scenario-spa-app-registration.md) primeiro no portal Azure para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID para a sua aplicação)
- O URL do fornecedor de identidade (nome ou a instância) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se estiver a escrever uma aplicação de linha de negócio exclusivamente para a sua organização (também nomeada candidatura de inquilino único).
- Para aplicações web, você também terá que definir o redirectUri onde o fornecedor de identidade vai voltar à sua aplicação com os tokens de segurança.

## <a name="initializing-applications"></a>Aplicações inicializadas

Pode utilizar mSAL.js da seguinte forma numa aplicação JavaScript/Typescript simples. Inicialize o contexto de autenticação MSAL instantaneamente `UserAgentApplication` com um objeto de configuração. O config mínimo necessário para inicializar mSAL.js é o cliente ID da sua aplicação que deve obter do portal de registo de candidaturas.

Para métodos de autenticação`loginRedirect` `acquireTokenRedirect`com fluxos redirecionais (e), em MSAL.js 1.2.x ou anterior, `handleRedirectCallback()` terá de registar explicitamente um callback para o sucesso ou erro através do método. Isto é necessário, uma vez que os fluxos de redirecionamento não devolvem promessas como os métodos com uma experiência pop-up fazem. Isto tornou-se opcional na versão 1.3.0 da MSAL.js.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js foi concebido para ter uma `UserAgentApplication` única instância e configuração do para representar um único contexto de autenticação. Não são recomendados vários casos porque causam entradas e comportamentos de cache conflituosos no navegador.

## <a name="configuration-options"></a>Opções de configuração

MSAL.js tem um objeto de configuração mostrado abaixo que fornece um agrupamento `UserAgentApplication`de opções configuráveis disponíveis para criar uma instância de .

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Abaixo está o conjunto total de opções configuráveis que são suportadas atualmente no objeto de config:

- **clienteID**: Necessário. O cliente ID da sua candidatura, deve obtê-lo no portal de registo de candidaturas.

- **autoridade**: Opcional. Um URL indicando um diretório que a MSAL pode solicitar fichas. O valor `https://login.microsoftonline.com/common`predefinido é: .
    * Em Azure AD, é da&lt;&gt;/&lt;forma&gt;https:// &lt;&gt; audiência de exemplo , onde `https://login.microsoftonline.com`por &lt;&gt; exemplo é o domínio do fornecedor de identidade (por exemplo), e o público é um identificador que representa o público signinte. Estes podem ser os seguintes valores:
        * `https://login.microsoftonline.com/<tenant>`- o arrendatário é um domínio associado ao arrendatário, como contoso.onmicrosoft.com, ou o GUID que representa a `TenantID` propriedade do diretório utilizado apenas para assinar em utilizadores de uma organização específica.
        * `https://login.microsoftonline.com/common`- Usado para assinar em utilizadores com contas de trabalho e escola ou uma conta pessoal da Microsoft.
        * `https://login.microsoftonline.com/organizations/`- Costumava inscrever utentes com contas de trabalho e escola.
        * `https://login.microsoftonline.com/consumers/`- Usado para iniciar sessão em utilizadores com apenas conta pessoal da Microsoft (ao vivo).
    * Em Azure AD B2C, é `https://<instance>/tfp/<tenant>/<policyName>/`do formulário , onde o caso é o domínio Azure AD B2C, ou seja, {o seu nome de inquilino}.b2clogin.com, o inquilino é o nome do inquilino Azure AD B2C i.e. {your-tenant-name}.onmicrosoft.com, policyName é o nome da política B2C a aplicar.


- **validaAutoridade**: Opcional.  Validar o emitente de fichas. A predefinição é `true`. No caso dos pedidos B2C, uma vez que o valor da autoridade é conhecido e pode `false`ser diferente por política, a validação da autoridade não funcionará e tem de ser definida para .

- **redirecionamentoUri**: Opcional.  O URI redirecionado da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs redirecionais registados no portal. Incumprimentos `window.location.href`para .

- **postLogoutRedirectUri**: Opcional.  Redireciona `postLogoutRedirectUri` o utilizador para depois de assinar. O padrão `redirectUri`é .

- **navigateToLoginRequestUrl**: Opcional. Capacidade de desligar a navegação predefinida para iniciar a página após o login. A predefinição é verdadeiro. Isto é usado apenas para redirecionar fluxos.

- **cacheLocalização**: Opcional.  Define o armazenamento `localStorage` do `sessionStorage`navegador para qualquer um ou . A predefinição é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Esta bandeira foi introduzida em MSAL.js v0.2.2 como uma correção para os problemas de loop de [autenticação](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e Microsoft Edge. Permitir que `storeAuthStateInCookie` a bandeira seja verdadeira para tirar partido desta correção. Quando isso estiver ativado, o MSAL.js armazenará o estado de pedido auth necessário para a validação dos fluxos de auth nos cookies do navegador. Por defeito, esta `false`bandeira está definida para .

- **madeireiro**: Opcional.  Um objeto Logger com uma instância de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar registos de forma personalizada. Para mais detalhes sobre a passagem do objeto madeireiro, consulte [a exploração madeireira com msal.js](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes de uma resposta de renovação simbólica da AD Azure deve ser considerado cronometrado. O padrão é de 6 segundos.

- **tokenRenovationOffsetSeconds**: Opcional. O número de milissegundos que define a janela de compensação necessária para renovar o símbolo antes de expirar. O padrão é de 300 milissegundos.

- **navigateFrameWait**: Opcional. O número de milissegundos que define o tempo de espera antes que os iframes escondidos naveguem para o seu destino. O padrão é de 500 milissegundos.

Estes só são aplicáveis a serem passados da biblioteca de invólucros Angulares MSAL:
- **Recursos desprotegidos**: Opcional.  Uma série de URIs que são recursos desprotegidos. A MSAL não anexará um símbolo aos pedidos de saída que possuam estes URI. Incumprimentos `null`para .

- **protectedResourceMap**: Opcional.  Isto está a mapear recursos para os âmbitos utilizados pela MSAL para anexar automaticamente fichas de acesso em chamadas API web. Um único sinal de acesso é obtido para o recurso. Assim, pode mapear um caminho específico dehttps://graph.microsoft.com/v1.0/merecursos da seguinte forma: {" ", ["user.read"]}, ou o URL da aplicação do recurso como: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Isto é necessário para chamadas cors. Incumprimentos `null`para .
