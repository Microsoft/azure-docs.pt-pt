---
title: Tutorial de aplicação de uma página única JavaScript - plataforma de identidade da Microsoft Azure
description: Como as aplicações javaScript SPA podem chamar uma API que requer acesso a fichas por Azure Ative Directory v2.0 endpoint
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6f0253490d39e69d491dd5fd3ab0d0d0a32d47bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181567"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação de uma página única JavaScript (SPA)

Este guia demonstra como uma aplicação de uma página única javaScript (SPA) pode:
- Inscreva-se em contas pessoais, bem como trabalho e contas escolares
- Adquirir um token de acesso
- Ligue para a Microsoft Graph API ou outras APIs que requerem fichas de acesso do ponto final da *plataforma de identidade* da Microsoft

>[!NOTE]
> Se for novidade na plataforma de identidade da Microsoft, recomendamos que comece com o [Sign in users e obtenha um sinal de acesso num quickstart javaScript SPA](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostragerada por este guia

![Mostra como funciona a aplicação de amostragerada por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Mais informações

A aplicação de amostra criada por este guia permite que um JavaScript SPA questione o Microsoft Graph API ou um API web que aceita tokens do ponto final da plataforma de identidade da Microsoft. Neste cenário, após a adesão de um utilizador, é solicitado um sinal de acesso e adicionado aos pedidos http através do cabeçalho de autorização. Esta ficha será usada para adquirir o perfil e os e-mails do utilizador através da **MS Graph API**. A aquisição e renovação de token são tratadas pela **Microsoft Authentication Library (MSAL) para JavaScript**.

### <a name="libraries"></a>Bibliotecas

Este guia utiliza a seguinte biblioteca:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de Autenticação microsoft para JavaScript|

## <a name="set-up-your-web-server-or-project"></a>Configurar o seu servidor web ou projeto

> Prefere baixar o projeto desta amostra? [Descarregue os ficheiros do projeto.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
>
> Para configurar a amostra de código antes de executá-la, salte para o [passo de configuração](#register-your-application).

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, você precisa de um servidor web local, como [Node.js,](https://nodejs.org/en/download/) [.NET Core](https://www.microsoft.com/net/core), ou integração IIS Express com [visual studio 2017](https://www.visualstudio.com/downloads/).

* As instruções neste guia baseiam-se num servidor web construído em Node.js. Recomendamos a utilização [do Código de Estúdio Visual](https://code.visualstudio.com/download) como o seu ambiente integrado de desenvolvimento (IDE).

## <a name="create-your-project"></a>Crie o seu projeto

Certifique-se de que tem [o Node.js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar a sua aplicação. Lá, implementaremos [Express](https://expressjs.com/) um simples servidor `index.html` web Express para servir o seu ficheiro.

1. Primeiro, utilizando o terminal integrado visual Studio Code, localize a pasta do projeto e, em seguida, instale o Express utilizando o NPM.

1. Em seguida, crie um `server.js`ficheiro .js nomeado e, em seguida, adicione o seguinte código:

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

Agora tem um servidor simples para servir o seu SPA. A estrutura de pasta pretendida no final deste tutorial é a seguinte:

![uma representação de texto da estrutura de pasta SPA pretendida](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Criar o SPA UI

1. Crie `index.html` um ficheiro para o seu JavaScript SPA. Este ficheiro implementa um UI construído com **Bootstrap 4 Framework** e importa ficheiros script para configuração, autenticação e chamada API.

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
   > Pode substituir a versão de MSAL.js no script anterior pela versão mais recente lançada em [versão MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Agora, crie um ficheiro `ui.js`.js chamado , que aceda e atualize elementos DOM, e adicione o seguinte código:

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

## <a name="register-your-application"></a>Registar a sua aplicação

Antes de prosseguir com a autenticação, registe a sua candidatura no **Diretório Ativo do Azure**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a conta no canto superior direito e, em seguida, marque a sua sessão do portal para o inquilino da AD Azure que pretende utilizar.
1. Vá à plataforma de identidade da Microsoft para programar página de registos de [Aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Quando a página **Registar uma aplicação** for apresentada, introduza um nome para a sua aplicação.
1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
1. Na secção **Redirecionamento URI,** selecione a plataforma **Web** a partir da lista de drop-down e, em seguida, delineie o valor para o URL da aplicação que é baseado no seu servidor web.
1. Selecione **Registar**.
1. Na página de **visão geral** da aplicação, note o valor de ID da **Aplicação (cliente)** para posterior utilização.
1. Este arranque rápido requer que o fluxo de [subvenção implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo da aplicação registada, **selecione Autenticação**.
1. Em **configurações avançadas**, sob **a subvenção implícita,** selecione as **fichas de identificação** e as **fichas** de acesso caixas de verificação. São necessários tokens de identificação e fichas de acesso porque esta aplicação deve inscrever os utilizadores e chamar uma API.
1. Selecione **Guardar**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Delineie um URL de redirecionamento para Node.js
>
> Para node.js, pode definir a porta do servidor web no ficheiro *server.js.* Este tutorial utiliza a porta 3000, mas pode utilizar qualquer outra porta disponível.
>
> Para configurar um URL redireccionador nas informações de registo de aplicação, volte para o painel de registo de **aplicações** e faça qualquer um dos seguintes:
>
> - Definir *`http://localhost:3000/`* como url de **redirecionamento**.
> - Se estiver a utilizar uma porta *`http://localhost:<port>/`* TCP personalizada, utilize (onde * \<* a porta>é o número de porta TCP personalizado).
>   1. Copie o valor **url.**
>   1. Volte para o painel de registo de **aplicações** e colhe o valor copiado como **URL de redirecionamento**.
>

### <a name="configure-your-javascript-spa"></a>Configure o seu JavaScript SPA

Crie um novo ficheiro `authConfig.js`.js chamado , que conterá os parâmetros de configuração para autenticação, e adicione o seguinte código:

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
 - Enter_the_Application_Id_Here>é o ID de **Aplicação (cliente)** para o pedido que registou. * \<*
 - Enter_the_Cloud_Instance_Id_Here>é o exemplo da nuvem Azure. * \<* Para a nuvem azure principal *https://login.microsoftonline.com*ou global, basta entrar . Para as nuvens **nacionais** (por exemplo, China), veja [as nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)
 - Enter_the_Tenant_info_here>está definida para uma das seguintes opções: * \<*
   - Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, *contoso.microsoft.com).*
   - Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
   - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.**


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utilize a Biblioteca de Autenticação da Microsoft (MSAL) para iniciar sessão no utilizador

Crie um novo ficheiro `authPopup.js`.js chamado , que conterá a sua autenticação e lógica de aquisição simbólica, e adicione o seguinte código:

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

Depois de um **Sign In** utilizador selecionar o botão `signIn` Iniciar `loginPopup` sessão pela primeira vez, o método chama-se para iniciar sessão no utilizador. Este método abre uma janela pop-up com o ponto final da *plataforma de identidade da Microsoft* para solicitar e validar as credenciais do utilizador. Após um inserido bem sucedido, o utilizador é redirecionado para a página original *do índice.html.* Um símbolo é recebido, `msal.js`processado por , e a informação contida no token é emcached. Esta ficha é conhecida como ficha *de identificação* e contém informações básicas sobre o utilizador, como o nome do utilizador. Se planeia utilizar quaisquer dados fornecidos por este token para qualquer finalidade, tem de se certificar de que este token é validado pelo seu servidor backend para garantir que o token foi emitido a um utilizador válido para a sua aplicação.

O SPA gerado por `acquireTokenSilent` este guia `acquireTokenPopup` chama e/ou para adquirir um *token* de acesso usado para consultar a Microsoft Graph API para obter informações sobre o perfil do utilizador. Se precisar de uma amostra que valide o símbolo de identificação, veja [esta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directy-javascript-singlepageapp-dotnet-webapi-v2") aplicação de amostra no GitHub. A amostra utiliza uma API web ASP.NET para validação de tokens.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após o início do início, não pretende pedir aos utilizadores que se reatentem sempre que precisam de solicitar um sinal de acesso a um recurso. Assim, *adquirirTokenSilent* deve ser usado a maior parte do tempo para adquirir fichas. Existem, no entanto, situações em que é necessário forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft. Os exemplos incluem:

- Os utilizadores precisam de reintroduzir as suas credenciais porque a palavra-passe expirou.
- A sua aplicação está a solicitar acesso a um recurso e precisa do consentimento do utilizador.
- É necessária autenticação de dois fatores.

A *chamada adquiridaTokenPopup* abre uma janela pop-up (ou *adquireTokenRedirect redireciona* os utilizadores para o ponto final da plataforma de identidade da Microsoft). Nessa janela, os utilizadores precisam de interagir confirmando as suas credenciais, dando consentimento ao recurso necessário, ou completando a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com a aquisição e renovação simbólicas sem qualquer interação do utilizador. Depois `loginPopup` (ou) `loginRedirect`de ser executado `acquireTokenSilent` pela primeira vez, é o método comumente utilizado para obter fichas usadas para aceder a recursos protegidos para chamadas posteriores. (As chamadas para solicitar ou renovar fichas são feitas silenciosamente.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a palavra-passe do utilizador pode ter expirado. A sua aplicação pode lidar com esta exceção de duas formas:

1. Faça uma `acquireTokenPopup` chamada imediatamente, o que aciona um pedido de entrada do utilizador. Este padrão é comumente utilizado em aplicações online onde não existe conteúdo não autenticado na aplicação disponível para o utilizador. A amostra gerada por esta configuração guiada utiliza este padrão.

1. As aplicações também podem indicar ao utilizador que é necessário um sinal de inscrição interativo, para que o `acquireTokenSilent` utilizador possa selecionar o momento certo para iniciar sessão, ou a aplicação pode voltar a tentar mais tarde. Isto é comumente usado quando o utilizador pode usar outra funcionalidade da aplicação sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível na aplicação. Nesta situação, o utilizador pode decidir quando pretende iniciar sessão para aceder ao recurso protegido ou atualizar a informação desatualizada.

> [!NOTE]
> Este quickstart `loginPopup` utiliza `acquireTokenPopup` os métodos e métodos por padrão. Se estiver a usar o Internet Explorer como `loginRedirect` `acquireTokenRedirect` navegador, recomenda-se a utilização e métodos, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com a forma como o Internet Explorer lida com janelas pop-up. Se quiser ver como obter o mesmo `Redirect methods`resultado utilizando, [por favor, veja](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Ligue para a Microsoft Graph API usando o símbolo que acabou de adquirir

1. Em primeiro lugar, crie `graphConfig.js`um ficheiro .js chamado , que armazenará os seus pontos finais REST. Adicione o seguinte código:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Em que:
   - Enter_the_Graph_Endpoint_Here>é o caso da MS Graph API. * \<* Para o ponto final global do Gráfico MS `https://graph.microsoft.com`API, basta substituir esta cadeia por . Para implementações nacionais de nuvem, consulte a [Documentação API do Gráfico](https://docs.microsoft.com/graph/deployments).

1. Em seguida, crie um `graph.js`ficheiro .js chamado , que fará uma chamada REST para a Microsoft Graph API, e adicione o seguinte código:

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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre fazer uma chamada rest contra uma API protegida

Na aplicação da amostra criada `callMSGraph()` por este guia, `GET` o método é utilizado para fazer um pedido http contra um recurso protegido que requer um símbolo. O pedido devolve então o conteúdo ao chamador. Este método adiciona o token adquirido no *cabeçalho*de autorização http . Para a aplicação de amostra criada por este guia, o recurso é o *ponto* final do Microsoft Graph API Me, que exibe as informações de perfil do utilizador.

## <a name="test-your-code"></a>Teste o seu código

1. Configure o servidor para ouvir uma porta TCP baseada na localização do seu ficheiro *index.html.* Para node.js, inicie o servidor web para ouvir a porta executando os seguintes comandos numa linha de comando saem da pasta de aplicação:

   ```bash
   npm install
   npm start
   ```
1. No seu navegador, entre **http://localhost:3000** ou **http://localhost:{port}**, onde a *porta* é a porta que o seu servidor web está a ouvir. Deve ver o conteúdo do seu ficheiro *index.html* e o botão **Sign In.**

## <a name="test-your-application"></a>Teste a sua candidatura

Depois de o navegador carregar o ficheiro *index.html,* selecione 'Iniciar ' **'Sem'selecione Iniciar**o tempo . É-lhe pedido que assine sessão com o ponto final da plataforma de identidade da Microsoft:

![A janela de inscrição da conta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que inscreveu a sua candidatura, é-lhe pedido que lhe conceda acesso ao seu perfil e o inscreva:

![A janela "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, as informações do perfil do utilizador são devolvidas na resposta da Microsoft Graph API que é exibida:

![Resultados da chamada da Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer que o *utilizador.leia* o âmbito para ler o perfil de um utilizador. Por predefinição, este âmbito é adicionado automaticamente em todas as aplicações registadas no portal de registo. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor back-end, podem requerer âmbitos adicionais. Por exemplo, a Microsoft Graph API requer o âmbito *Mail.Read* para listar os e-mails do utilizador.

> [!NOTE]
> O utilizador pode ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

Se uma API de back-end não necessitar de um âmbito (não recomendado), pode utilizar o *clienteId* como âmbito nas chamadas para adquirir fichas.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
