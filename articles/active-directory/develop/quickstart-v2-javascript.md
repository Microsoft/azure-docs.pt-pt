---
title: 'Início rápido: Iniciar snus em utilizadores em aplicações de página única JavaScript | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação JavaScript pode chamar uma API que requer acesso a tokens emitidos pela plataforma de identidade Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 131cb325e3581a135be7a88f26c59637d35bc5f3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937956"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Quickstart: Inicie seduário nos utilizadores e obtenha um token de acesso num JavaScript SPA

Neste arranque rápido, você descarrega e execute uma amostra de código que demonstra como uma aplicação de página única JavaScript (SPA) pode iniciar sôm nos utilizadores e ligar para o Microsoft Graph. A amostra de código também demonstra como obter um token de acesso para ligar para a API do Gráfico microsoft ou qualquer API web.

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código do Estúdio Visual](https://code.visualstudio.com/download) (para editar ficheiros de projeto)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registe-se e baixe a sua aplicação de arranque rápido
> Para iniciar a sua aplicação de arranque rápido, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Expresso): Registar e configurar automaticamente a sua app e, em seguida, descarregar a sua amostra de código
>
> 1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portal Azure - <span class="docon docon-navigate-external x-hidden-focus"></span> Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a aplicação.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**.
> 1. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registar e configurar manualmente a sua aplicação e amostra de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**. Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
> 1. Este arranque rápido requer que o [fluxo de subvenção implícito](v2-oauth2-implicit-grant-flow.md) seja ativado. Em **Gestão**, **selecione Autenticação**.
> 1. Em **Configurações de Plataforma**, selecione Adicionar uma **plataforma**. Abre-se um painel à esquerda. Lá, selecione a região **aplicações web.**
> 1. Ainda à esquerda, desa um **valor URI de redirecionamento** para `http://localhost:3000/` . Em seguida, selecione **Access Token** e **ID Token**.
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> O código de amostra deste quickstart requer um **URI de redirecionamento** `http://localhost:3000/` e permite uma **concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar a sua aplicação JavaScript
>
> Na pasta *JavaScriptSPA,* edite *authConfig.js*, e desaprote o `clientID` , e os `authority` `redirectUri` valores em `msalConfig` .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Em que:
> - *\<Enter_the_Application_Id_Here>* é o **ID de Aplicação (cliente)** para a aplicação que registou.
> - *\<Enter_the_Cloud_Instance_Id_Here>* é o exemplo da nuvem Azure. Para a nuvem Azure principal ou global, basta *https://login.microsoftonline.com* entrar. Para nuvens **nacionais** (por exemplo, China), ver [nuvens nacionais.](./authentication-national-cloud.md)
> - *\<Enter_the_Tenant_info_here>* é definida como uma das seguintes opções:
>    - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino** (por exemplo, *contoso.microsoft.com).*
>    - Se a sua candidatura suporta *contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
>    - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por **comum**. Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor pelos **consumidores**.
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configuramos o seu projeto com valores das propriedades da sua aplicação.

