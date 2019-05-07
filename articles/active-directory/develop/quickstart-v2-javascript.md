---
title: Guia de introdução do Microsoft identity platform JavaScript | Azure
description: Saiba como os aplicativos JavaScript podem chamar uma API que precisam de tokens de acesso pela plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 605206682cb70d430773cdbf9ff746eabf594103
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190853"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Início rápido: Iniciar sessão dos utilizadores e adquirir um token de acesso a partir de uma aplicação de página única (SPA) de JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, irá aprender como usar um código de exemplo que demonstra como uma aplicação de página única (SPA) de JavaScript pode iniciar sessão em contas pessoais, trabalhar e escolar contas e obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API web.

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

Terá a seguinte configuração para este início rápido:
* Para executar o projeto com um servidor de node. js,
    * Instalar o [Node. js](https://nodejs.org/en/download/)
    * Instale [Visual Studio Code](https://code.visualstudio.com/download) para editar os arquivos do projeto
* Para executar o projeto como uma solução do Visual Studio, instale [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registre-se e transferir a aplicação de início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Vá para a nova [portal do Azure – registos de aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) painel.
> 1. Introduza um nome para a sua aplicação e clique em **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
> 1. Selecione **novo registo**.
> 1. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
> 1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
> 1. Selecione o **Web** plataforma sob a **URI de redirecionamento** secção e defina o valor como `http://localhost:30662/`.
> 1. Quando terminar, selecione **Registar**.  Na aplicação **descrição geral** página, tome nota da **ID de aplicação (cliente)** valor.
> 1. Este início rápido requer a [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel de navegação do lado esquerdo do aplicativo registrado, selecione **autenticação**.
> 1. Na **definições avançadas**, em **concessão implícita**, permita as duas opções **tokens de ID** e **tokens de acesso** caixas de verificação. Tokens de ID e tokens de acesso são necessários, uma vez que esta aplicação tem de iniciar sessão dos utilizadores e chamar uma API.
> 1. Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua aplicação no portal do Azure
> Para o código de exemplo para este início rápido funcionar, terá de adicionar um redirecionamento URI como `http://localhost:30662/` e ative **concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Efetuar estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transfira o projeto

Pode escolher uma destas opções adequadas ao seu ambiente de desenvolvimento.
* [Transferir os ficheiros de projeto core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip), para executar com um servidor web com node. js. Para abrir os ficheiros, utilize um editor, como [Visual Studio Code](https://code.visualstudio.com/).

* (Opcional) [Transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip), para executar com o servidor IIS. Extrair o ficheiro zip para uma pasta local, por exemplo, **C:\Azure-Samples**.



#### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar a sua aplicação do JavaScript

> [!div renderon="docs"]
> Sob a pasta *JavaScriptSPA*, edite `index.html` e defina a `clientID` e `authority` valores sob `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Sob a pasta *JavaScriptSPA*, edite `index.html` e substitua `msalConfig` com:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="docs"]
>
> Em que:
> - `Enter_the_Application_Id_here` - é o **ID da Aplicação (cliente)** que registou.
> - `Enter_the_Tenant_Info_Here` - está definido para uma das seguintes opções:
>   - Se a sua aplicação suportar **Contas neste diretório organizacional**, substitua este valor pelo **Id do Inquilino** ou pelo **Nome do inquilino** (por exemplo, contoso.microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **contas em qualquer diretório organizacional e contas Microsoft pessoais**, substitua este valor com `common`. Para restringir o suporte para *contas Microsoft pessoais de apenas*, substitua este valor com `consumers`.
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.
>

#### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

* Se estiver a utilizar [node. js](https://nodejs.org/en/download/):

    1. Execute o seguinte comando a partir do diretório do projeto para iniciar o servidor:

        ```batch
        npm install
        node server.js
        ```

    1. Abra um browser e navegue para `http://localhost:30662/`.
    1. Clique em **sessão** botão para iniciar o início de sessão e, em seguida, chamar o Microsoft Graph API.


* Se utilizar [Visual Studio](https://visualstudio.microsoft.com/downloads/), certifique-se de selecionar a solução de projeto e, em seguida, prima **F5** para executar o seu projeto.

Depois do navegador carrega a aplicação, clique em **sessão**.  A primeira vez que iniciar sessão, lhe for pedido para fornecer o seu consentimento para permitir que a aplicação para aceder ao seu perfil e para o início de sessão. Com êxito de início de sessão, deverá ver as informações do perfil de utilizador apresentadas na página.

## <a name="more-information"></a>Mais Informações

### <a name="msaljs"></a>*msal.js*

A MSAL é a biblioteca utilizada para iniciar sessão de utilizadores e pedir tokens utilizados para aceder a uma API protegida pela plataforma de identidade da Microsoft. O guia de introdução *Index* contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Pode substituir a versão acima com a versão mais recente lançada sob [msal libera](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Em alternativa, se tiver o nó instalado, pode baixar a versão mais recente através do npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido mostra também como inicializar a biblioteca:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Onde  |  |
> |---------|---------|
> |`ClientId`     |O ID de Aplicação da aplicação registada no portal do Azure|
> |`authority`    | (Opcional) É o URL da autoridade, tal como descrito na seção de configuração acima para suportar os tipos de conta. A autoridade de padrão é `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Opcional) Isso define o armazenamento de navegador para o estado de autenticação. A predefinição é sessionStorage.   |
> |`storeAuthStateInCookie`  | (Opcional) A biblioteca irá armazenar o estado do pedido de autenticação necessário para a validação dos fluxos de autenticação nos cookies do browser. Esta definição estiver definida para navegadores de periferia e IE atenuar determinados [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

 Consulte a [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) para obter mais detalhes sobre as opções configuráveis disponíveis.

### <a name="sign-in-users"></a>Iniciar sessão dos utilizadores

O fragmento de código seguinte mostra como iniciar sessão dos utilizadores:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Onde  |  |
> |---------|---------|
> | `scopes`   | (Opcional) Contém os âmbitos que está a ser pedidos para o consentimento do utilizador em tempo de início de sessão. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (ou seja, `api://<Application ID>/access_as_user` ). |

> [!TIP]
> Em alternativa, talvez queira usar o `loginRedirect` método para redirecionar a página atual para a página de início de sessão, em vez de uma janela de pop-up.

### <a name="request-tokens"></a>Pedir tokens

A MSAL possui três métodos utilizados para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método processa a aquisições de token e a renovação sem qualquer interação do utilizador. Depois do `loginRedirect` ou `loginPopup` método é executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter os tokens que são utilizados para aceder a recursos protegidos por chamadas subsequentes. Chamadas para pedir ou renovar os tokens são feitas automaticamente.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Onde  |  |
> |---------|---------|
> | `scopes`   | Contém a ser pedidos a serem retornados no token de acesso para a API de âmbitos. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (ou seja, `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Existem situações em que precisa forçar os utilizadores interajam com o ponto final de plataforma de identidade Microsoft. Por exemplo:
* Os utilizadores podem ter de reintroduzir as respetivas credenciais porque a palavra-passe expirou
* A aplicação está a solicitar acesso a âmbitos de recursos adicionais que o usuário precisa para dar consentimento para
* É precisa a autenticação de dois fatores

Padrão recomendado normal para a maioria dos aplicativos é chamar `acquireTokenSilent` em primeiro lugar, em seguida, capturar a exceção e, em seguida, chame `acquireTokenPopup` (ou `acquireTokenRedirect`) para iniciar um pedido de interativo.

Chamar o `acquireTokenPopup` resulta numa janela de pop-up para iniciar sessão (ou `acquireTokenRedirect` resulta em redirecionar utilizadores para o ponto de extremidade de plataforma de identidade Microsoft) em que os usuários precisam interagir qualquer uma das respetivas credenciais de instalação, a dar o consentimento à necessários recurso ou concluir a autenticação de dois fatores.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```
> [!NOTE]
> Este início rápido utiliza a `loginRedirect` e `acquireTokenRedirect` métodos quando o browser utilizado é o Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionadas com a manipulação de janelas pop-ups pelo navegador do Internet Explorer.

## <a name="next-steps"></a>Passos Seguintes

Para obter um guia passo a passo mais detalhado sobre como criar a aplicação para este início rápido, experimente o tutorial de JavaScript abaixo.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Conheça as etapas para criar a aplicação para este início rápido

> [!div class="nextstepaction"]
> [Tutorial para iniciar sessão e chamar MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Procurar o repositório MSAL para documentação, perguntas frequentes, problemas e muito mais

> [!div class="nextstepaction"]
> [Repositório do GitHub de msal](https://github.com/AzureAD/microsoft-authentication-library-for-js)
