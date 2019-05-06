---
title: Inicializar aplicações de cliente (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba mais sobre a inicializar a aplicativos cliente usando a biblioteca de autenticação da Microsoft para JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7990566ca9cd93e79b8356cfd15fda03a7469695
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138306"
---
# <a name="initialize-client-applications-using-msaljs"></a>Inicializar aplicações de cliente com msal
Este artigo descreve ao inicializar biblioteca de autenticação da Microsoft para JavaScript (msal) com uma instância de um aplicativo de agente do usuário. A aplicação de agente do usuário é um formulário de aplicação de cliente público no qual o código de cliente é executado num agente de usuário, como um navegador da web. Estes clientes não armazenar segredos, uma vez que o contexto do navegador é acessível abertamente. Para saber mais sobre os tipos de aplicativos de cliente e as opções de configuração de aplicação, veja a [descrição geral](msal-client-applications.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar uma aplicação, primeiro tem de [registrá-la no portal do Azure](scenario-spa-app-registration.md) para que a sua aplicação pode ser integrada com a plataforma de identidade da Microsoft. Após o registo, poderá ter as seguintes informações (que podem ser encontradas no portal do Azure):

- O ID de cliente (uma cadeia que representa um GUID para a sua aplicação)
- URL do fornecedor de identidade (com o nome da instância) e o início de sessão público-alvo para a sua aplicação. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- O ID de inquilino se estiver escrevendo um aplicativo de linha de negócio unicamente para a sua organização (também denominada aplicação inquilino único).
- Para aplicações web, terá de definir também o redirectUri onde o fornecedor de identidade irá devolver ao seu aplicativo com os tokens de segurança.

## <a name="initializing-applications"></a>A inicializar a aplicações

Pode usar msal da seguinte forma num aplicativo simples do JavaScript/Typescript. Inicializar o contexto de autenticação de MSAL pela Instanciação de `UserAgentApplication` com um objeto de configuração. A configuração mínima necessária para inicializar a msal é o clientID da sua aplicação que pode ser obtido a partir do portal de registo da aplicação.

Para métodos de autenticação com fluxos de redirecionamento (`loginRedirect` e `acquireTokenRedirect`), terá de registar explicitamente um retorno de chamada para o sucesso ou erro por meio de `handleRedirectCallback()` método. Isto é necessário uma vez que os fluxos de redirecionamento não retornam promessas tal como os métodos com uma experiência de pop-up.

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

Msal foi concebido para ter uma única instância e a configuração do `UserAgentApplication` para representar um contexto de autenticação único. Várias instâncias não são recomendadas como causarem em conflito entradas de cache e o comportamento no browser.

## <a name="configuration-options"></a>Opções de configuração

Msal tem uma configuração de objeto mostrado abaixo que fornece um agrupamento de opções configuráveis disponíveis para a criação de uma instância de `UserAgentApplication`.

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

Segue-se o conjunto total de opções configuráveis que são atualmente suportados no objeto de configuração:

- **clientID**: Necessário. O clientID da sua aplicação, deverá obter isso do portal de registo da aplicação.

- **Autoridade**: Opcional. Um URL que indica um diretório que MSAL pode pedir tokens a partir de. Valor predefinido é: `https://login.microsoftonline.com/common`.
    * No Azure AD, é o formulário https://&lt;instância&gt;/&lt;público-alvo&gt;, em que &lt;instância&gt; é o domínio de fornecedor de identidade (por exemplo, `https://login.microsoftonline.com`) e &lt;público-alvo&gt; é um identificador que representa o público-alvo início de sessão. Isto pode ser os seguintes valores:
        * `https://login.microsoftonline.com/<tenant>`-o inquilino é um domínio associado ao inquilino, como contoso.onmicrosoft.com ou o GUID que representa o `TenantID` propriedade do diretório utilizado apenas para iniciar sessão dos utilizadores de uma organização específica.
        * `https://login.microsoftonline.com/common`-Utilizado para iniciar sessão dos utilizadores com uma conta pessoal da Microsoft e contas escolar ou profissional.
        * `https://login.microsoftonline.com/organizations/`-Utilizado para iniciar sessão dos utilizadores com contas profissionais e escolares.
        * `https://login.microsoftonline.com/consumers/` -Utilizado para iniciar sessão dos utilizadores com apenas pessoal conta Microsoft (live).
    * No Azure AD B2C, ele é o formato `https://<instance>/tfp/<tenant>/<policyName>/`, onde a instância é o domínio do Azure AD B2C, inquilino é o nome do inquilino do Azure AD B2C, policyName é o nome da política B2C para aplicar.


- **validateAuthority**: Opcional.  Valide o emissor de tokens. A predefinição é `true`. Para as aplicações B2C, uma vez que o valor de autoridade é conhecido e pode ser diferente, de acordo com a política, a validação de autoridade não funcionará e tem de ser definida como `false`.

- **redirectUri**: Opcional.  O URI de redirecionamento de seu aplicativo, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos URIs que registou no portal de redirecionamento, exceto pelo fato de que tem de ser codificado de URL. Assume a predefinição `window.location.href`.

- **postLogoutRedirectUri**: Opcional.  Redireciona o usuário `postLogoutRedirectUri` depois de terminar. A predefinição é `redirectUri`.

- **navigateToLoginRequestUrl**: Opcional. Capacidade para desativar a navegação padrão para a página inicial após o início de sessão. A predefinição é verdadeiro. Isto é utilizado apenas para os fluxos de redirecionamento.

- **cacheLocation**: Opcional.  Define o armazenamento de navegador para qualquer um `localStorage` ou `sessionStorage`. A predefinição é `sessionStorage`.

- **storeAuthStateInCookie**: Opcional.  Este sinalizador foi introduzido no msal v0.2.2 como uma correção para o [problemas de autenticação do loop](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) no Microsoft Internet Explorer e o Microsoft Edge. Ativar o sinalizador `storeAuthStateInCookie` para verdadeiro para tirar proveito disso corrigir. Quando esta opção estiver ativada, a msal irá armazenar o estado do pedido de autenticação necessário para a validação dos fluxos de autenticação nos cookies do browser. Por predefinição, este sinalizador estiver definido como `false`.

- **logger**: Opcional.  Um objeto Logger com uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar os registos de maneira personalizada. Para obter detalhes sobre passando o objeto logger, consulte [logs com msal](msal-logging.md).

- **loadFrameTimeout**: Opcional.  O número de milissegundos de inatividade antes de uma resposta de renovação de token do Azure AD deve ser considerada excedeu o tempo limite. A predefinição é 6 segundos.

- **tokenRenewalOffsetSeconds**: Opcional. O número de milissegundos que define a janela de deslocamento necessária para renovar o token antes da expiração. A predefinição é de 300 milissegundos.

Só são aplicáveis a ser transmitida a partir da biblioteca de wrapper MSAL Angular:
- **unprotectedResources**: Opcional.  Matriz de URIs que são recursos desprotegidos. MSAL não irá anexar um token para os pedidos enviados que tenham estes URI. Assume a predefinição `null`.

- **protectedResourceMap**: Opcional.  Isso é de mapeamento de recursos para âmbitos utilizados pelo MSAL para anexar automaticamente os tokens de acesso nas chamadas de API web. Um token de acesso único é obtido do recurso. Portanto, pode mapear um caminho de recurso específico da seguinte forma: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, ou o URL da aplicação do recurso como: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Isto é necessário para chamadas CORS. Assume a predefinição `null`.
