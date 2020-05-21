---
title: Inscreva-se em utilizadores em aplicações de página única JavaScript (SPA) com código auth / Azure
titleSuffix: Microsoft identity platform
description: Saiba como uma aplicação JavaScript pode chamar uma API que requer acesso a fichas usando a plataforma de identidade da Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 0ba4531ed15630a8887cb7be843a00ba23a439cc
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682014"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Quickstart: Inscreva-se nos utilizadores e obtenha um sinal de acesso num JavaScript SPA utilizando o fluxo de código auth

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem mudar antes da disponibilidade geral (GA).

Neste arranque rápido, executa uma amostra de código que demonstra como uma aplicação de uma página única javaScript (SPA) pode inscrever utilizadores de contas pessoais, contas de trabalho e contas escolares utilizando o fluxo de código de autorização. A amostra de código também demonstra obter um sinal de acesso para chamar uma API web, neste caso a API do Microsoft Graph. Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

Este quickstart utiliza MSAL.js 2.0 com o fluxo de código de autorização. Para um quickstart semelhante que utiliza MSAL.js 1.0 com o fluxo implícito, consulte [Quickstart: Inscreva-se nos utilizadores em aplicações de página única JavaScript](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma subscrição Azure gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

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
> 1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito e, em seguida, marque a sua sessão do portal para o inquilino da AD Azure que deseja utilizar.
> 1. Selecione [Registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza um nome para a sua aplicação.
> 1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
> 1. Selecione **Registar**. Na página de **visão geral** da aplicação, note o valor de ID da **Aplicação (cliente)** para posterior utilização.
> 1. No painel esquerdo da aplicação registada, **selecione Autenticação**.
> 1. Sob **as configurações da Plataforma,** selecione **Adicionar uma plataforma**. No painel que abre a aplicação de **página única.**
> 1. Desloque o valor **URI redirecionamento** para `http://localhost:3000/` .
> 1. Selecione **Configurar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure
> Para que a amostra de código neste arranque rápido funcione, é necessário adicionar um `redirectUri` como `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando o Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configure a sua aplicação JavaScript
>
> Na pasta da *aplicação,* abra o ficheiro *authConfig.js* e atualize o `clientID` , e `authority` `redirectUri` valores no `msalConfig` objeto.
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Modificar os valores na `msalConfig` secção aqui descrita:
>
> - `Enter_the_Application_Id_Here`é o ID de **Aplicação (cliente)** para o pedido que registou.
> - `Enter_the_Cloud_Instance_Id_Here`é o exemplo da nuvem Azure. Para a nuvem azure principal ou global, entre `https://login.microsoftonline.com/` . Para as nuvens **nacionais** (por exemplo, China), veja [as nuvens nacionais.](authentication-national-cloud.md)
> - `Enter_the_Tenant_info_here`é definido para um dos seguintes:
>   - Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo nome de Id do **Arrendatário** ou **inquilino**. Por exemplo, `contoso.microsoft.com`.
>   - Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
>   - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por `common` . **Para este arranque rápido,** utilize `common` .
>   - Para restringir o suporte apenas a *contas pessoais*da Microsoft, substitua este valor por `consumers` .
> - `Enter_the_Redirect_Uri_Here` é `http://localhost:3000/`.
>
> O `authority` valor no seu *authConfig.js* deve ser semelhante ao seguinte se estiver a usar a nuvem azure principal (global):
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Para encontrar os valores de Id de **Aplicação (cliente),** **ID de Diretório (inquilino)** e tipos de **conta suportada,** vá para a página **de visão geral** do registo da aplicação no portal Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação.

> [!div renderon="docs"]
>
> Em seguida, ainda na mesma pasta, editar o ficheiro *graphConfig.js* e atualizar os `graphMeEndpoint` valores e `graphMailEndpoint` valores no `apiConfig` objeto.
>
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
> `Enter_the_Graph_Endpoint_Here`é o ponto final contra o que as chamadas da API serão feitas contra. Para o principal (global) serviço Microsoft Graph API, introduza `https://graph.microsoft.com/` (inclua o trail-slash para a frente). Para obter mais informações sobre o Microsoft Graph sobre as nuvens nacionais, consulte a [implantação da nuvem nacional.](https://docs.microsoft.com/graph/deployments)
>
> Os `graphMeEndpoint` `graphMailEndpoint` valores e valores no ficheiro *graphConfig.js* devem ser semelhantes aos seguintes se estiver a utilizar o serviço principal (global) Microsoft Graph API:
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Executar o projeto com um servidor web utilizando node.js:

1. Para iniciar o servidor, execute os seguintes comandos a partir do diretório do projeto:
    ```console
    npm install
    npm start
    ```
1. Navegue para `http://localhost:3000/`.

1. Selecione Iniciar o **sessão** para iniciar o processo de início de sessão e, em seguida, ligue para a Microsoft Graph API.

    A primeira vez que faz o insessão, é solicitado que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de assinado com sucesso, as informações do seu perfil de utilizador devem ser exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

:::image type="content" source="media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagrama que mostra o fluxo de código de autorização para uma aplicação de uma única página":::

### <a name="msaljs"></a>msal.js

A biblioteca MSAL.js assina nos utilizadores e solicita os tokens que são usados para aceder a uma API protegida pela plataforma de identidade da Microsoft. O ficheiro *index.html* da amostra contém uma referência à biblioteca:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Se tiver node.js instalado, pode descarregar a versão mais recente utilizando o Node.js Package Manager (npm):

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Próximos passos

Para um guia passo a passo mais detalhado sobre a construção da aplicação utilizada neste quickstart, consulte o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial para iniciar sessão e ligar para o Gráfico de MS >](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
