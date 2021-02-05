---
title: 'Quickstart: Inicie súmitos em aplicativos de página única (SPA) javaScript angular com código de auth e ligue para o Microsoft Graph | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, saiba como uma aplicação de página única JavaScript Angular (SPA) pode iniciar sômitos em utilizadores de contas pessoais, contas de trabalho e contas escolares utilizando o fluxo de código de autorização e ligue para o Microsoft Graph.
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
ms.openlocfilehash: 466dc5b09e225876d57dfac7d39d28b88f1beb0f
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583269"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>Quickstart: Inicie e obtenha um token de acesso em um SPA Angular usando o fluxo de código auth

Neste arranque rápido, você descarrega e execute uma amostra de código que demonstra como uma aplicação de página única JavaScript Angular (SPA) pode iniciar sôm nos utilizadores e ligar para o Microsoft Graph usando o fluxo de código de autorização. A amostra de código demonstra como obter um token de acesso para ligar para a API do Gráfico microsoft ou qualquer API web. 

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

Este quickstart utiliza o MSAL Angular v2 com o fluxo de código de autorização. Para um quickstart semelhante que utiliza o MSAL Angular 1.x com o fluxo implícito, consulte [Quickstart: Assine nos utilizadores em aplicações de página única JavaScript](./quickstart-v2-angular.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie gratuitamente uma subscrição Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registe-se e baixe a sua aplicação de arranque rápido
> Para iniciar a sua aplicação de arranque rápido, utilize qualquer uma das seguintes opções.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1 (Expresso): Registar e configurar automaticamente a sua app e, em seguida, descarregar a sua amostra de código
>
> 1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portal Azure - <span class="docon docon-navigate-external x-hidden-focus"></span> Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a aplicação.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**.
> 1. Vá ao painel de arranque rápido e siga as instruções para descarregar e configurar automaticamente a sua nova aplicação.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2 (Manual): Registar e configurar manualmente a sua aplicação e amostra de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> 1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**. Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. Nas **configurações da Plataforma**, selecione Adicionar uma **plataforma**. No painel que abre selecione **aplicação de página única**.
> 1. Desa estale o valor **de redirecionamento URIs** para `http://localhost:4200/` . Esta é a porta padrão que o NodeJS irá ouvir na sua máquina local. Devolveremos a resposta de autenticação a este URI depois de autenticar com sucesso o utilizador. 
> 1. Selecione **Configurar** para aplicar as alterações.
> 1. Nas **Configurações da Plataforma** expande a **aplicação de página única**.
> 1. Confirme que nos **tipos de Subvenção** ![ já configurado o seu ](media/quickstart-v2-javascript/green-check.png) URI de redirecionamento é elegível para o Fluxo de Código de Autorização com PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código neste arranque rápido funcione, adicione um **URI de redirecionamento** de `http://localhost:4200/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-javascript/green-check.png) A sua aplicação está configurada com estes atributos.

 #### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto

> [!div renderon="docs"]
> Para executar o projeto com um servidor web utilizando Node.js, [descarregue os ficheiros do projeto principal](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto com um servidor web usando Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Passo 3: Configurar a sua aplicação JavaScript
>
> Na pasta *src,* abra a pasta da *aplicação* e, em seguida, abra o ficheiro *app.module.ts* e atualize o `clientID` , e `authority` `redirectUri` valores no `auth` objeto.
>
> ```javascript
> // MSAL instance to be passed to msal-angular
> export function MSALInstanceFactory(): IPublicClientApplication {
>   return new PublicClientApplication({
>     auth: {
>       clientId: 'Enter_the_Application_Id_Here',
>       authority: 'Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here',
>       redirectUri: 'Enter_the_Redirect_Uri_Here'
>     },
>     cache: {
>       cacheLocation: BrowserCacheLocation.LocalStorage,
>       storeAuthStateInCookie: isIE, // set to true for IE 11
>     },
>   });
> }
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Modifique os valores na `auth` secção descrita aqui:
>
> - `Enter_the_Application_Id_Here` é o **ID de Aplicação (cliente)** para a aplicação que registou.
> - `Enter_the_Cloud_Instance_Id_Here` é o exemplo da nuvem Azure. Para a nuvem Azure principal ou global, `https://login.microsoftonline.com/` entre. Para nuvens **nacionais** (por exemplo, China), ver [nuvens nacionais.](authentication-national-cloud.md)
> - `Enter_the_Tenant_info_here` é definido para um dos seguintes:
>   - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino.** Por exemplo, `contoso.microsoft.com`.
>   - Se a sua candidatura suportar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
>   - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por `common` . **Para este arranque rápido,** utilize `common` .
>   - Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor por `consumers` .
> - `Enter_the_Redirect_Uri_Here` é `http://localhost:4200/`.
>
> O `authority` valor no seu *app.module.ts* deve ser semelhante ao seguinte se estiver a usar a nuvem Azure principal (global):
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
> - `Enter_the_Graph_Endpoint_Herev1.0/me` é o ponto final que as chamadas da API serão feitas contra. Para o serviço API do Microsoft Graph principal(global), `https://graph.microsoft.com/` insira (inclua o corte dianteiro). Para obter mais informações, consulte a [documentação](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
> export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration {
>   const protectedResourceMap = new Map<string, Array<string>>();
>   protectedResourceMap.set('Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']);
>
>   return {
>     interactionType: InteractionType.Redirect,
>     protectedResourceMap
>   };
> }
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
1. Navegue para `http://localhost:4200/`.

1. Selecione **Iniciar sessão** para iniciar o processo de login e, em seguida, ligue para a API do Gráfico microsoft.

    A primeira vez que iniciar sessão, é solicitado que forneça o seu consentimento para permitir que a aplicação aceda ao seu perfil e o inscreva. Depois de ter assinado com sucesso, clique no botão **Perfil** para mostrar as informações do utilizador na página.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra

![Diagrama mostrando o fluxo de código de autorização para uma aplicação de uma página.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

O MSAL.js sinais de biblioteca nos utilizadores e solicita os tokens que são usados para aceder a uma API que está protegida pela plataforma de identidade da Microsoft. 

Se tiver Node.js instalado, pode descarregar a versão mais recente utilizando o Node.js Package Manager (npm):

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>Passos seguintes

Para obter um guia passo a passo detalhado sobre a construção da aplicação de fluxo de código auth utilizando o JavaScript de baunilha, consulte o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial para iniciar sincê-lo e chamar MS Graph](./tutorial-v2-javascript-auth-code.md)
