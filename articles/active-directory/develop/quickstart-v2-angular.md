---
title: Inscreva-se em utilizadores em aplicações angulares de uma página única - Azure
titleSuffix: Microsoft identity platform
description: Saiba como uma aplicação Angular pode chamar uma API que requer fichas de acesso utilizando a plataforma de identidade da Microsoft.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 4b6a2481c18314a44470a020033ffdc4ba1d7259
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380019"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Quickstart: Inscreva-se nos utilizadores e obtenha um sinal de acesso numa aplicação de uma única página angular

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem mudar antes da disponibilidade geral (GA).

Neste arranque rápido, você usa uma amostra de código para saber como uma aplicação angular de uma página única (SPA) pode inscrever utilizadores que tenham contas pessoais da Microsoft, contas de trabalho ou contas escolares. Um SPA angular também pode obter um sinal de acesso para ligar para o Microsoft Graph API ou qualquer API web.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. [Crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js.](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) para editar ficheiros de projetos, ou [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para executar o projeto.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registe-se e descarregue a app quickstart
> Para iniciar a aplicação quickstart, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Opção 1 (expresso): Registe-se e configure automaticamente a aplicação e, em seguida, descarregue a amostra de código
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com).
> 1. Se a sua conta tiver acesso a mais de um inquilino, selecione a conta no canto superior direito e, em seguida, detete a sua sessão do portal para o inquilino azure Ative Directory (Azure AD) que pretende utilizar.
> 1. Abra o painel de [inscrições](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) da nova App no portal Azure.
> 1. Insira um nome para a sua candidatura e, em seguida, selecione **Registar**.
> 1. Vá ao painel de arranque rápido e veja o arranque rápido angular. Siga as instruções para descarregar e configure automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Opção 2 (manual): Registe e configure manualmente a aplicação e a amostra de código
>
> #### <a name="step-1-register-the-application"></a>Passo 1: Registar o pedido
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com).
> 1. Se a sua conta tiver acesso a mais de um inquilino, selecione a sua conta no canto superior direito e marque a sua sessão de portal para o inquilino da AD Azure que pretende utilizar.
> 1. Siga as instruções para [registar uma aplicação de uma página única](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) no portal Azure.
> 1. Adicione uma nova plataforma no painel de **autenticação** do registo `http://localhost:4200/`da sua aplicação e registe o redirecionamento URI: .
> 1. Este quickstart usa o fluxo implícito de [subvenção.](v2-oauth2-implicit-grant-flow.md) Selecione as definições implícitas de **subvenção** para **fichas de ID** e **fichas de acesso**. São necessários tokens de identificação e fichas de acesso porque esta aplicação assina nos utilizadores e chama a API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Passo 1: Configure a aplicação no portal Azure
> Para a amostra de código para este arranque rápido funcionar, **http://localhost:4200/** você precisa adicionar um URI redirecionamento como e ativar **a concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-code-sample"></a>Passo 2: Descarregue a amostra de código
>[!div renderon="docs"]
>Para executar o projeto com um servidor web utilizando node.js, [clone o repositório de amostras](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) ou [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Abra os ficheiros usando um editor como Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Passo 3: Configure a aplicação JavaScript
>
> Na pasta *src/app,* edite *app.module.ts* e desempente os `clientId` valores e valores `authority` em . `MsalModule.forRoot`
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
>|Nome do valor|Descrição|
>|---------|---------|
>|Enter_the_Application_Id_Here|Na página **geral** do registo da sua candidatura, este é o seu valor de **ID (cliente).** |
>|Enter_the_Cloud_Instance_Id_Here|Este é o caso da nuvem Azure. Para a nuvem azure principal **https://login.microsoftonline.com**ou global, entre . Para as nuvens nacionais (por exemplo, China), veja [as nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)|
>|Enter_the_Tenant_Info_Here| Definir para uma das seguintes opções: Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo nome de ID de diretório (inquilino) ou nome de inquilino (por exemplo, **contoso.microsoft.com).** Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por **organizações.** Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.** |
>|Enter_the_Redirect_Uri_Here|Substitua **http://localhost:4200**por .|
>|cacheLocalização  | (Opcional) Delineie o armazenamento do navegador para o estado de autenticação. O predefinido é **a sessãoStorage**.   |
>|lojaAuthStateInCookie  | (Opcional) Identifique a biblioteca que armazena o estado de pedido de autenticação. Este estado é obrigado a validar os fluxos de autenticação nos cookies do navegador. Este cookie está definido para o Internet Explorer e Edge para acomodar esses dois navegadores. Para mais detalhes, consulte as [questões conhecidas.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues) |
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

Para obter mais informações sobre as opções configuráveis disponíveis, consulte [Inicialize as aplicações do cliente.](msal-js-initializing-client-applications.md) 

Pode encontrar o código fonte para a biblioteca MSAL.js no repositório [AzureAD/microsoft-autenticação-biblioteca-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) no GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Passo 3: Executar o projeto

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Se estiver a usar o Node.js:

1. Inicie o servidor executando os seguintes comandos a partir do diretório do projeto:

   ```console
   npm install
   npm start
   ```

1. Navegue **http://localhost:4200/** para .
1. Selecione **Login**.
1. Selecione **Perfil** para ligar para o Microsoft Graph.

Depois de o navegador carregar a aplicação, selecione **Login**. A primeira vez que começa a fazer o início do sessão, é-lhe pedido que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de ter assinado com sucesso, selecione **Perfil**, e as informações do seu perfil de utilizador serão exibidas na página.

## <a name="how-the-sample-works"></a>Como funciona a amostra

![Diagrama que mostra como funciona a aplicação de amostra sintetizadora neste quickstart](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a assinar um utilizador e adquira fichas no tutorial angular:

> [!div class="nextstepaction"]
> [Tutorial angular](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

