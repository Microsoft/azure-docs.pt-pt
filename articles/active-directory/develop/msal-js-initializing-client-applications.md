---
title: Inicializar MSAL.js aplicações de clientes [ Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como rubricar as aplicações do cliente utilizando a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js).
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81010159"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicialize as aplicações do cliente utilizando MSAL.js
Este artigo descreve a inicialização da Biblioteca de Autenticação do Microsoft para o JavaScript (MSAL.js) com uma aplicação de agente de utilizador. A aplicação de agente de utilizador é uma forma de aplicação de cliente público na qual o código do cliente é executado num agente de utilizador, como um navegador web. Estes clientes não armazenam segredos, uma vez que o contexto do navegador é abertamente acessível. Para saber mais sobre os tipos de aplicação do cliente e opções de configuração de aplicações, leia a [visão geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de rubricar uma aplicação, primeiro precisa [de a registar no portal Azure](scenario-spa-app-registration.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID para a sua aplicação)
- O URL do fornecedor de identidade (nomeado o caso) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como a autoridade.
- O iD do inquilino se estiver a escrever uma aplicação de linha de negócio apenas para a sua organização (também nomeado pedido de inquilino único).
- Para aplicações web, você também terá que definir o redirectUri onde o fornecedor de identidade vai voltar à sua aplicação com os tokens de segurança.

## <a name="initializing-applications"></a>Inicialização de candidaturas

Pode utilizar MSAL.js da seguinte forma numa aplicação JavaScript/Typescript simples. Inicialize o contexto de autenticação MSAL instantaneamente `UserAgentApplication` com um objeto de configuração. O mínimo necessário para a config para inicializar MSAL.js é o clienteID da sua aplicação que deve obter do portal de registo de candidaturas.

Para métodos de autenticação com fluxos de redireccionamento `loginRedirect` `acquireTokenRedirect` (e), em MSAL.js 1.2.x ou mais cedo, terá de registar explicitamente uma chamada de retorno para o sucesso ou erro através do `handleRedirectCallback()` método. Isto é necessário, uma vez que os fluxos de redirecionamento não devolvem promessas como fazem os métodos com uma experiência pop-up. Isto tornou-se opcional na versão 1.3.0 MSAL.js.

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

MSAL.js é projetado para ter uma única instância e configuração do `UserAgentApplication` para representar um único contexto de autenticação. Várias instâncias não são recomendadas, uma vez que causam entradas e comportamentos de cache conflituosos no navegador.

## <a name="configuration-options"></a>Opções de configuração

MSAL.js tem um objeto de configuração mostrado abaixo que fornece um agrupamento de opções configuráveis disponíveis para criar um exemplo de `UserAgentApplication` .

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

Abaixo está o conjunto total de opções configuráveis que são suportadas atualmente no objeto config:

- **clienteID**: Obrigatório. O clienteID da sua candidatura, deverá obtê-lo no portal de registo de candidaturas.

- **autoridade**: Opcional. Um URL indicando um diretório que o MSAL pode solicitar fichas. O valor predefinido é: `https://login.microsoftonline.com/common` .
    * Em Azure AD, é da forma https:// &lt; audiência de exemplo , onde o caso é o domínio do fornecedor de &gt; / &lt; identidade &gt; &lt; &gt; (por exemplo, `https://login.microsoftonline.com` ) e o público é um &lt; &gt; identificador que representa o público de inscrição. Estes podem ser os seguintes valores:
        * `https://login.microsoftonline.com/<tenant>`- o arrendatário é um domínio associado ao arrendatário, como contoso.onmicrosoft.com, ou o GUID que representa a `TenantID` propriedade do diretório utilizado apenas para assinar nos utilizadores de uma organização específica.
        * `https://login.microsoftonline.com/common`- Costumava inscrever-se em utilizadores com contas de trabalho e escola ou uma conta pessoal da Microsoft.
        * `https://login.microsoftonline.com/organizations/`- Costumava inscrever-se em utilizadores com contas de trabalho e escola.
        * `https://login.microsoftonline.com/consumers/`- Costumava assinar em utilizadores apenas com conta pessoal da Microsoft (ao vivo).
    * Em Azure AD B2C, é do formulário `https://<instance>/tfp/<tenant>/<policyName>/` , onde o caso é o domínio Azure AD B2C, ou seja, {your-tenant-name}.b2clogin.com, inquilino é o nome do inquilino Azure AD B2C i.e. {your-tenant-name}.onmicrosoft.com, policyName é o nome da política B2C para aplicar.


- **validar aauthority**: Opcional.  Valide o emitente de fichas. A predefinição é `true`. Para os pedidos B2C, uma vez que o valor da autoridade é conhecido e pode ser diferente por política, a validação da autoridade não funcionará e tem de ser definida para `false` .

- **redirectUri**: Opcional.  O URI redirecionado da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs de redirecionamento que registou no portal. Incumprimentos a `window.location.href` .

- **postLogoutRedirectUri**: Opcional.  Redireciona o utilizador para `postLogoutRedirectUri` depois de assinar. O padrão é `redirectUri` .

- **navegarToLoginRequestUrl**: Opcional. Capacidade de desligar a navegação predefinida para iniciar a página após o início do login. A predefinição é verdadeiro. Isto é usado apenas para fluxos de redirecionamento.

- **cacheLocation**: Opcional.  Define o armazenamento do navegador para qualquer um `localStorage` ou `sessionStorage` . A predefinição é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Esta bandeira foi introduzida em MSAL.js v0.2.2 como uma correção para os problemas do ciclo de [autenticação](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e Microsoft Edge. Permita que a bandeira `storeAuthStateInCookie` seja verdadeira para aproveitar esta correção. Quando isto estiver ativado, MSAL.js armazenará o estado de pedido auth necessário para validação dos fluxos de auth nos cookies do navegador. Por predefinição, esta bandeira está definida para `false` .

- **madeir**: Opcional.  Um objeto Logger com uma instância de retorno que pode ser fornecida pelo desenvolvedor para consumir e publicar registos de forma personalizada. Para obter mais informações sobre a passagem do objeto do madeirm, consulte [o registo com msal.js](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes de uma resposta simbólica de renovação da Azure AD deve ser considerado cronometrado. O padrão é de 6 segundos.

- **tokenRenewalOffsetsSeconds**: Opcional. O número de milissegundos que define a janela de compensação necessária para renovar o token antes de expirar. O padrão é de 300 milissegundos.

- **navigateFrameWait**: Opcional. O número de milissegundos que define o tempo de espera antes de se esconder se ramses navegar para o seu destino. O padrão é de 500 milissegundos.

Estes só são aplicáveis para serem passados a partir da biblioteca de invólucros MSAL Angular:
- **Recursos desprotegidos**: Opcional.  Conjunto de URIs que são recursos desprotegidos. A MSAL não anexará um símbolo aos pedidos de saída que tenham estes URI. Incumprimentos a `null` .

- **protectedResourceMap**: Opcional.  Isto é mapear recursos para os âmbitos utilizados pela MSAL para anexar automaticamente fichas de acesso em chamadas web API. Um único sinal de acesso é obtido para o recurso. Assim, pode mapear um caminho específico de recursos da seguinte forma: {" https://graph.microsoft.com/v1.0/me """[user.read"}}, ou o URL da aplicação do recurso como: {" https://graph.microsoft.com/ "user.read", "mail.send"}} Isto é necessário para chamadas CORS. Incumprimentos a `null` .
