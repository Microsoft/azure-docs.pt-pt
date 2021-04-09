---
title: 'Quickstart: Ligue para o Microsoft Graph a partir de uma aplicação de ambiente de trabalho Node.js | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação de desktop Node.js Electron pode iniciar sômedônio e obter um token de acesso para chamar uma API protegida por um ponto final da plataforma de identidade microsoft
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653274"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Quickstart: Adquirir um token de acesso e ligar para a Microsoft Graph API a partir de uma aplicação de desktop Electron

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação de desktop Da Electrn pode iniciar sôm nos utilizadores e adquirir fichas de acesso para ligar para a Microsoft Graph API.

Este quickstart utiliza a Biblioteca de Autenticação da [Microsoft para Node.js (nó MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) com o [fluxo de código de autorização com PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registe-se e descarregue a aplicação da amostra
>
> Siga os passos abaixo para começar.
>
> #### <a name="step-1-register-the-application"></a>Passo 1: Registar o pedido
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `msal-node-desktop` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Selecione **Registar** para criar a aplicação.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. **Selecione Adicionar uma plataforma** de  >  **aplicações móveis e desktop**.
> 1. Na secção **URIs de redirecionamento,** insira `msal://redirect` .
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal Azure
> Para que a amostra de código para este arranque rápido funcione, é necessário adicionar um URL de resposta como **msal://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-windows-desktop/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-electron-sample-project"></a>Passo 2: Descarregue o projeto de amostra de eletrões

> [!div renderon="docs"]
> [Descarregue a amostra de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Passo 3: Configurar o projeto de amostra de eletrões
>
> 1. Extrair o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, *C:/Azure-Samples*.
> 1. *Edite.env* e substitua os valores dos campos `TENANT_ID` e com o seguinte `CLIENT_ID` corte:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
>    - `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino Ou** Inquilino (por exemplo, contoso.microsoft.com)
>
> > [!TIP]
> > Para encontrar os valores de ID de **aplicação (cliente),** **ID de diretório (inquilino),** aceda à página **geral** da aplicação no portal Azure.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

Terá de instalar as dependências desta amostra uma vez:

```console
npm install
```

Em seguida, executar a aplicação através do pedido de comando ou consola:

```console
npm start
```

Você deve ver uI da aplicação com um **botão de sinal no** botão.

## <a name="about-the-code"></a>Sobre o código

Em seguida, são discutidos alguns dos aspetos importantes da aplicação da amostra.

### <a name="msal-node"></a>Nó MSAL

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) é a biblioteca usada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Para obter mais informações sobre como utilizar o Nó MSAL com aplicações para desktop, consulte [este artigo](scenario-desktop-overview.md).

Pode instalar o nó MSAL executando o seguinte comando npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para o nó MSAL adicionando o seguinte código:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Em seguida, inicialize a MSAL com o código seguinte:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Em que: |Description |
> |---------|---------|
> | `clientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `authority`    | O ponto final STS para o utilizador autenticar. Normalmente `https://login.microsoftonline.com/{tenant}` para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id do seu inquilino.|

### <a name="requesting-tokens"></a>Pedir tokens

Na primeira etapa do fluxo de código de autorização com o PKCE, prepare e envie um pedido de código de autorização com os parâmetros apropriados. Então, na segunda parte do fluxo, ouça a resposta do código de autorização. Uma vez obtido o código, troque-o para obter um token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Em que:| Description |
> |---------|---------|
> | `authWindow` | Janela de eletrões atual em processo. |
> | `tokenRequest` | Contém os âmbitos que estão a ser solicitados, tais como `"User.Read"` para o Microsoft Graph ou para `"api://<Application ID>/access_as_user"` APIs web personalizados. |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de aplicativos de desktop da Electron com o MSAL Node, consulte o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API numa aplicação de ambiente de trabalho electron](tutorial-v2-nodejs-desktop.md)