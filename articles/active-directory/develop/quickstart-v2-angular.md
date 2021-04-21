---
title: 'Quickstart: Inscreva-se nos utilizadores em aplicações de página única angular - Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação Angular pode chamar uma API que requer acesso a tokens emitidos pela plataforma de identidade Microsoft.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814033"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Quickstart: Inscreva-se nos utilizadores e obtenha um token de acesso numa aplicação de página única angular

Neste arranque rápido, você descarrega e execute uma amostra de código que demonstra como uma aplicação de página única angular (SPA) pode iniciar sôm nos utilizadores e ligar para o Microsoft Graph. A amostra de código demonstra como obter um token de acesso para ligar para a API do Gráfico microsoft ou qualquer API web.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. [Crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js. ](https://nodejs.org/en/download/)
* [Código visual do estúdio](https://code.visualstudio.com/download) para editar ficheiros de projeto, ou [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para executar o projeto.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registe-se e descarregue a app quickstart
>
> Para iniciar a aplicação quickstart, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opção 1 (expresso): Registar e configurar automaticamente a aplicação e, em seguida, descarregar a amostra de código
>
> 1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a sua inscrição e, em seguida, **selecione Registar..**
> 1. No painel de arranque rápido, encontre o arranque rápido angular. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opção 2 (manual): Registar e configurar manualmente a aplicação e a amostra de código
>
> #### <a name="step-1-register-the-application"></a>Passo 1: Registar o pedido
>
> 1. Siga as instruções para [registar uma aplicação de uma página](./scenario-spa-app-registration.md) no portal Azure.
> 1. Adicione uma nova plataforma no painel de **autenticação** do registo da sua aplicação e registe o redireccionamento URI: `http://localhost:4200/` .
> 1. Este quickstart utiliza o [fluxo de subvenção implícito.](v2-oauth2-implicit-grant-flow.md) Na secção **de fluxos implícitos e híbridos,** selecione **fichas de identificação** e **fichas de acesso.** São necessários tokens de ID e fichas de acesso porque esta aplicação assina os utilizadores e chama uma API.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Passo 1: Configurar a aplicação no portal Azure
>
> Para que a amostra de código neste arranque rápido funcione, é necessário adicionar um URI de redirecionamento e ativar a `http://localhost:4200/` **concessão Implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-code-sample"></a>Passo 2: Descarregue a amostra de código
>[!div renderon="docs"]
>Para executar o projeto com um servidor web utilizando Node.js, clone o [repositório](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) de amostras ou [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Abra os ficheiros num editor como Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Passo 3: Configurar a aplicação JavaScript
>
> Na pasta *src/app,* edite *app.module.ts* e desaccie os `clientId` valores e `authority` valores em `MsalModule.forRoot` .
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Substitua estes valores:
>
>|Nome do valor|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na página **geral** do registo da sua candidatura, este é o seu valor **de ID de candidatura (cliente).** |
>|Enter_the_Cloud_Instance_Id_Here|Este é o exemplo da nuvem Azure. Para a nuvem Azure principal ou global, `https://login.microsoftonline.com` entre. Para nuvens nacionais (por exemplo, China), ver [nuvens nacionais.](./authentication-national-cloud.md)|
>|Enter_the_Tenant_Info_Here| Definir uma das seguintes opções: Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo iD do diretório (inquilino) ou nome do inquilino (por exemplo, `contoso.microsoft.com` ). Se a sua candidatura suportar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` . Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por `common` . Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor por `consumers` . |
>|Enter_the_Redirect_Uri_Here|Substitua-a por `http://localhost:4200` .|
>|cacheLocação  | (Opcional) Desa estação o armazenamento do navegador para o estado de autenticação. A predefinição é `sessionStorage`.   |
>|lojaAuthStateInCookie  | (Opcional) Identifique a biblioteca que armazena o estado de pedido de autenticação. Este estado é necessário para validar os fluxos de autenticação nos cookies do navegador. Este cookie está definido para o Internet Explorer e Microsoft Edge para acomodar esses dois navegadores. Para mais detalhes, consulte as [questões conhecidas.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues) |
>
> Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

> Para obter mais informações sobre as opções disponíveis, consulte [As aplicações do cliente Inicialize.](msal-js-initializing-client-applications.md)

> Pode encontrar o código fonte da biblioteca MSAL.js no repositório [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) no GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configuramos o seu projeto com valores das propriedades da sua aplicação.

> [!div renderon="docs"]
>
> Desloque-se para baixo no mesmo ficheiro e atualize o `graphMeEndpoint` . 
> - Substitua a cadeia `Enter_the_Graph_Endpoint_Herev1.0/me` por `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` é o ponto final que as chamadas da API serão feitas contra. Para o serviço API do Microsoft Graph principal(global), `https://graph.microsoft.com/` insira (inclua o corte dianteiro). Para obter mais informações, consulte a [documentação](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Se estiver a usar Node.js:

1. Inicie o servidor executando os seguintes comandos a partir do diretório do projeto:

   ```console
   npm install
   npm start
   ```

1. Aceda a `http://localhost:4200/`.
1. **Selecione Iniciar sessão**. A primeira vez que iniciar sessão, é solicitado que permita que a aplicação aceda ao seu perfil e o inscreva automaticamente.
1. Selecione **Profile** para ligar para o Microsoft Graph. As informações do seu perfil de utilizador são apresentadas na página.

## <a name="how-the-sample-works"></a>Como funciona a amostra

![Diagrama que mostra como funciona a aplicação da amostra neste quickstart.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Passos seguintes

Saiba como assinar num utilizador e adquirir fichas no tutorial angular:

> [!div class="nextstepaction"]
> [Tutorial angular](./tutorial-v2-angular.md)
