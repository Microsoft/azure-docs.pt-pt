---
title: Início rápido do JavaScript da plataforma de identidade da Microsoft-Azure
description: Saiba como os aplicativos JavaScript podem chamar uma API que exige tokens de acesso usando a plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: c41dedf6b4fe52ba3250ada14b0cca6bbeb636af
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827120"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Início rápido: Conectar usuários e adquirir um token de acesso de um aplicativo de página única do JavaScript

Neste guia de início rápido, você usa um exemplo de código para saber como um aplicativo de página única (SPA) JavaScript pode conectar usuários de contas pessoais, contas corporativas e contas de estudante. Um SPA do JavaScript também pode obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API da Web. (Veja [como o exemplo funciona](#how-the-sample-works) para uma ilustração.)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* Qualquer uma [Visual Studio Code](https://code.visualstudio.com/download) (para editar arquivos de projeto) ou o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (ro executa o projeto como uma solução do Visual Studio).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registre e baixe seu aplicativo de início rápido
> Para iniciar seu aplicativo de início rápido, use uma das opções a seguir.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (expressa): Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.
> 1. Se sua conta fornece acesso a mais de um locatário, selecione a conta no canto superior direito e, em seguida, defina a sessão do portal para o locatário Azure Active Directory (Azure AD) que você deseja usar.
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) .
> 1. Insira um nome para seu aplicativo e selecione **registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente seu novo aplicativo.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (manual): Registrar e configurar manualmente seu aplicativo e exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.
>
> 1. Se sua conta fornece acesso a mais de um locatário, selecione sua conta no canto superior direito e, em seguida, defina a sessão do portal para o locatário do Azure AD que você deseja usar.
> 1. Acesse a página da plataforma de identidade da Microsoft para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **registrar um aplicativo** for exibida, insira um nome para seu aplicativo.
> 1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Na seção **URI** de redirecionamento, na lista suspensa, selecione a plataforma **da Web** e defina o valor como `http://localhost:30662/`.
> 1. Selecione **Registar**. Na página **visão geral** do aplicativo, observe o valor da **ID do aplicativo (cliente)** para uso posterior.
> 1. Este início rápido requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) esteja habilitado. No painel esquerdo do aplicativo registrado, selecione **autenticação**.
> 1. Na seção **Configurações avançadas** , em **concessão implícita**, marque as caixas de seleção tokens de **ID** e tokens de **acesso** . Tokens de ID e tokens de acesso são necessários, pois esse aplicativo precisa conectar usuários e chamar uma API.
> 1. Na parte superior do painel, selecione **salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um `http://localhost:30662/` URI de redirecionamento como e habilitar a **concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Baixar o projeto

Selecione a opção adequada ao seu ambiente de desenvolvimento:

