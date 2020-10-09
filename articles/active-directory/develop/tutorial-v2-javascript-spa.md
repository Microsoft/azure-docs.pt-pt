---
title: 'Tutorial: Criar uma aplicação de página única JavaScript que utiliza a plataforma de identidade da Microsoft para autenticação Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação de página única JavaScript (SPA) que utiliza a plataforma de identidade da Microsoft para assinar nos utilizadores e obter um token de acesso para ligar para a API do Gráfico microsoft em seu nome.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 027305d953a24de17e62aa74b33b72494b03e652
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825907"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a API do Gráfico microsoft a partir de uma aplicação de página única JavaScript (SPA)

Neste tutorial, você constrói uma aplicação de uma página (SPA) no JavaScript que pode iniciar sôs em utilizadores com contas pessoais da Microsoft ou contas de trabalho e escola, e depois adquirir um token de acesso para ligar para a API do Gráfico microsoft.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto JavaScript com `npm`
> * Registe a inscrição no portal Azure
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Adicione código para ligar para a Microsoft Graph API
> * Testar a aplicação

>[!TIP]
> Este tutorial utiliza MSAL.js v1.x que se limita a utilizar o fluxo de subvenção implícito para aplicações de uma página única. Recomendamos que todas as novas aplicações utilizem [MSAL.js 2.x e o fluxo de código de autorização com suporte AKCE e CORS.](tutorial-v2-javascript-auth-code.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/) para gerir um servidor web local.
* [Código do Estúdio Visual](https://code.visualstudio.com/download) ou outro editor para modificar ficheiros de projeto.
* Um navegador web moderno. **O Internet Explorer** não é **suportado** pela app que constrói neste tutorial devido à utilização da app de convenções [ES6.](http://www.ecma-international.org/ecma-262/6.0/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostras gerada por este guia

![Mostra como funciona a app de amostras gerada por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

A aplicação de amostra criada por este guia permite a um JavaScript SPA consultar a API do Microsoft Graph ou uma API web que aceita fichas a partir do ponto final da plataforma de identidade da Microsoft. Neste cenário, após a indicação de um utilizador, é solicitado um token de acesso e adicionado aos pedidos HTTP através do cabeçalho de autorização. Este token será utilizado para adquirir o perfil e os e-mails do utilizador através da **MS Graph API**.

A aquisição e renovação de token são tratadas pela [Microsoft Authentication Library (MSAL) para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js).

## <a name="set-up-your-web-server-or-project"></a>Configurar o seu servidor web ou projeto

> Prefere descarregar o projeto desta amostra? [Descarregue os ficheiros do projeto.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
>
> Para configurar a amostra de código antes de a executar, salte para o [passo de configuração](#register-your-application).

## <a name="create-your-project"></a>Criar o seu projeto

Certifique-se de que [ temNode.js](https://nodejs.org/en/download/) instalada e, em seguida, crie uma pasta para hospedar a sua aplicação. Lá, implementaremos um simples servidor web [Express](https://expressjs.com/) para servir o seu `index.html` ficheiro.

1. Utilizando um terminal (como o terminal integrado do Código do Estúdio Visual), localize a pasta do projeto e escreva:

   ```console
   npm init
   ```

2. Em seguida, instale as dependências necessárias:

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. Agora, crie um ficheiro .js nomeado `server.js` e, em seguida, adicione o seguinte código:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Tem agora um servidor simples para servir o seu SPA. A estrutura de pastas pretendida no final deste tutorial é a seguinte:

![uma representação de texto da estrutura de pasta SPA pretendida](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Criar o SPA UI

1. Crie um `index.html` ficheiro para o seu JAVAScript SPA. Este ficheiro implementa um UI construído com **o Quadro Bootstrap 4** e importa ficheiros de script para configuração, autenticação e chamada API.

   No `index.html` ficheiro, adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
         </div>
       </div>
       </div>
       <br>
       <br>
         <div class="col-md-4">
           <div class="list-group" id="list-tab" role="tablist">
           </div>
         </div>
         <div class="col-md-5">
           <div class="tab-content" id="nav-tabContent">
           </div>
         </div>
       </div>
       <br>
       <br>

       <!-- importing bootstrap.js and supporting js libraries -->
       <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
       <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
       <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

       <!-- importing app scripts (load order is important) -->
       <script type="text/javascript" src="./authConfig.js"></script>
       <script type="text/javascript" src="./graphConfig.js"></script>
       <script type="text/javascript" src="./ui.js"></script>

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Pode substituir a versão de MSAL.js no script anterior pela versão mais recente lançada em [MSAL.js versões](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Agora, crie um ficheiro .js `ui.js` nomeado, que aceda e atualize elementos DOM, e adicione o seguinte código:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Registar a aplicação

Antes de prosseguir com a autenticação, registe o seu pedido no **Diretório Ativo Azure**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Se a sua conta lhe der acesso a mais do que um inquilino, selecione a conta no canto superior direito e, em seguida, desemocione a sua sessão de portal para o inquilino AD AZure que pretende utilizar.
1. Aceda à plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Quando a página **Registar uma aplicação** for apresentada, introduza um nome para a sua aplicação.
1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
1. Na secção **URI de redirecionamento,** selecione a plataforma **Web** a partir da lista de drop-down e, em seguida, desaver o valor para o URL de aplicação que é baseado no seu servidor web.
1. Selecione **Registar**.
1. Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
1. Este arranque rápido requer que o [fluxo de subvenção implícito](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo da aplicação registada, selecione **Autenticação**.
1. Nas **definições Avançadas**, sob **concessão implícita,** selecione as caixas de verificação **de fichas de identificação** e **de acesso.** São necessárias fichas de ID e fichas de acesso porque esta aplicação deve assinar nos utilizadores e chamar uma API.
1. Selecione **Guardar**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Desa redirecione um URL de redirecionamento para Node.js
>
> Para Node.js, pode definir a porta do servidor web no ficheiro *server.js.* Este tutorial usa a porta 3000, mas pode usar qualquer outra porta disponível.
>
> Para configurar um URL de redirecionamento nas informações de registo de pedidos, volte para o painel de registo de **inscrição** de pedido e faça qualquer um dos seguintes:
>
> - Definido *`http://localhost:3000/`* como URL de **redirecionamento**.
> - Se estiver a utilizar uma porta TCP personalizada, utilize *`http://localhost:<port>/`* (onde *\<port>* está o número de porta TCP personalizado).
>   1. Copie o valor **url.**
>   1. Volte para o painel **de registo de aplicação** e cole o valor copiado como **URL de redirecionamento**.
>

### <a name="configure-your-javascript-spa"></a>Configure o seu JavaScript SPA

Crie um novo ficheiro .js `authConfig.js` nomeado, que conterá os seus parâmetros de configuração para autenticação, e adicione o seguinte código:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Em que:
 - *\<Enter_the_Application_Id_Here>* é o **ID de Aplicação (cliente)** para a aplicação que registou.
 - *\<Enter_the_Cloud_Instance_Id_Here>* é o exemplo da nuvem Azure. Para a nuvem Azure principal ou global, basta *https://login.microsoftonline.com* entrar. Para nuvens **nacionais** (por exemplo, China), ver [nuvens nacionais.](./authentication-national-cloud.md)
 - *\<Enter_the_Tenant_info_here>* é definida como uma das seguintes opções:
   - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino** (por exemplo, *contoso.microsoft.com).*
   - Se a sua candidatura suporta *contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
   - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por **comum**. Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor pelos **consumidores**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utilize a Biblioteca de Autenticação microsoft (MSAL) para iniciar súbbóia no utilizador

Crie um novo ficheiro .js `authPopup.js` nomeado, que conterá a sua lógica de autenticação e aquisição simbólica, e adicione o seguinte código:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Mais informações

Depois de um utilizador selecionar o botão **'Iniciar'** pela primeira vez, o `signIn` método chama para assinar no `loginPopup` utilizador. Este método abre uma janela pop-up com o ponto final da *plataforma de identidade da Microsoft* para solicitar e validar as credenciais do utilizador. Após uma sincê-in bem sucedida, o utilizador é redirecionado de volta para a página original *index.html.* Um token é recebido, processado por `msal.js` , e a informação contida no token é em cache. Este token é conhecido como o *token de ID* e contém informações básicas sobre o utilizador, como o nome de exibição do utilizador. Se pretender utilizar quaisquer dados fornecidos por este token para qualquer finalidade, tem de se certificar de que este token é validado pelo seu servidor de backend para garantir que o token foi emitido a um utilizador válido para a sua aplicação.

O SPA gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API do Microsoft Graph para obter informações sobre o perfil do utilizador. Se precisar de uma amostra que valide o token de identificação, dê uma olhada [nesta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directório-javascript-singlepageapp-dotnet-webapi-v2") aplicação de amostra no GitHub. A amostra utiliza uma API web ASP.NET para validação de fichas.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após a iniciação inicial, não pretende pedir aos utilizadores que reauttenenássem sempre que precisam de pedir um token para aceder a um recurso. Assim, *adquirir oTokenSilent* deve ser usado na maior parte do tempo para adquirir fichas. Existem situações, no entanto, em que é necessário forçar os utilizadores a interagirem com o ponto final da plataforma de identidade da Microsoft. Alguns exemplos:

- Os utilizadores precisam de reentrar nas suas credenciais porque a palavra-passe expirou.
- A sua aplicação está a solicitar o acesso a um recurso e precisa do consentimento do utilizador.
- É necessária autenticação de dois fatores.

Call *acquireTokenPopup* abre uma janela pop-up (ou *adquireTokenRedirect* redireciona os utilizadores para o ponto final da plataforma de identidade microsoft). Nessa janela, os utilizadores precisam interagir confirmando as suas credenciais, dando consentimento ao recurso necessário ou completando a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com a aquisição e renovação de símbolos sem qualquer interação do utilizador. Depois `loginPopup` de (ou `loginRedirect` ) ser executado pela primeira vez, é `acquireTokenSilent` o método comumente usado para obter fichas usadas para aceder a recursos protegidos para chamadas subsequentes. (As chamadas para solicitar ou renovar fichas são feitas silenciosamente.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a palavra-passe do utilizador pode ter expirado. A sua aplicação pode lidar com esta exceção de duas formas:

1. Faça uma chamada `acquireTokenPopup` imediatamente, o que despoleta uma solicitação de inscrição do utilizador. Este padrão é comumente utilizado em aplicações online onde não existe conteúdo não autenticado na aplicação disponível para o utilizador. A amostra gerada por esta configuração guiada utiliza este padrão.

1. As aplicações também podem fazer uma indicação visual ao utilizador de que é necessário um início de sção interativo, para que o utilizador possa selecionar o momento certo para iniciar sedutar, ou a aplicação pode voltar a tentar `acquireTokenSilent` mais tarde. Isto é normalmente utilizado quando o utilizador pode utilizar outras funcionalidades da aplicação sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível na aplicação. Nesta situação, o utilizador pode decidir quando pretender iniciar seducação para aceder ao recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este arranque rápido utiliza os `loginPopup` métodos e `acquireTokenPopup` métodos por defeito. Se estiver a utilizar o Internet Explorer como seu navegador, recomenda-se a utilização `loginRedirect` e `acquireTokenRedirect` métodos, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com a forma como o Internet Explorer lida com as janelas pop-up. Se quiser ver como obter o mesmo resultado utilizando `Redirect methods` , [por favor, veja](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Ligue para a Microsoft Graph API usando o token que acabou de adquirir

1. Em primeiro lugar, crie um ficheiro .js chamado `graphConfig.js` , que irá armazenar os seus pontos finais REST. Adicione o seguinte código:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Em que:
   - *\<Enter_the_Graph_Endpoint_Here>* é o caso da MS Graph API. Para o ponto final global da MS Graph API, basta substituir esta cadeia por `https://graph.microsoft.com` . Para implementações em nuvem nacional, consulte a [Documentação da API do Gráfico.](/graph/deployments)

1. Em seguida, crie um ficheiro .js nomeado `graph.js` , que fará uma chamada REST para a Microsoft Graph API, e adicione o seguinte código:

   ```javascript
   function callMSGraph(endpoint, token, callback) {
     const headers = new Headers();
     const bearer = `Bearer ${token}`;

     headers.append("Authorization", bearer);

     const options = {
         method: "GET",
         headers: headers
     };

     console.log('request made to Graph API at: ' + new Date().toString());

     fetch(endpoint, options)
       .then(response => response.json())
       .then(response => callback(response, endpoint))
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre fazer uma chamada REST contra uma API protegida

Na aplicação de amostra criada por este guia, o `callMSGraph()` método é utilizado para fazer um pedido HTTP contra um recurso protegido que requer um `GET` símbolo. Em seguida, o pedido devolve o conteúdo ao autor da chamada. Este método adiciona o símbolo adquirido no *cabeçalho de autorização HTTP*. Para a aplicação de amostra criada por este guia, o recurso é o ponto final do Microsoft Graph API *me,* que exibe as informações de perfil do utilizador.

## <a name="test-your-code"></a>Teste o seu código

1. Configure o servidor para ouvir uma porta TCP que se baseie na localização do seu *ficheiro Lindex.htm.* Para Node.js, inicie o servidor web para ouvir a porta executando os seguintes comandos num pedido de linha de comando da pasta de aplicação:

   ```bash
   npm install
   npm start
   ```
1. No seu navegador, insira **http://localhost:3000** **http://localhost:{port}** ou, onde a *porta* é a porta que o seu servidor web está a ouvir. Deverá ver o conteúdo do seuindex.htmficheiro *L* e o botão **Iniciar.**

Depois de o navegador carregar o ficheiro *index.html,* selecione **Iniciar Sôs.** É-lhe pedido que assine com o ponto final da plataforma de identidade da Microsoft:

![A janela de inscrição da conta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que iniciar a sua candidatura, é solicitado que lhe conceda acesso ao seu perfil e o inscreva:

![A janela "Permissões Solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, as informações do seu perfil de utilizador são devolvidas na resposta API do Microsoft Graph que é apresentada:

![Resultados da chamada API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

A API do Microsoft Graph requer que o âmbito de leitura do *utilizador.leia* para ler o perfil de um utilizador. Por predefinição, este âmbito é automaticamente adicionado em todas as aplicações registadas no portal de registo. Outros APIs para o Microsoft Graph, bem como APIs personalizados para o seu servidor back-end, podem necessitar de âmbitos adicionais. Por exemplo, a API do Gráfico microsoft requer o âmbito *Mail.Read* para listar os e-mails do utilizador.

> [!NOTE]
> O utilizador poderá ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Aprofundar o desenvolvimento de aplicações de uma página única (SPA) na plataforma de identidade da Microsoft na nossa série de cenários multi-partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicação de página única](scenario-spa-overview.md)

