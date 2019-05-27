---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121806"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utilizar o Microsoft Authentication Library (MSAL) para iniciar a sessão do utilizador

1. Adicione o seguinte código para seu `index.html` dentro do ficheiro a `<script></script>` etiquetas:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Mais Informações

Depois de um usuário clica o **iniciar sessão** botão pela primeira vez, o `signIn` chamadas de método `loginPopup` para iniciar a sessão do utilizador. Este método resulta em abrir uma janela de pop-up com a *ponto final de plataforma do Microsoft identity* para solicitar e validar as credenciais do utilizador. Como resultado de um sessão com êxito-, o utilizador é redirecionado para o original *Index. HTML* página e um token é recebida, processados pelo `msal.js` e as informações contidas no token é colocado em cache. Este token é conhecido como o *token de ID* e contém informações básicas sobre o usuário, como o nome de exibição do usuário. Se planeia utilizar todos os dados fornecidos por este token para fins, terá de certificar-se de que este token é validado pelo seu servidor de back-end para garantir que o token foi emitido para um utilizador válido para a sua aplicação.

O SPA gerado por este guia de chamadas `acquireTokenSilent` e/ou `acquireTokenPopup` adquirir um *token de acesso* usado para consultar a Graph API da Microsoft para informações de perfil do usuário. Se precisar de uma amostra que valida o token de ID, dê uma olhada [isso](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub de exemplo active-directory-javascript-singlepageapp-dotnet-webapi-v2") aplicativo de exemplo no GitHub – o exemplo usa um ASP .NET API da web para validação do token.

#### <a name="getting-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Depois do inicial início de sessão, que não pretende peça aos utilizadores para autenticar toda vez que precisam para solicitar um token para aceder a um recurso – então *acquireTokenSilent* deve ser utilizada a maior parte do tempo para adquirir tokens. Há contudo situações que precisa forçar os utilizadores interajam com o ponto final de plataforma do Microsoft identity – alguns exemplos incluem:

- Os utilizadores podem ter de reintroduzir as respetivas credenciais pela palavra-passe ter expirado
- A aplicação está a pedir acesso a um recurso para o qual o utilizador tem de dar consentimento
- É precisa a autenticação de dois fatores

Chamar o *acquireTokenPopup* resulta numa janela de pop-up (ou *acquireTokenRedirect* resulta em redirecionar utilizadores para o ponto de extremidade de plataforma de identidade Microsoft) em que os utilizadores têm de interagir por qualquer uma confirmar as respetivas credenciais, a dar o consentimento para o recurso necessário, ou de concluir a autenticação de dois fatores.

#### <a name="getting-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método processa a aquisições de token e a renovação sem qualquer interação do utilizador. Após `loginPopup` (ou `loginRedirect`) é executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter os tokens utilizados para aceder a recursos protegidos por chamadas subsequentes - como chamadas para pedir ou renovar os tokens são feitas automaticamente.
`acquireTokenSilent` pode falhar em alguns casos – por exemplo, a senha do usuário expirou. Seu aplicativo pode manipular essa exceção de duas formas:

1. Fazer uma chamada para `acquireTokenPopup` imediatamente, o que resulta em pedir ao utilizador para iniciar sessão. Este padrão é muito usado em aplicativos online onde não há nenhum conteúdo não autenticado no aplicativo disponível para o utilizador. O exemplo gerado por esta configuração assistida usa esse padrão.

2. Aplicativos também podem tornar uma indicação visual para o usuário que um interativo início de sessão é necessário, para que o usuário pode selecionar o momento certo para iniciar sessão ou a aplicação pode repetir `acquireTokenSilent` num momento posterior. Isto é normalmente utilizado quando o utilizador pode utilizar outras funcionalidades da aplicação sem a ser interrompida – por exemplo, não existe conteúdo não autenticado no aplicativo. Neste caso, o usuário pode decidir quando pretende iniciar sessão para aceder ao recurso protegido ou para atualizar as informações Desatualizadas.

> [!NOTE]
> Este início rápido utiliza a `loginRedirect` e `acquireTokenRedirect` métodos quando o browser utilizado é o Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionadas com a manipulação de janelas pop-ups pelo navegador do Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chamar o Microsoft Graph API com o token de que obteve apenas

Adicione o seguinte código para seu `index.html` dentro do ficheiro a `<script></script>` etiquetas:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Obter mais informações sobre como fazer uma chamada REST em relação a uma API protegida

No aplicativo de exemplo criado por este guia, o `callMSGraph()` método é utilizado para fazer um HTTP `GET` em relação a um recurso protegido que necessita de um token do pedido e, em seguida, devolver o conteúdo para o chamador. Este método adiciona o token obtido no *cabeçalho de autorização de HTTP*. Para o aplicativo de exemplo criado por este guia, o recurso é o Microsoft Graph API *me* ponto de extremidade – que exibe informações de perfil do usuário.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para terminar sessão do utilizador

Adicione o seguinte código para seu `index.html` dentro do ficheiro a `<script></script>` etiquetas:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
