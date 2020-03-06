---
title: Inscreva-se nos utilizadores em aplicações de uma página única do JavaScript  Azure
titleSuffix: Microsoft identity platform
description: Saiba como uma aplicação JavaScript pode chamar uma API que requer acesso a fichas usando a plataforma de identidade da Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.openlocfilehash: 40f5935c7d653ce90aa56bc5ee2bb15711bb9dd8
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399249"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Quickstart: Inscreva-se nos utilizadores e obtenha um sinal de acesso num JavaScript SPA

Neste arranque rápido, você usa uma amostra de código para saber como uma aplicação de uma página única JavaScript (SPA) pode assinar em utilizadores de contas pessoais, contas de trabalho e contas escolares. Um JavaScript SPA também pode obter um sinal de acesso para ligar para o Microsoft Graph API ou qualquer API web. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) (para editar ficheiros de projeto)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registe-se e descarregue a sua aplicação quickstart
> Para iniciar a sua aplicação de arranque rápido, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Express): Registe-se e configure automaticamente a sua aplicação e, em seguida, descarregue a sua amostra de código
>
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a conta no canto superior direito e, em seguida, detete a sua sessão do portal para o inquilino azure Ative Directory (Azure AD) que deseja utilizar.
> 1. Vá ao novo portal Azure - Painel de inscrições de [aplicativos.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Introduza um nome para a aplicação. 
> 1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
> 1. Selecione **Registar**.
> 1. Siga as instruções para descarregar e configure automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registe-se e configure manualmente a sua aplicação e amostra de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
>
> 1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito e, em seguida, marque a sua sessão do portal para o inquilino da AD Azure que deseja utilizar.
> 1. Vá à plataforma de identidade da Microsoft para programar página de registos de [Aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando aparecer uma página de **inscrição,** insira um nome para a sua candidatura.
> 1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
> 1. Selecione **Registar**. Na página de **visão geral** da aplicação, note o valor de ID da **Aplicação (cliente)** para posterior utilização.
> 1. Este arranque rápido requer que o fluxo de [subvenção implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo da aplicação registada, **selecione Autenticação**.
> 1. Em **configurações**de plataforma, selecione **Adicionar uma plataforma**. Abre-se um painel à esquerda. Lá, selecione a região de **Aplicações Web.**
> 1. Ainda à esquerda, detete o valor **URI redirecionamento** para `http://localhost:3000/`. Em seguida, selecione **Access Token** e **ID Token**.
> 1. **Selecione Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure
> Para que a amostra de código neste quickstart funcione, é necessário adicionar um `redirectUri` como `http://localhost:3000/` e ativar a **concessão implícita.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando o Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código]()

> [!div renderon="docs"]

> #### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configure a sua aplicação JavaScript
>
> Na pasta *JavaScriptSPA,* edite *authConfig.js*e detetete os valores `clientID`, `authority` e `redirectUri` em `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };  
>
>```

> [!div renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Em que:
> - *\<Enter_the_Application_Id_Here>* é o ID de **Aplicação (cliente)** para a aplicação que registou.
> - *\<Enter_the_Cloud_Instance_Id_Here>* é o exemplo da nuvem Azure. Para a nuvem azure principal ou global, basta entrar *https://login.microsoftonline.com* . Para as nuvens **nacionais** (por exemplo, China), veja [as nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)
> - *\<Enter_the_Tenant_info_here>* está definido para uma das seguintes opções:
>    - Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, *contoso.microsoft.com).*
>    - Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
>    - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.**
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)** , o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação. 

> [!div renderon="docs"]
> 
> Em seguida, ainda na mesma pasta, editar o ficheiro *graphfig.js* para definir o `graphMeEndpoint` e `graphMeEndpoint` para o objeto `apiConfig`.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
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
> - *\<Enter_the_Graph_Endpoint_Here>* é o ponto final contra o que serão feitas chamadas da API. Para o serviço principal ou global da Microsoft Graph API, basta entrar `https://graph.microsoft.com`. Para mais informações, consulte a [implantação nacional de nuvens](https://docs.microsoft.com/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Executar o projeto com um servidor web utilizando [o Node.js:](https://nodejs.org/en/download/)

1. Para iniciar o servidor, execute o seguinte comando a partir do diretório do projeto:
    ```batch
    npm install
    npm start
    ```
1. Abra um navegador web e vá para `http://localhost:3000/`.

1. Selecione **Iniciar** sessão para iniciar o início do início do início e, em seguida, ligue para a Microsoft Graph API.

Depois de o navegador carregar a aplicação, selecione 'Iniciar sessão' de **'Sign In'.** A primeira vez que faz o insessão, é-lhe pedido que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de assinado com sucesso, as informações do seu perfil de utilizador devem ser exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

![Como funciona a aplicação de amostras neste quickstart](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A biblioteca MSAL assina nos utilizadores e solicita os tokens que são usados para aceder a uma API protegida pela plataforma de identidade da Microsoft. O ficheiro quickstart *index.html* contém uma referência à biblioteca:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Pode substituir a versão anterior pela versão mais recente lançada nos lançamentos da [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Em alternativa, se tiver node.js instalado, pode descarregar a versão mais recente através do Node.js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de arranque rápido também mostra como inicializar a biblioteca MSAL:

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

> |Onde  |  |
> |---------|---------|
> |`clientId`     | A identificação da aplicação que está registada no portal Azure.|
> |`authority`    | (Opcional) O URL da autoridade que suporta os tipos de conta, como descrito anteriormente na secção de configuração. A autoridade padrão é `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | A resposta/redirecionamento configurado do registo de candidaturasUri. Neste caso, `http://localhost:3000/`. |
> |`cacheLocation`  | (Opcional) Define o armazenamento do navegador para o estado de auth. O padrão é sessãoStorage.   |
> |`storeAuthStateInCookie`  | (Opcional) A biblioteca que armazena o pedido de autenticação diz que é necessário para validação dos fluxos de autenticação nos cookies do navegador. Este cookie está definido para navegadores IE e Edge para mitigar certos [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Para obter mais informações sobre as opções configuráveis disponíveis, consulte [Inicialize as aplicações do cliente.](msal-js-initializing-client-applications.md)

### <a name="sign-in-users"></a>Inscreva-se nos utilizadores

O seguinte código de corte mostra como iniciar sessão nos utilizadores:

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

> |Onde  |  |
> |---------|---------|
> | `scopes`   | (Opcional) Contém âmbitos que estão a ser solicitados para o consentimento do utilizador no momento do início de sessão. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs web personalizados (isto é, `api://<Application ID>/access_as_user`). |

> [!TIP]
> Em alternativa, é melhor utilizar o método `loginRedirect` para redirecionar a página atual para a página de inscrição em vez de uma janela popup.

### <a name="request-tokens"></a>Fichas de pedido

A MSAL utiliza três métodos para adquirir fichas: `acquireTokenRedirect`, `acquireTokenPopup`e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O método `acquireTokenSilent` lida com aquisições simbólicas e renovação sem qualquer interação do utilizador. Após a execução do método `loginRedirect` ou `loginPopup` pela primeira vez, `acquireTokenSilent` é o método comumente utilizado para obter fichas que são usadas para aceder a recursos protegidos para chamadas subsequentes. Chamadas para solicitar ou renovar fichas são feitas silenciosamente.

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

> |Onde  |  |
> |---------|---------|
> | `scopes`   | Contém âmbitos a serem solicitados para serem devolvidos no sinal de acesso à API. Por exemplo, `[ "mail.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs web personalizados (isto é, `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Existem situações em que é necessário forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft. Por exemplo:
* Os utilizadores podem ter de reintroduzir as suas credenciais porque a sua palavra-passe expirou.
* A sua aplicação está a solicitar o acesso a âmbitos de recursos adicionais que o utilizador necessita de consentir.
* É necessária autenticação de dois fatores.

O padrão recomendado habitual para a maioria das aplicações é ligar `acquireTokenSilent` primeiro, depois pegar a exceção, e depois ligar para `acquireTokenPopup` (ou `acquireTokenRedirect`) para iniciar um pedido interativo.

Chamar o `acquireTokenPopup` resulta numa janela pop-up para iniciar sessão. (Ou `acquireTokenRedirect` resulta em redirecionar os utilizadores para o ponto final da plataforma de identidade da Microsoft.) Nessa janela, os utilizadores precisam de interagir confirmando as suas credenciais, dando o consentimento ao recurso necessário, ou completando a autenticação de dois fatores.

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
> Este quickstart utiliza os métodos `loginRedirect` e `acquireTokenRedirect` com o Microsoft Internet Explorer, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com o manuseamento de janelas popup pelo Internet Explorer.

## <a name="next-steps"></a>Passos seguintes

Para um guia passo a passo mais detalhado sobre a construção da aplicação para este arranque rápido, consulte:

> [!div class="nextstepaction"]
> [Tutorial para iniciar sessão e ligar para o Gráfico mS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Para navegar no repo mSAL para documentação, FAQ, problemas e muito mais, ver:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
