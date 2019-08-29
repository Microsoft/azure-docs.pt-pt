---
title: Inicializar aplicativos cliente (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba como inicializar aplicativos cliente usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d36def4faa98f4b8e42c93cf3e222c2ec7ca89a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073833"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializar aplicativos cliente usando o MSAL. js
Este artigo descreve como inicializar a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com uma instância de um aplicativo de agente do usuário. O aplicativo de agente de usuário é uma forma de aplicativo cliente público em que o código do cliente é executado em um agente de usuário, como um navegador da Web. Esses clientes não armazenam segredos, pois o contexto do navegador está aberto de acessível. Para saber mais sobre os tipos de aplicativo cliente e as opções de configuração de aplicativo, leia a [visão geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo com o portal do Azure](scenario-spa-app-registration.md) para que seu aplicativo possa ser integrado à plataforma Microsoft Identity. Após o registro, talvez você precise das seguintes informações (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID para seu aplicativo)
- A URL do provedor de identidade (chamada de instância) e o público-alvo de entrada para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios somente para sua organização (também chamado de aplicativo de locatário único).
- Para aplicativos Web, você também precisará definir o redirectUri onde o provedor de identidade retornará ao seu aplicativo com os tokens de segurança.

## <a name="initializing-applications"></a>Inicializando aplicativos

Você pode usar MSAL. js da seguinte maneira em um aplicativo JavaScript/typescript simples. Inicialize o contexto de autenticação MSAL instanciando `UserAgentApplication` com um objeto de configuração. A configuração mínima necessária para inicializar o MSAL. js é o clientID do seu aplicativo que você deve obter do portal de registro de aplicativos.

Para métodos de autenticação com fluxos de`loginRedirect` redirecionamento (e `acquireTokenRedirect`), você precisará registrar explicitamente um retorno de chamada `handleRedirectCallback()` para êxito ou erro por meio do método. Isso é necessário, já que fluxos de redirecionamento não retornam promessas, pois os métodos com uma experiência de pop-up fazem.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
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

O `UserAgentApplication` MSAL. js foi projetado para ter uma única instância e configuração do para representar um único contexto de autenticação. Várias instâncias não são recomendadas, pois causam entradas e comportamento de cache conflitantes no navegador.

## <a name="configuration-options"></a>Opções de configuração

MSAL. js tem um objeto de configuração mostrado abaixo que fornece um agrupamento de opções configuráveis disponíveis para a criação `UserAgentApplication`de uma instância do.

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

- **clientID**: Necessário. O clientID do seu aplicativo, você deve obtê-lo no portal de registro de aplicativos.

- **autoridade**: Opcional. Uma URL que indica um diretório do qual MSAL pode solicitar tokens. O valor padrão é `https://login.microsoftonline.com/common`:.
    * No Azure ad&lt;, ele é do formato público&gt;/&gt;da instância&lt;https://, em &lt;que&gt; Instance é o domínio do provedor de identidade ( `https://login.microsoftonline.com`por exemplo,) e o público-alvo&gt; é um identificador que representa o público-alvo. &lt; Esses valores podem ser os seguintes:
        * `https://login.microsoftonline.com/<tenant>`-o locatário é um domínio associado ao locatário, como contoso.onmicrosoft.com, ou o GUID que representa a `TenantID` Propriedade do diretório usado somente para conectar usuários de uma organização específica.
        * `https://login.microsoftonline.com/common`-Usado para conectar usuários com contas corporativas e de estudante ou uma conta pessoal da Microsoft.
        * `https://login.microsoftonline.com/organizations/`-Usado para conectar usuários com contas corporativas e de estudante.
        * `https://login.microsoftonline.com/consumers/`-Usado para conectar usuários com apenas conta Microsoft pessoais (ao vivo).
    * Em Azure ad B2C, é o formato `https://<instance>/tfp/<tenant>/<policyName>/`, em que Instance é o domínio Azure ad B2C, locatário é o nome do locatário Azure ad B2C, PolicyName é o nome da política B2C a ser aplicada.


- **validateAuthority**: Opcional.  Valide o emissor de tokens. A predefinição é `true`. Para aplicativos B2C, como o valor de autoridade é conhecido e pode ser diferente por política, a validação de autoridade não funcionará e precisará ser `false`definida como.

- **redirectUri**: Opcional.  O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no Portal. Assume a predefinição `window.location.href`.

- **postLogoutRedirectUri**: Opcional.  Redireciona o usuário para `postLogoutRedirectUri` depois de sair. A predefinição é `redirectUri`.

- **navigateToLoginRequestUrl**: Opcional. Capacidade de desativar a navegação padrão na página inicial após o logon. O padrão é true. Isso é usado somente para fluxos de redirecionamento.

- **cacheLocation**: Opcional.  Define o `localStorage` armazenamento do navegador como `sessionStorage`ou. A predefinição é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Esse sinalizador foi introduzido no MSAL. js v 0.2.2 como uma correção para os [problemas de loop de autenticação](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e no Microsoft Edge. Habilite o `storeAuthStateInCookie` sinalizador como true para tirar proveito dessa correção. Quando habilitado, o MSAL. js armazenará o estado de solicitação de autenticação necessário para a validação dos fluxos de autenticação nos cookies do navegador. Por padrão, esse sinalizador é definido `false`como.

- **logger**: Opcional.  Um objeto de agente com uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de maneira personalizada. Para obter detalhes sobre como passar o objeto do agente, consulte [registrando em log com MSAL. js](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes de uma resposta de renovação de token do Azure AD deve ser considerado com tempo limite. O padrão é 6 segundos.

- **tokenRenewalOffsetSeconds**: Opcional. O número de milissegundos que define a janela de deslocamento necessária para renovar o token antes da expiração. O padrão é 300 milissegundos.

Eles só se aplicam a serem passados da biblioteca de invólucro angular do MSAL:
- **unprotectedResources**: Opcional.  Matriz de URIs que são recursos desprotegidos. MSAL não anexará um token às solicitações de saída que têm esse URI. Assume a predefinição `null`.

- **protectedResourceMap**: Opcional.  Esse é o mapeamento de recursos para escopos usados pelo MSAL para anexar automaticamente tokens de acesso em chamadas à API Web. Um único token de acesso é obtido para o recurso. Portanto, você pode mapear um caminho de recurso específico da seguinte https://graph.microsoft.com/v1.0/me maneira: {"", ["User. Read"]} ou a URL do aplicativo do recurso como https://graph.microsoft.com/: {"", ["User. Read", "mail. Send"]}. Isso é necessário para chamadas de CORS. Assume a predefinição `null`.
