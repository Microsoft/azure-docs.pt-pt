---
title: 'Quickstart: Inicie súmitos em aplicativos de página única JavaScript React (SPA) com código de auth e ligue para o Microsoft Graph | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, saiba como uma aplicação de página única JavaScript React (SPA) pode iniciar sômitos em utilizadores de contas pessoais, contas de trabalho e contas escolares utilizando o fluxo de código de autorização e ligue para o Microsoft Graph.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 3df3d4a3e87f67678833f097a1e2aa3633a5991e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096433"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>Quickstart: Inicie e obtenha um token de acesso num React SPA usando o fluxo de código auth

Neste arranque rápido, descarrega e executa uma amostra de código que demonstra como uma aplicação de página única JavaScript React (SPA) pode iniciar sôm nos utilizadores e ligar para o Microsoft Graph usando o fluxo de código de autorização. A amostra de código demonstra como obter um token de acesso para ligar para a API do Gráfico microsoft ou qualquer API web. 

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

Este quickstart utiliza o MSAL React com o fluxo de código de autorização. Para um quickstart semelhante que utiliza MSAL.js com o fluxo implícito, consulte [Quickstart: Inicie nos utilizadores em aplicações de página única JavaScript](./quickstart-v2-javascript.md).

Esta característica [!INCLUDE [active-directory-develop-preview](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie gratuitamente uma subscrição Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registe-se e baixe a sua aplicação de arranque rápido
> Para iniciar a sua aplicação de arranque rápido, utilize qualquer uma das seguintes opções.
>
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Expresso): Registar e configurar automaticamente a sua app e, em seguida, descarregar a sua amostra de código
>
> 1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a aplicação.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**.
> 1. Vá ao painel de arranque rápido e siga as instruções para descarregar e configurar automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registar e configurar manualmente a sua aplicação e amostra de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza um nome para a sua aplicação.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**. Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. Nas **configurações da Plataforma**, selecione Adicionar uma **plataforma**. No painel que abre selecione **aplicação de página única**.
> 1. Desa estale o valor **de redirecionamento URIs** para `http://localhost:3000/` . Esta é a porta padrão que o NodeJS irá ouvir na sua máquina local. Devolveremos a resposta de autenticação a este URI depois de autenticar com sucesso o utilizador. 
> 1. Selecione **Configurar** para aplicar as alterações.
> 1. Nas **Configurações da Plataforma** expande a **aplicação de página única**.
> 1. Confirme que nos **tipos de Subvenção** ![ já configurado o seu ](media/quickstart-v2-javascript/green-check.png) URI de redirecionamento é elegível para o Fluxo de Código de Autorização com PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código neste arranque rápido funcione, adicione um **URI de redirecionamento** de `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar a sua aplicação JavaScript
>
> Na pasta *src,* abra o ficheiro *authConfig.js* e atualize o `clientID` , e `authority` `redirectUri` valores no `msalConfig` objeto.
>
> ```javascript
> /**
> * Configuration object to be passed to MSAL instance on creation. 
> * For a full list of MSAL.js configuration parameters, visit:
> * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md 
> */
> export const msalConfig = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>        redirectUri: "Enter_the_Redirect_Uri_Here"
>    },
>    cache: {
>        cacheLocation: "sessionStorage", // This configures where your cache will be stored
>        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    },
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Modifique os valores na `msalConfig` secção descrita aqui:
>
> - `Enter_the_Application_Id_Here` é o **ID de Aplicação (cliente)** para a aplicação que registou.
> - `Enter_the_Cloud_Instance_Id_Here` é o exemplo da nuvem Azure. Para a nuvem Azure principal ou global, `https://login.microsoftonline.com/` entre. Para nuvens **nacionais** (por exemplo, China), ver [nuvens nacionais.](authentication-national-cloud.md)
> - `Enter_the_Tenant_info_here` é definido para um dos seguintes:
>   - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino.** Por exemplo, `contoso.microsoft.com`.
>   - Se a sua candidatura suportar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
>   - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por `common` . **Para este arranque rápido,** utilize `common` .
>   - Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor por `consumers` .
> - `Enter_the_Redirect_Uri_Here` é `http://localhost:3000/`.
>
> O `authority` valor do seu *authConfig.js* deve ser semelhante ao seguinte se estiver a usar a nuvem Azure principal (global):
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Para encontrar os valores de ID de **aplicação (cliente),** **ID de diretório (inquilino)** e **tipos de conta suportados,** aceda à **página** geral do registo da aplicação no portal Azure.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configuramos o seu projeto com valores das propriedades da sua aplicação.

> [!div renderon="docs"]
>
> Desloque-se para baixo no mesmo ficheiro e atualize o `graphMeEndpoint` . 
> - Substitua a cadeia `Enter_the_Graph_Endpoint_Herev1.0/me` por `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` é o ponto final que as chamadas da API serão feitas contra. Para o serviço API do Microsoft Graph principal(global), `https://graph.microsoft.com/` insira (inclua o corte dianteiro). Para obter mais informações, consulte a [documentação](/graph/deployments).
>
>
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>    export const graphConfig = {
>        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me"
>    };
> ```
>
>
#### <a name="step-4-run-the-project"></a>Passo 4: Executar o projeto

Executar o projeto com um servidor web utilizando Node.js:

1. Para iniciar o servidor, execute os seguintes comandos a partir do diretório do projeto:
    ```console
    npm install
    npm start
    ```
1. Navegue para `http://localhost:3000/`.

1. Selecione **Iniciar s-in** para iniciar o processo de início e, em seguida, ligue para a API do Gráfico microsoft.

    A primeira vez que iniciar sessão, é solicitado que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de ter assinado com sucesso, clique na **Informação** do Perfil de Pedido para mostrar as informações do seu perfil na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

![Diagrama mostrando o fluxo de código de autorização para uma aplicação de uma página.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

O MSAL.js sinais de biblioteca nos utilizadores e solicita os tokens que são usados para aceder a uma API que está protegida pela plataforma de identidade da Microsoft.

Se tiver Node.js instalado, pode descarregar a versão mais recente utilizando o Node.js Package Manager (npm):

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>Passos seguintes

Para obter um guia passo a passo detalhado sobre a construção da aplicação de fluxo de código auth utilizando o JavaScript de baunilha, consulte o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial para iniciar sincê-lo e chamar MS Graph](./tutorial-v2-javascript-auth-code.md)