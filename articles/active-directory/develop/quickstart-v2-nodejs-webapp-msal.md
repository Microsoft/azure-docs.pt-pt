---
title: 'Quickstart: Adicione autenticação a uma aplicação web nó com nó MSAL | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste arranque rápido, aprende-se a implementar a autenticação com uma aplicação web Node.js e a Microsoft Authentication Library (MSAL) para Node.js.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 5ca8c41dc1e6a05975227555abd91f5d6725285a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092183"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Quickstart: Inicie os utilizadores e obtenha um token de acesso numa aplicação web nó usando o fluxo de código auth

Neste arranque rápido, você descarrega e execute uma amostra de código que demonstra como uma Node.js aplicação web pode assinar nos utilizadores usando o fluxo de código de autorização. A amostra de código também demonstra como obter um token de acesso para ligar para a Microsoft Graph API. 

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

Este quickstart utiliza a Biblioteca de Autenticação da Microsoft para Node.js (nó MSAL) com o fluxo de código de autorização.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie gratuitamente uma subscrição Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registe-se e baixe a sua aplicação de arranque rápido
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Desa estale o valor **URI de redirecionamento** para `http://localhost:3000/redirect` .
> 1. Selecione **Registar**. 
> 1. Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
> 1. Under **Manage**, selecione **Certificados & segredos**  >  **Novo segredo do cliente**.  Deixe a descrição em branco e a expiração predefinido e, em seguida, **selecione Adicionar**.
> 1. Note o **valor** do Segredo do **Cliente** para posterior utilização.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Passo 3: Configurar a sua app Nó
>
> Extraia o projeto e abria a pasta *ms-identidade-node-main,* e depois abra o ficheiro *index.js.*
> Desa cos para `clientID` o **ID da Aplicação (cliente).**
> Desa cos para `clientSecret` o **segredo** do **Cliente.**
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Modifique os valores na `config` secção descrita aqui:
>
> - `Enter_the_Application_Id_Here` é o **ID de Aplicação (cliente)** para a aplicação que registou.
> - `Enter_the_Client_Secret_Here` é o **valor** do segredo do **Cliente** para a aplicação que registou.
>
> O `authority` valor predefinido representa a nuvem Azure principal (global):
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Para encontrar o valor do ID da **Aplicação (cliente),** aceda à página **geral** do registo da aplicação no portal Azure. Aceda aos **certificados & segredos** para recuperar ou gerar um novo **segredo do Cliente.**
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Executar o projeto utilizando Node.js:

1. Para iniciar o servidor, execute os seguintes comandos a partir do diretório do projeto:
    ```console
    npm install
    npm start
    ```
1. Navegue para `http://localhost:3000/`.

1. Selecione **Iniciar s-in** para iniciar o processo de início de sindes.

    A primeira vez que iniciar sessão, é solicitado que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de ter assinado com sucesso, verá uma mensagem de registo na linha de comando.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

A amostra, quando executada, acolhe um servidor web na localidade, porta 3000. Quando um navegador da Web acede a este site, a amostra redireciona imediatamente o utilizador para uma página de autenticação da Microsoft. Por isso, a amostra não contém nenhum *html* ou elementos de exibição. O sucesso da autenticação exibe a mensagem: "OK".

### <a name="msal-node"></a>Nó MSAL

A biblioteca MSAL Node assina nos utilizadores e solicita os tokens que são usados para aceder a uma API que está protegida pela plataforma de identidade microsoft. Você pode baixar a versão mais recente usando o Node.js Package Manager (npm):

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar Auth a uma aplicação web existente - a amostra de código GitHub >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
