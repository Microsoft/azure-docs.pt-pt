---
title: Tutorial de Aplicativo Web - Escreva aplicação web
description: Este tutorial percorre um exemplo de implementação de uma simples aplicação web. Esta secção do tutorial passa pela escrita da aplicação web.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87848031"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Escreva a aplicação web Azure para ler dados do FHIR
Agora que é capaz de se conectar ao seu servidor FHIR e dados POST, está pronto para escrever uma aplicação web que irá ler dados de FHIR. Neste passo final do tutorial, vamos percorrer a escrita e aceder à aplicação web.

## <a name="create-web-application"></a>Criar aplicação web
Em Azure, **selecione Criar um recurso** e selecione Web **App**. Certifique-se de nomear a sua aplicação web seja qual for o seu especificado no URI de redirecionamento para a aplicação do seu cliente ou volte e atualize o URI redirecionado com o novo nome. 

![Criar aplicação web](media/tutorial-web-app/create-web-app.png)

Uma vez que a aplicação web esteja disponível, **vá para o recurso**. Selecione **O Editor de Serviço de Aplicações (Pré-visualização)** em Ferramentas de Desenvolvimento à direita e, em seguida, selecione **Go**. A seleção de Go abrirá o Editor de Serviço de Aplicações. Clique à direita no espaço cinzento sob *Explore* e crie um novo ficheiro chamado **index.html**.

Abaixo está o código que pode inserir em **index.html**. Terá de atualizar os seguintes itens:
* **clientId** - Atualize com o ID da sua aplicação ao cliente. Esta ID será a mesma ID que puxou ao recuperar o seu símbolo
* **autoridade** - Atualizar com o seu ID inquilino Azure AD
* **FHIRendpoint** - Atualize o FHIRendpoint para ter o seu nome de serviço FHIR
* **âmbitos** - Atualização para refletir o URL completo para o seu público

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

A partir daqui, pode voltar ao seu recurso de aplicação web e abrir o URL encontrado na página 'Vista Geral'. Faça login para ver o paciente James Tiberious Kirk que criou anteriormente.

## <a name="next-steps"></a>Passos Seguintes
Implementou com sucesso a API Azure para fHIR, registou uma aplicação de cliente público, testou o acesso e criou uma pequena aplicação web. Confira a Azure API para funcionalidades suportadas pela FHIR como um próximo passo.

>[!div class="nextstepaction"]
>[Funcionalidades Suportadas](fhir-features-supported.md)





