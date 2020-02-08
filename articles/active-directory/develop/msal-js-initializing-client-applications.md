---
title: Inicializar aplicativos cliente MSAL. js | Azure
titleSuffix: Microsoft identity platform
description: Saiba como inicializar aplicativos cliente usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
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
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084037"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializar aplicativos cliente usando o MSAL. js
Este artigo descreve como inicializar a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com uma instância de um aplicativo de agente do usuário. O aplicativo de agente de usuário é uma forma de aplicativo cliente público em que o código do cliente é executado em um agente de usuário, como um navegador da Web. Esses clientes não armazenam segredos, pois o contexto do navegador está aberto de acessível. Para saber mais sobre os tipos de aplicação do cliente e as opções de configuração de aplicações, leia a [visão geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar uma aplicação, é necessário [registrá-la](scenario-spa-app-registration.md) primeiro no portal Azure para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registro, talvez você precise das seguintes informações (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID para seu aplicativo)
- A URL do provedor de identidade (chamada de instância) e o público-alvo de entrada para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios somente para sua organização (também chamado de aplicativo de locatário único).
- Para aplicativos Web, você também precisará definir o redirectUri onde o provedor de identidade retornará ao seu aplicativo com os tokens de segurança.

## <a name="initializing-applications"></a>Inicializando aplicativos

Você pode usar MSAL. js da seguinte maneira em um aplicativo JavaScript/typescript simples. Inicialize o contexto de autenticação MSAL, instantaneando `UserAgentApplication` com um objeto de configuração. A configuração mínima necessária para inicializar o MSAL. js é o clientID do seu aplicativo que você deve obter do portal de registro de aplicativos.

Para métodos de autenticação com fluxos redirecionais (`loginRedirect` e `acquireTokenRedirect`), terá de registar explicitamente um callback para o sucesso ou erro através `handleRedirectCallback()` método. Isso é necessário, já que fluxos de redirecionamento não retornam promessas, pois os métodos com uma experiência de pop-up fazem.

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

MSAL.js foi concebido para ter uma única instância e configuração do `UserAgentApplication` para representar um único contexto de autenticação. Várias instâncias não são recomendadas, pois causam entradas e comportamento de cache conflitantes no navegador.

## <a name="configuration-options"></a>Opções de configuração

MSAL.js tem um objeto de configuração mostrado abaixo que fornece um agrupamento de opções configuráveis disponíveis para criar uma instância de `UserAgentApplication`.

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

Veja abaixo o conjunto total de opções configuráveis que têm suporte no momento no objeto de configuração:

- **clienteID**: Necessário. O clientID do seu aplicativo, você deve obtê-lo no portal de registro de aplicativos.

- **autoridade**: Opcional. Uma URL que indica um diretório do qual MSAL pode solicitar tokens. O valor predefinido é: `https://login.microsoftonline.com/common`.
    * Em Azure AD, é da forma https://&lt;exemplo&gt;/&lt;&gt;do público , onde &lt;por exemplo&gt; é o domínio do fornecedor de identidade (por exemplo, `https://login.microsoftonline.com`) e &lt;público&gt; é um identificador que representa o público que representa o público que representa o público que representa o público que representa o sign-in. Esses valores podem ser os seguintes:
        * `https://login.microsoftonline.com/<tenant>`- inquilino é um domínio associado ao inquilino, como contoso.onmicrosoft.com, ou o GUID que representa a propriedade `TenantID` do diretório utilizado apenas para assinar em utilizadores de uma organização específica.
        * `https://login.microsoftonline.com/common`- Usado para assinar em utilizadores com contas de trabalho e escola ou uma conta pessoal da Microsoft.
        * `https://login.microsoftonline.com/organizations/`- Costumava inscrever utentes com contas de trabalho e escola.
        * `https://login.microsoftonline.com/consumers/` - Usado para iniciar sessão em utilizadores com apenas conta pessoal da Microsoft (ao vivo).
    * Em Azure AD B2C, é da forma `https://<instance>/tfp/<tenant>/<policyName>/`, onde o caso é o domínio Azure AD B2C, ou seja, {o seu nome de inquilino}.b2clogin.com, o inquilino é o nome do inquilino Azure AD B2C i.e. {your-tenant-name}.onmicrosoft.com, policyName é o nome da política B2C a aplicar.


- **validaAutoridade**: Opcional.  Valide o emissor de tokens. A predefinição é `true`. No caso dos pedidos B2C, uma vez que o valor da autoridade é conhecido e pode ser diferente por política, a validação da autoridade não funcionará e tem de ser definida para `false`.

- **redirecionamentoUri**: Opcional.  O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no Portal. Incumprimentos para `window.location.href`.

- **postLogoutRedirectUri**: Opcional.  Redireciona o utilizador para `postLogoutRedirectUri` depois de assinar. O padrão é `redirectUri`.

- **navigateToLoginRequestUrl**: Opcional. Capacidade de desativar a navegação padrão na página inicial após o logon. O padrão é verdade. Isso é usado somente para fluxos de redirecionamento.

- **cacheLocalização**: Opcional.  Define o armazenamento do navegador para `localStorage` ou `sessionStorage`. A predefinição é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Esta bandeira foi introduzida em MSAL.js v0.2.2 como uma correção para os problemas de loop de [autenticação](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e Microsoft Edge. Permitir que a bandeira `storeAuthStateInCookie` fiel para tirar partido desta correção. Quando habilitado, o MSAL. js armazenará o estado de solicitação de autenticação necessário para a validação dos fluxos de autenticação nos cookies do navegador. Por defeito, esta bandeira está definida para `false`.

- **madeireiro**: Opcional.  Um objeto de agente com uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de maneira personalizada. Para mais detalhes sobre a passagem do objeto madeireiro, consulte [a exploração madeireira com msal.js](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes de uma resposta de renovação de token do Azure AD deve ser considerado com tempo limite. O padrão é 6 segundos.

- **tokenRenovationOffsetSeconds**: Opcional. O número de milissegundos que define a janela de deslocamento necessária para renovar o token antes da expiração. O padrão é 300 milissegundos.

- **navigateFrameWait**: Opcional. O número de milissegundos que define o tempo de espera antes que os iframes ocultos naveguem até seu destino. O padrão é 500 milissegundos.

Eles só se aplicam a serem passados da biblioteca de invólucro angular do MSAL:
- **Recursos desprotegidos**: Opcional.  Matriz de URIs que são recursos desprotegidos. MSAL não anexará um token às solicitações de saída que têm esse URI. Incumprimentos para `null`.

- **protectedResourceMap**: Opcional.  Esse é o mapeamento de recursos para escopos usados pelo MSAL para anexar automaticamente tokens de acesso em chamadas à API Web. Um único token de acesso é obtido para o recurso. Assim, pode mapear um caminho específico de recursos da seguinte forma: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, ou o URL da aplicação do recurso como: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Isso é necessário para chamadas de CORS. Incumprimentos para `null`.