> [!div renderon="docs"]
>
> Em seguida, ainda na mesma pasta, edite *graphConfig.js* ficheiro para definir o `graphMeEndpoint` e para o `graphMeEndpoint` `apiConfig` objeto.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Em que:
> - *\<Enter_the_Graph_Endpoint_Here>* é o ponto final que as chamadas da API serão feitas contra. Para o serviço API principal ou global da Microsoft Graph, basta introduzir `https://graph.microsoft.com` . Para mais informações, consulte [a implantação nacional de nuvens](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Executar o projeto com um servidor web utilizando [Node.js: ](https://nodejs.org/en/download/)

1. Para iniciar o servidor, executar o seguinte comando a partir do diretório do projeto:
    ```cmd
    npm install
    npm start
    ```
1. Abra um navegador web e vá para `http://localhost:3000/` .

1. Selecione **Iniciar sôs-in** para iniciar a s início da sinagem e, em seguida, ligue para a Microsoft Graph API.

Depois de o navegador carregar a aplicação, selecione **Iniciar sção**. A primeira vez que iniciar sessão, é solicitado que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de ter assinado com sucesso, as informações do seu perfil de utilizador devem ser apresentadas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

![Como funciona a amostra JavaScript SPA: 1. O SPA inicia a sinsução. 2. O SPA adquire um símbolo de ID da plataforma de identidade da Microsoft. 3. As chamadas DE SPA adquirem ficha. 4. A plataforma de identidade da Microsoft devolve um token de acesso ao SPA. 5. O SPA faz e HTTP GET solicita com o Acess Token para a Microsoft Graph API. 6. A API do Gráfico devolve uma resposta HTTP ao SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A biblioteca MSAL assina nos utilizadores e solicita os tokens que são usados para aceder a uma API que está protegida pela plataforma de identidade da Microsoft. O ficheiro *index.html* de arranque rápido contém uma referência à biblioteca:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Pode substituir a versão anterior pela versão mais recente lançada em [MSAL.js versões](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Em alternativa, se tiver Node.js instalado, pode descarregar a versão mais recente através do Node.js Package Manager (npm):

```cmd
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código quickstart também mostra como inicializar a biblioteca MSAL:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Onde  | Descrição |
> |---------|---------|
> |`clientId`     | Identificação de aplicação da aplicação registada no portal Azure.|
> |`authority`    | (Opcional) O URL de autoridade que suporta tipos de conta, conforme descrito anteriormente na secção de configuração. A autoridade por defeito `https://login.microsoftonline.com/common` é. |
> |`redirectUri`     | A resposta/reorientação do registo do pedido configurado. Neste caso, `http://localhost:3000/` . |
> |`cacheLocation`  | (Opcional) Define o armazenamento do navegador para o estado de auth. O padrão é sessãoStorage.   |
> |`storeAuthStateInCookie`  | (Opcional) A biblioteca que armazena o pedido de autenticação indica que é necessária para validação dos fluxos de autenticação nos cookies do navegador. Este cookie está definido para navegadores IE e Edge para mitigar certos [problemas conhecidos.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) |

Para obter mais informações sobre as opções disponíveis, consulte [As aplicações do cliente Inicialize.](msal-js-initializing-client-applications.md)

### <a name="sign-in-users"></a>Inscreva-se nos utilizadores

O seguinte código snippet mostra como se assina nos utilizadores:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Onde  | Descrição |
> |---------|---------|
> | `scopes`   | (Opcional) Contém âmbitos que estão a ser solicitados para o consentimento do utilizador no momento de início de sedu. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou para `[ "<Application ID URL>/scope" ]` APIs web personalizados (isto é, `api://<Application ID>/access_as_user` ). |

> [!TIP]
> Em alternativa, é melhor utilizar o `loginRedirect` método para redirecionar a página atual para a página de sposição em vez de uma janela pop-up.

### <a name="request-tokens"></a>Fichas de pedido

A MSAL utiliza três métodos para adquirir fichas: `acquireTokenRedirect` `acquireTokenPopup` , e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com aquisições simbólicas e renovação sem qualquer interação do utilizador. Após o `loginRedirect` ou método ser executado pela primeira `loginPopup` vez, é o método `acquireTokenSilent` comumente usado para obter fichas que são usadas para aceder a recursos protegidos para chamadas subsequentes. As chamadas para solicitar ou renovar fichas são feitas silenciosamente.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Onde  | Descrição |
> |---------|---------|
> | `scopes`   | Contém âmbitos a serem solicitados para serem devolvidos no token de acesso para a API. Por exemplo, `[ "mail.read" ]` para o Microsoft Graph ou para `[ "<Application ID URL>/scope" ]` APIs web personalizados (isto é, `api://<Application ID>/access_as_user` ).|

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Existem situações em que é necessário forçar os utilizadores a interagir com a plataforma de identidade da Microsoft. Por exemplo:
* Os utilizadores podem precisar de reentrar nas suas credenciais porque a sua palavra-passe expirou.
* A sua aplicação está a solicitar acesso a âmbitos de recursos adicionais aos quais o utilizador precisa de consentir.
* É necessária autenticação de dois fatores.

O padrão recomendado habitual para a maioria das aplicações é ligar `acquireTokenSilent` primeiro, depois pegar a exceção e, em seguida, ligar `acquireTokenPopup` (ou `acquireTokenRedirect` ) para iniciar um pedido interativo.

Chamando os `acquireTokenPopup` resultados numa janela popup para iniciar sessão. (Ou `acquireTokenRedirect` resulta na reorientação dos utilizadores para a plataforma de identidade da Microsoft). Nessa janela, os utilizadores precisam interagir confirmando as suas credenciais, dando o consentimento ao recurso necessário, ou completando a autenticação de dois fatores.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Este quickstart utiliza os e métodos com o `loginRedirect` Microsoft Internet `acquireTokenRedirect` Explorer, devido a um problema [conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com o manuseamento de janelas popup pelo Internet Explorer.

## <a name="next-steps"></a>Próximos passos

Para um guia passo a passo mais detalhado sobre a construção do pedido para este arranque rápido, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Inscreva-se nos utilizadores e ligue para a API do Gráfico microsoft a partir de uma aplicação de página única JavaScript (SPA)](tutorial-v2-javascript-spa.md)
