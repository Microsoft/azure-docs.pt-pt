---
title: Inscreva-se em utilizadores em aplicações de página única JavaScript com código auth / Azure
titleSuffix: Microsoft identity platform
description: Saiba como uma aplicação JavaScript pode chamar uma API que requer acesso a fichas usando a plataforma de identidade da Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871517"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Quickstart: Inscreva-se nos utilizadores e obtenha um sinal de acesso num JavaScript SPA usando o Fluxo de Código Auth 

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem mudar antes da disponibilidade geral (GA).


Este quickstart utiliza MSAL.js 2.0 com o fluxo do Código de Autorização. Para utilizar mSAL.js 1.0 com o fluxo implícito, veja [este arranque rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

Neste arranque rápido, você usa uma amostra de código para saber como uma aplicação de uma página única JavaScript (SPA) pode assinar em utilizadores de contas pessoais, contas de trabalho e contas escolares. Um JavaScript SPA também pode obter um sinal de acesso para ligar para o Microsoft Graph API ou qualquer API web. Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma subscrição Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) (para editar ficheiros de projeto)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registe-se e descarregue a sua aplicação quickstart
> Para iniciar a sua aplicação de arranque rápido, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Express): Registe-se e configure automaticamente a sua aplicação e, em seguida, descarregue a sua amostra de código
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com).
> 1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a conta no canto superior direito e, em seguida, detete a sua sessão do portal para o inquilino azure Ative Directory (Azure AD) que deseja utilizar.
> 1. Selecione [Registos das aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Introduza um nome para a aplicação.
> 1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
> 1. Selecione **Registar**.
> 1. Vá ao painel de arranque rápido e siga as instruções para descarregar e configure automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registe-se e configure manualmente a sua aplicação e amostra de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com).
>
> 1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito e, em seguida, marque a sua sessão do portal para o inquilino da AD Azure que deseja utilizar.
> 1. Selecione [Registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza um nome para a sua aplicação.
> 1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
> 1. Selecione **Registar**. Na página de **visão geral** da aplicação, note o valor de ID da **Aplicação (cliente)** para posterior utilização.
> 1. No painel esquerdo da aplicação registada, **selecione Autenticação**.
> 1. Em **configurações**de plataforma, selecione **Adicionar uma plataforma**. Abre-se um painel à esquerda. Lá, selecione a região **de Aplicações de Página Única.**
> 1. Ainda à esquerda, detete o `http://localhost:3000/`valor **URI redirecionamento** para . 
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure
> Para que a amostra de código neste arranque `redirectUri` rápido `http://localhost:3000/`funcione, é necessário adicionar um como .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando o Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configure a sua aplicação JavaScript
>
> Na pasta da *aplicação,* editar *authConfig.js* `clientID`, e definir os valores `authority` e `redirectUri` valores abaixo `msalConfig`de .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Em que:
> - Enter_the_Application_Id_Here>é o ID de **Aplicação (cliente)** para o pedido que registou. * \<*
> - Enter_the_Cloud_Instance_Id_Here>é o exemplo da nuvem Azure. * \<* Para a nuvem azure principal *https://login.microsoftonline.com/* ou global, basta entrar . Para as nuvens **nacionais** (por exemplo, China), veja [as nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)
> - Enter_the_Tenant_info_here>está definida para uma das seguintes opções: * \<*
>    - Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, *contoso.microsoft.com).*
>    - Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
>    - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.**
> - Enter_the_Redirect_Uri_Here>é * \<*`http://localhost:3000`
> > [!TIP]
> > Para encontrar os valores de Id de **Aplicação (cliente),** **ID de Diretório (inquilino)** e tipos de **conta suportada,** vá para a página **de visão geral** do registo da aplicação no portal Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação.

> [!div renderon="docs"]
>
> Em seguida, ainda na mesma pasta, editar o ficheiro `graphMeEndpoint` `graphMailEndpoint` *graphfig.js* para definir o e para o `apiConfig` objeto.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Enter_the_Graph_Endpoint_Here>é o ponto final contra o que serão feitas chamadas da API. * \<* Para o serviço principal ou global `https://graph.microsoft.com`da Microsoft Graph API, introduza . Para mais informações, consulte a [implantação da nuvem nacional.](https://docs.microsoft.com/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Executar o projeto com um servidor web utilizando [o Node.js:](https://nodejs.org/en/download/)

1. Para iniciar o servidor, execute os seguintes comandos a partir do diretório do projeto:
    ```bash
    npm install
    npm start
    ```
1. Navegue para `http://localhost:3000/`.

1. Selecione Iniciar o **sessão** para iniciar o processo de início de sessão e, em seguida, ligue para a Microsoft Graph API.

    A primeira vez que faz o insessão, é solicitado que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de assinado com sucesso, as informações do seu perfil de utilizador devem ser exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

![Como funciona a amostra JavaScript SPA: 1. O SPA inicia o início do início. 2. O SPA adquire um símbolo de identificação da plataforma de identidade da Microsoft. 3. As chamadas de SPA adquirem ficha. 4. A plataforma de identidade da Microsoft devolve um token de Acesso ao SPA. 5. O SPA faz e http GET solicita com o Token de Acesso à Microsoft Graph API. 6. O Graph API devolve uma resposta HTTP ao SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A biblioteca MSAL.js assina nos utilizadores e solicita os tokens que são usados para aceder a uma API protegida pela plataforma de identidade da Microsoft. O ficheiro *index.html* da amostra contém uma referência à biblioteca:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> Pode substituir a versão anterior pela versão mais recente lançada nos lançamentos da [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Em alternativa, se tiver node.js instalado, pode descarregar a versão mais recente utilizando o Node.js Package Manager (npm):

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Passos seguintes

O [repo MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) contém documentação adicional da biblioteca, uma FAQ, e fornece suporte ao problema.

Para um guia passo a passo mais detalhado sobre a construção da aplicação para este arranque rápido, consulte:

> [!div class="nextstepaction"]
> [Tutorial para iniciar sessão e ligar para o Gráfico mS](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