* Para executar o projeto com um servidor Web usando o Node. js, [Baixe os arquivos do projeto principal](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Para abrir os arquivos, use um editor como [Visual Studio Code](https://code.visualstudio.com/).

* Adicional Para executar o projeto com o servidor IIS, [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Extraia o arquivo zip para uma pasta local (por exemplo, *C:\Azure-Samples*).

#### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar seu aplicativo JavaScript

> [!div renderon="docs"]
> Na pasta *JavaScriptSPA* , edite *index. html*e defina os `clientID` valores e `authority` em. `msalConfig`

> [!div class="sxs-lookup" renderon="portal"]
> Na pasta *JavaScriptSPA* , edite *index. html*e substitua `msalConfig` pelo código a seguir:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here",
        redirectURI: "http://localhost:30662/"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Em que:
> - *\<Enter_the_Application_Id_here >* é a **ID do aplicativo (cliente)** para o aplicativo que você registrou.
> - *\<Enter_the_Tenant_info_here >* é definido como uma das seguintes opções:
>    - Se seu aplicativo der suporte a *contas nesse diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, *contoso.Microsoft.com*).
>    - Se seu aplicativo oferecer suporte a *contas em qualquer diretório organizacional*, substitua esse valor pelas **organizações**.
>    - Se seu aplicativo oferecer suporte a *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte *somente a contas pessoais da Microsoft*, substitua esse valor por **consumidores**.
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)** , o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.
>

#### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

* Se você estiver usando o [node. js](https://nodejs.org/en/download/):

    1. Para iniciar o servidor, execute o seguinte comando no diretório do projeto:

        ```batch
        npm install
        node server.js
        ```

    1. Abra um navegador da Web e vá `http://localhost:30662/`para.
    1. Selecione **entrar** para iniciar a entrada e, em seguida, chame Microsoft Graph API.


* Se você estiver usando o [Visual Studio](https://visualstudio.microsoft.com/downloads/), selecione a solução de projeto e, em seguida, selecione F5 para executar o projeto.

Depois que o navegador carregar o aplicativo, selecione **entrar**. Na primeira vez que você entrar, você será solicitado a fornecer seu consentimento para permitir que o aplicativo acesse seu perfil e para conectá-lo. Depois que você tiver entrado com êxito, suas informações de perfil de usuário devem ser exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona o exemplo

![Como funciona o aplicativo de exemplo neste início rápido](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A biblioteca MSAL conecta os usuários e solicita os tokens que são usados para acessar uma API que é protegida pela plataforma de identidade da Microsoft. O arquivo *index. html* de início rápido contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Você pode substituir a versão anterior pela versão lançada mais recente em [versões do MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Como alternativa, se você tiver o Node. js instalado, poderá baixar a versão mais recente por meio do Gerenciador de pacotes do node. js (NPM):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido também mostra como inicializar a biblioteca MSAL:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        redirectURI: "http://localhost:30662/"
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
> |`clientId`     | A ID do aplicativo que está registrado no portal do Azure.|
> |`authority`    | Adicional A URL de autoridade que dá suporte a tipos de conta, conforme descrito anteriormente na seção de configuração. A autoridade padrão é `https://login.microsoftonline.com/common`. |
> |`redirectURI`     | O URI de resposta/redirecionamento configurado do registro do aplicativo. Nesse caso, `http://localhost:30662/`. |
> |`cacheLocation`  | Adicional Define o armazenamento do navegador para o estado de autenticação. O padrão é sessionStorage.   |
> |`storeAuthStateInCookie`  | Adicional A biblioteca que armazena o estado de solicitação de autenticação necessário para a validação dos fluxos de autenticação nos cookies do navegador. Esse cookie é definido para navegadores do IE e do Edge para atenuar determinados [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Para obter mais informações sobre as opções configuráveis disponíveis, consulte [inicializar aplicativos cliente](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Usuários de entrada

O trecho de código a seguir mostra como conectar usuários:

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
> | `scopes`   | Adicional Contém escopos que estão sendo solicitados para consentimento do usuário no momento da entrada. Por exemplo, `[ "user.read" ]` para Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`). |

> [!TIP]
> Como alternativa, talvez você queira usar o `loginRedirect` método para redirecionar a página atual para a página de entrada em vez de uma janela pop-up.

### <a name="request-tokens"></a>Tokens de solicitação

O MSAL usa três métodos para adquirir tokens `acquireTokenPopup`: `acquireTokenRedirect`, e`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método manipula aquisições e renovações de token sem qualquer interação do usuário. Depois que `loginRedirect` o `loginPopup` método ou é executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens que são usados para acessar recursos protegidos para chamadas subsequentes. Chamadas para solicitar ou renovar tokens são feitas silenciosamente.

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
> | `scopes`   | Contém os escopos que estão sendo solicitados a serem retornados no token de acesso para a API. Por exemplo, `[ "user.read" ]` para Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Há situações em que você precisa forçar os usuários a interagir com o ponto de extremidade da plataforma de identidade da Microsoft. Por exemplo:
* Talvez os usuários precisem reinserir suas credenciais porque sua senha expirou.
* Seu aplicativo está solicitando acesso a escopos de recursos adicionais aos quais o usuário precisa dar consentimento.
* A autenticação de dois fatores é necessária.

O padrão recomendado usual para a maioria dos aplicativos é `acquireTokenSilent` chamar primeiro, depois capturar a exceção e, em `acquireTokenPopup` seguida, `acquireTokenRedirect`chamar (ou) para iniciar uma solicitação interativa.

Chamar os `acquireTokenPopup` resultados em uma janela pop-up para entrar. (Ou `acquireTokenRedirect` resulta no redirecionamento de usuários para o ponto de extremidade da plataforma Microsoft Identity.) Nessa janela, os usuários precisam interagir confirmando suas credenciais, dando o consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

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
> Este guia de início `loginRedirect` rápido `acquireTokenRedirect` usa os métodos e com o Microsoft Internet Explorer, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à manipulação de janelas pop-up do Internet Explorer.

## <a name="next-steps"></a>Passos seguintes

Para obter um guia passo a passo mais detalhado sobre como criar o aplicativo para este início rápido, consulte:

> [!div class="nextstepaction"]
> [Tutorial para entrar e chamar o MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Para procurar o repositório MSAL para documentação, perguntas frequentes, problemas e muito mais, consulte:

> [!div class="nextstepaction"]
> [Repositório GitHub do MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
