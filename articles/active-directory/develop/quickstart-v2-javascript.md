---
title: Microsoft identity platform JavaScript manual de início rápido - Azure
description: Saiba como aplicativos JavaScript podem chamar uma API que requer que os tokens de acesso utilizando a plataforma de identidade da Microsoft.
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
ms.openlocfilehash: 2182708ea459b578a2a9a94213ab41e76821aefc
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514349"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Início rápido: Iniciar sessão dos utilizadores e adquirir um token de acesso a partir de uma aplicação de página única de JavaScript

Neste início rápido, irá aprender a utilizar um código de exemplo que demonstra como uma aplicação de página única (SPA) de JavaScript pode iniciar sessão dos utilizadores de contas pessoais, contas de trabalho e contas escolares. Um SPA do JavaScript também pode obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API web.

![Como funciona a aplicação de exemplo neste início rápido](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer a configuração seguinte:
* Para executar o projeto com um servidor de node. js, transfira e instale [node. js](https://nodejs.org/en/download/).
* Para editar os arquivos do projeto, transfira e instale [Visual Studio Code](https://code.visualstudio.com/download).
* Para executar o projeto como uma solução do Visual Studio, transfira e instale [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registre-se e transferir a aplicação de início rápido
> Para iniciar a aplicação de início rápido, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Express): Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de um trabalho ou uma conta escolar ou uma conta Microsoft pessoal.
> 1. Se sua dá conta lhe acesso a mais do que um inquilino, selecione a conta no canto superior direito e, em seguida, defina a sua sessão do portal para o inquilino do Azure Active Directory (Azure AD) que pretende utilizar.
> 1. Vá para a nova [portal do Azure – registos de aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) painel.
> 1. Introduza um nome para a sua aplicação e selecione **registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registar e configurar manualmente o seu aplicativo e o código de exemplo
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
>
> 1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de um trabalho ou uma conta escolar ou uma conta Microsoft pessoal.
>
> 1. Se sua dá conta lhe acesso a mais do que um inquilino, selecione a sua conta no canto superior direito e, em seguida, defina a sua sessão do portal para o inquilino do Azure AD que pretende utilizar.
> 1. Vá para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
> 1. Selecione **novo registo**.
> 1. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
> 1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
> 1. Sob o **URI de redirecionamento** secção, na lista pendente, selecione a **Web** plataforma e, em seguida, defina o valor como `http://localhost:30662/`.
> 1. Selecione **Registar**. Na aplicação **descrição geral** página, tenha em atenção a **ID de aplicação (cliente)** valor para utilização posterior.
> 1. Este início rápido requer a [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo do aplicativo registrado, selecione **autenticação**.
> 1. Na **definições avançadas** secção, em **concessão implícita**, selecione o **tokens de ID** e **tokens de acesso** caixas de verificação. Tokens de ID e tokens de acesso são necessários, uma vez que esta aplicação tem de iniciar sessão dos utilizadores e chamar uma API.
> 1. Na parte superior do painel, selecione **guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua aplicação no portal do Azure
> Para o código de exemplo para este início rápido funcionar, terá de adicionar um redirecionamento URI como `http://localhost:30662/` e ative **concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Efetuar estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transfira o projeto

Selecione a opção adequada para seu ambiente de desenvolvimento:

* Para executar o projeto com um servidor web utilizando node. js, [transferir os ficheiros de projeto core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Para abrir os ficheiros, utilize um editor como [Visual Studio Code](https://code.visualstudio.com/).

* (Opcional) Para executar o projeto com o servidor IIS [transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Extraia o ficheiro zip para uma pasta local (por exemplo, *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar a sua aplicação do JavaScript

> [!div renderon="docs"]
> Na *JavaScriptSPA* pasta, editar *Index*e defina o `clientID` e `authority` valores sob `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Na *JavaScriptSPA* pasta, edite *Index*e substitua `msalConfig` com o código a seguir:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Este início rápido suporta Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Em que:
> - *\<Enter_the_Application_Id_here >* é o **ID da aplicação (cliente)** para a aplicação que registou.
> - *\<Enter_the_Tenant_info_here >* está definido para uma das seguintes opções:
>    - Se a sua aplicação suportar *contas neste diretório organizacional*, substitua este valor com o **ID do inquilino** ou **nome do inquilino** (por exemplo,  *contoso.microsoft.com*).
>    - Se a sua aplicação suportar *contas em qualquer diretório organizacional*, substitua este valor com **organizações**.
>    - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas Microsoft pessoais*, substitua este valor com **comuns**. Para restringir o suporte para *contas Microsoft pessoa de apenas*, substitua este valor com **consumidores**.
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)** , o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.
>

#### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

* Se estiver a utilizar [node. js](https://nodejs.org/en/download/):

    1. Para iniciar o servidor, execute o seguinte comando a partir do diretório do projeto:

        ```batch
        npm install
        node server.js
        ```

    1. Abra um browser e aceda a `http://localhost:30662/`.
    1. Selecione **sessão** para iniciar o início de sessão e, em seguida, chamar o Microsoft Graph API.


* Se estiver a utilizar [Visual Studio](https://visualstudio.microsoft.com/downloads/), selecione a solução de projeto e, em seguida, selecione F5 para executar o projeto.

Depois do navegador carrega a aplicação, selecione **sessão**. A primeira vez que iniciar sessão, lhe for pedido para fornecer o seu consentimento para permitir que a aplicação para aceder ao seu perfil e para o início de sessão. Depois de iniciar sessão com êxito, as suas informações de perfil do usuário deverá ser apresentadas na página.

## <a name="more-information"></a>Mais informações

### <a name="msaljs"></a>msal.js

A biblioteca MSAL inicia sessão dos utilizadores e solicita os tokens que são utilizados para aceder uma API que está protegida por plataforma de identidade da Microsoft. O início rápido *Index* ficheiro contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Pode substituir a versão anterior com a versão mais recente lançada sob [msal libera](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Em alternativa, se tiver o node. js instalado, pode baixar a versão mais recente através de node. js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido mostra também como inicializar a biblioteca MSAL:

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
> |`ClientId`     | O ID de aplicação da aplicação que está registado no portal do Azure.|
> |`authority`    | (Opcional) O URL da autoridade que oferece suporte a tipos de conta, tal como descrito anteriormente na seção de configuração. A autoridade de padrão é `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Opcional) Define o armazenamento de navegador para o estado de autenticação. A predefinição é sessionStorage.   |
> |`storeAuthStateInCookie`  | (Opcional) A biblioteca que armazena o estado de pedido de autenticação que é necessário para a validação dos fluxos de autenticação nos cookies do browser. Esse cookie é definido para navegadores de periferia e IE atenuar determinados [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Para obter mais informações sobre as opções configuráveis disponíveis, consulte [inicializar aplicações cliente](msal-js-initializing-client-applications.md).

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
> | `scopes`   | (Opcional) Contém os âmbitos que estão a ser pedidos para o consentimento do utilizador em tempo de início de sessão. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (ou seja, `api://<Application ID>/access_as_user`). |

> [!TIP]
> Em alternativa, pode querer utilizar o `loginRedirect` método para redirecionar a página atual para a página de início de sessão, em vez de uma janela de pop-up.

### <a name="request-tokens"></a>Pedir tokens

A MSAL utiliza três métodos para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup`, e `acquireTokenSilent`

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

Existem situações em que precisa forçar os utilizadores interajam com o ponto de extremidade de plataforma de identidade Microsoft. Por exemplo:
* Os utilizadores poderão ter de reintroduzir as credenciais, uma vez que a palavra-passe expirou.
* A aplicação está a solicitar acesso a âmbitos de recursos adicionais que o utilizador tem de dar consentimento a.
* É necessária a autenticação de dois fatores.

Padrão recomendado normal para a maioria dos aplicativos é chamar `acquireTokenSilent` em primeiro lugar, em seguida, capturar a exceção e, em seguida, chame `acquireTokenPopup` (ou `acquireTokenRedirect`) para iniciar um pedido de interativo.

Chamar o `acquireTokenPopup` resulta numa janela de pop-up para iniciar sessão. (Ou `acquireTokenRedirect` resulta em redirecionar utilizadores para o ponto de extremidade de plataforma de identidade Microsoft.) Nessa janela, os utilizadores têm de interagir por confirmar as respetivas credenciais, a dar o consentimento para o recurso necessário ou concluir a autenticação de dois fatores.

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
> Este início rápido utiliza a `loginRedirect` e `acquireTokenRedirect` métodos com o Microsoft Internet Explorer, devido uma [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionados com a manipulação de janelas pop-ups pelo Internet Explorer.

## <a name="next-steps"></a>Passos Seguintes

Para obter um guia passo a passo mais detalhado sobre a criação da aplicação para este início rápido, consulte:

> [!div class="nextstepaction"]
> [Tutorial para iniciar sessão e chamar MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Para procurar o repositório MSAL para documentação, perguntas frequentes, problemas e obter mais informações, veja:

> [!div class="nextstepaction"]
> [Repositório do GitHub de msal](https://github.com/AzureAD/microsoft-authentication-library-for-js)
