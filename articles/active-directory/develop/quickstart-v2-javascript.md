---
title: Início de JavaScript rápido do Azure AD v2 | Documentos da Microsoft
description: Saiba como os aplicativos JavaScript podem chamar uma API que precisam de tokens de acesso ao ponto final de v2.0 do Azure Active Directory
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
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe8c2287da7a7eabc26ff134d8bb44c5e45085f1
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203052"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Início rápido: Iniciar sessão dos utilizadores e adquirir um token de acesso a partir de uma aplicação do JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, irá aprender como usar um código de exemplo que demonstra como um JavaScript a aplicação de página única (SPA) pode iniciar sessão em contas pessoais, trabalhar e escolar contas, obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API web.

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-javascript/javascriptspa-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registre-se e transferir a aplicação de início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao [Portal do Azure - Registo de Aplicação (Pré-visualização)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Introduza um nome para a sua aplicação e clique em **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
>
> 1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) registar uma aplicação.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **Registos de aplicações (Pré-visualização) > Novo registo**.
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
* [Transferir os ficheiros de projeto core - para um servidor web, como o node. js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extrair o ficheiro zip para uma pasta local, por exemplo, **C:\Azure-Samples**.
Para abrir os ficheiros na pasta, utilize um editor, como [Visual Studio Code](https://code.visualstudio.com/).

#### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar a sua aplicação do JavaScript

> [!div renderon="docs"]
> Sob a pasta *JavaScriptSPA*, edite `index.html` e defina a `clientID` e `authority` valores sob `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Sob a pasta *JavaScriptSPA*, edite `index.html` e substitua `applicationConfig` com:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Em que:
> - `Enter_the_Application_Id_here` - é o **ID da Aplicação (cliente)** que registou.
> - `Enter_the_Tenant_Info_Here` - está definido para uma das seguintes opções:
>   - Se a sua aplicação suportar **Contas neste diretório organizacional**, substitua este valor pelo **Id do Inquilino** ou pelo **Nome do inquilino** (por exemplo, contoso.microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Contas em quaisquer contas da Microsoft de diretório organizacional e pessoais**, substitua este valor por `common`
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

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

## <a name="more-information"></a>Mais Informações

### <a name="msaljs"></a>*msal.js*

A MSAL é a biblioteca utilizada para iniciar sessão de utilizadores e tokens utilizados para aceder a uma API protegida pelo Microsoft Azure Active Directory (Azure AD) do pedido. O guia de introdução *Index* contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Em alternativa, se tiver o nó instalado, pode baixá-lo através do npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido mostra também como inicializar a biblioteca:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Onde  |  |
> |---------|---------|
> |`ClientId`     |O ID de Aplicação da aplicação registada no portal do Azure|
> |`authority`    |É o URL da autoridade. Passando *nulo* define a autoridade de padrão para `https://login.microsoftonline.com/common`. Se a aplicação for inquilino individual (direcionamento para contas apenas num diretório), defina este valor como `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Método de retorno de chamada chamado após a autenticação redireciona para a aplicação. Aqui, `acquireTokenRedirectCallBack` é passado. Isso é nulo se loginPopup a utilizar.|
> |`options`  |Uma coleção de parâmetros opcionais. Neste caso `storeAuthStateInCookie` e `cacheLocation` são configuração opcional. Consulte a [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) para obter mais detalhes sobre as opções. |

### <a name="sign-in-users"></a>Iniciar sessão dos utilizadores

O fragmento de código seguinte mostra como iniciar sessão dos utilizadores:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Onde  |  |
> |---------|---------|
> | `scopes`   | (Opcional) Contém os âmbitos que está a ser pedidos para o consentimento do utilizador em tempo de início de sessão. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (ou seja, `api://<Application ID>/access_as_user` ). Aqui, `applicationConfig.graphScopes` é passado. |

> [!TIP]
> Em alternativa, talvez queira usar o `loginRedirect` método para redirecionar a página atual para a página de início de sessão, em vez de uma janela de pop-up.

### <a name="request-tokens"></a>Pedir tokens

A MSAL possui três métodos utilizados para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método processa a aquisições de token e a renovação sem qualquer interação do utilizador. Depois do `loginRedirect` ou `loginPopup` método é executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter os tokens que são utilizados para aceder a recursos protegidos por chamadas subsequentes. Chamadas para pedir ou renovar os tokens são feitas automaticamente.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Onde  |  |
> |---------|---------|
> | `scopes`   | Contém a ser pedidos a serem retornados no token de acesso para a API de âmbitos. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (ou seja, `api://<Application ID>/access_as_user`). Aqui, `applicationConfig.graphScopes` é passado.|

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Existem situações em que precisa forçar os utilizadores interajam com o ponto final de v2.0 do Azure AD. Por exemplo:
* Os utilizadores podem ter de reintroduzir as respetivas credenciais porque a palavra-passe expirou
* A aplicação está a solicitar acesso a âmbitos de recursos adicionais que o usuário precisa para dar consentimento para
* É precisa a autenticação de dois fatores

Padrão recomendado normal para a maioria dos aplicativos é chamar `acquireTokenSilent` em primeiro lugar, em seguida, capturar a exceção e, em seguida, chame `acquireTokenRedirect` (ou `acquireTokenPopup`) para iniciar um pedido de interativo.

Chamar o `acquireTokenPopup(scope)` resulta numa janela de pop-up para iniciar sessão (ou `acquireTokenRedirect(scope)` resulta em redirecionar utilizadores para o ponto de final de v2.0 do Azure AD) em que os utilizadores têm de interagir por qualquer uma das suas credenciais, a confirmar a dar o consentimento para o recurso necessário, ou concluir a autenticação de dois fatores.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
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
