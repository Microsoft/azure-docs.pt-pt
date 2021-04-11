---
title: Utilize MSAL.js com Azure AD B2C
titleSuffix: Microsoft identity platform
description: A Biblioteca de Autenticação da Microsoft para o JavaScript (MSAL.js) permite que as aplicações funcionem com o Azure AD B2C e adquiram fichas para chamar APIs web seguras. Estas APIs web podem ser Microsoft Graph, outras APIs da Microsoft, APIs web de outros, ou a sua própria API web.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 5168dfb531c40a3efeef978abf65473ee59d2921
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255601"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Utilize a Biblioteca de Autenticação microsoft para o JavaScript funcionar com o Azure AD B2C

A [Biblioteca de Autenticação da Microsoft para o JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) permite aos desenvolvedores javaScript autenticar utilizadores com identidades sociais e locais utilizando [o Azure Ative Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C).

Ao utilizar o Azure AD B2C como um serviço de gestão de identidade, pode personalizar e controlar a forma como os seus clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações. O Azure AD B2C também lhe permite marcar e personalizar o UI que a sua aplicação exibe durante o processo de autenticação.

As seguintes secções demonstram como:

- Proteja uma API web Node.js
- Suporte a inscrição numa aplicação de uma página (SPA) e ligue para *a* API protegida da web
- Ativar o suporte de redefinição de palavra-passe

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, crie um [inquilino Azure AD B2C.](../../active-directory-b2c/tutorial-create-tenant.md)

## <a name="nodejs-web-api"></a>API Web Node.js

Os seguintes passos demonstram como uma **API web** pode usar a Azure AD B2C para se proteger e expor os âmbitos selecionados a uma aplicação do cliente.

MSAL.js para o Nó está atualmente em desenvolvimento. Para mais informações, consulte o [roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) do GitHub. Atualmente recomendamos a utilização [de data-azure-ad](https://github.com/AzureAD/passport-azure-ad), uma biblioteca de autenticação para Node.js desenvolvida e suportada pela Microsoft.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

Para proteger a sua API web com Azure AD B2C, primeiro tem de registá-la. Consulte [registar o seu pedido](../../active-directory-b2c/add-web-api-application.md) de etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Descarregue a aplicação da amostra

Descarregue a amostra como um ficheiro zip, ou clone-a do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Autenticação configure

1. Abra a *config.jsarquivada* na amostra.

2. Configure a amostra com as credenciais de aplicação que obteve anteriormente enquanto registava a sua candidatura. Altere as seguintes linhas de código substituindo os valores pelo nome do seu inquilino, identificação do cliente e nome da apólice.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Para mais informações, consulte esta [amostra de API web B2CNode.js.](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

## <a name="javascript-spa"></a>JavaScript SPA

Os seguintes passos demonstram como uma **aplicação de uma página** pode usar Azure AD B2C para se inscrever, iniciar sedub e chamar uma API web protegida.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

Para implementar a autenticação, primeiro tem de registar a sua candidatura. Consulte [registar o seu pedido](../../active-directory-b2c/tutorial-register-applications.md) de etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Descarregue a aplicação da amostra

Descarregue a amostra de [código. Arquivo ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone do repositório GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Autenticação configure

Existem dois pontos de interesse em configurar a sua aplicação:

- Configurar o ponto final da API e os âmbitos expostos
- Configurar parâmetros de autenticação e âmbitos simbólicos

1. Abra o ficheiro *apiConfig.js* na amostra.

2. Configure a amostra com os parâmetros que obteve anteriormente enquanto registava a sua API web. Altere as seguintes linhas de código substituindo os valores pelo endereço da sua API web e âmbitos expostos.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Abra o ficheiro *authConfig.js* na amostra.

1. Configure a amostra com os parâmetros que obteve anteriormente enquanto registava a sua aplicação de página única. Altere as seguintes linhas de código substituindo os valores pelo seu ClientId, metadados de autoridade e âmbitos de pedido de fichas simbólicas.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Para mais informações, consulte esta [amostra de aplicação de página única JavaScript B2C.](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

## <a name="support-password-reset"></a>Reposição da palavra-passe de suporte

Nesta secção, estenda a sua aplicação de uma página para utilizar o fluxo de utilizador de redefinição da palavra-passe Azure AD B2C. Embora MSAL.js não suporte atualmente vários fluxos de utilizador ou políticas personalizadas de forma nativa, é possível utilizar a biblioteca para lidar com casos de uso comum como o reset da palavra-passe.

Os seguintes passos assumem que já seguiu os passos na secção [DE SPA JavaScript](#javascript-spa) anterior.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Passo 1: Definir a cadeia de autoridade para o fluxo do utilizador de redefinição de palavra-passe

1. Em primeiro lugar, crie um objeto onde armazene os uris de autoridade:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Em seguida, inicialize o seu objeto MSAL com a `signInSignUp` política como padrão (ver o corte de código anterior). Quando um utilizador tenta iniciar sessão, é apresentado com o seguinte ecrã:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Ecrã de inscrição exibido por Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Passo 2: Apanhe e manuseie erros de autenticação no seu método de login

Quando um utilizador **seleciona a palavra-passe Esquecida,** a sua aplicação lança um erro que deve capturar no seu código e, em seguida, manuseia apresentando o fluxo de utilizador apropriado. Neste caso, o fluxo de reset da `b2c_1_reset` palavra-passe.

1. Estenda o seu método de inscrição da seguinte forma:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. O corte de código anterior mostra-lhe como mostrar o ecrã de reset da palavra-passe depois de ter apanhado o erro com o código `AADB2C90118` .

    Depois de redefinir a sua palavra-passe, o utilizador é devolvido à aplicação para iniciar novamente o sposição.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Ecrãs de fluxo de reset de palavra-passe mostrados por Azure AD B2C" border="false":::

    Para obter mais informações sobre códigos de erro e exceções de manuseamento, consulte [códigos de erro e de exceção da MSAL](msal-error-handling-js.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre estes conceitos Azure AD B2C:

- [Fluxos de utilizadores](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow)
- [Políticas personalizadas](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [Personalização da Experiência do Utilizador](../../active-directory-b2c/configure-user-input.md)
