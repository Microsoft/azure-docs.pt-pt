---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: bb73f58c5dca5e49cdb075b046f883ffeb77c95c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203357"
---
## <a name="test-your-code"></a>Testar o seu código

### <a name="test-with-node"></a>Testar com o nó

Se não estiver a utilizar o Visual Studio, certifique-se de que o seu servidor web é iniciado.

1. Configurar o servidor para escutar numa porta TCP que baseia-se na localização dos seus **Index** ficheiro. Para o nó, inicie o servidor web para escutar a porta ao executar os seguintes comandos numa linha de comandos a partir da pasta de aplicação:

    ```bash
    npm install
    node server.js
    ```
1. Abra o browser e escreva http://<span></span>localhost:30662 ou http://<span></span>localhost: {porta} em que **porta** é a porta que está a escutar ao seu servidor web. Deverá ver o conteúdo do ficheiro Index. HTML e o **sessão** botão.

<p/><!-- -->

### <a name="test-with-visual-studio"></a>Testar com o Visual Studio

Se estiver a utilizar o Visual Studio, certifique-se selecionar a solução de projeto e prima **F5** para executar o seu projeto. O browser é aberto o http://<span></span>localhost: localização {porta} e ver o **sessão** botão.

## <a name="test-your-application"></a>Testar a sua aplicação

Depois do navegador carrega o ficheiro Index. HTML, clique em **sessão**. Será solicitado a iniciar sessão com o ponto de final de v2.0 do Microsoft Azure Active Directory (Azure AD):

![Inicie sessão na sua conta do SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Forneça o consentimento para acesso à aplicação

A primeira vez que iniciar sessão na sua aplicação, lhe for pedido para fornecer o seu consentimento para permitir que a aplicação para aceder ao seu perfil e para o início de sessão:

![Forneça o seu consentimento para acesso à aplicação](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, deverá ver as informações do perfil de utilizador devolvidas na resposta apresentada na página do Microsoft Graph API.

![Resultados esperados da chamada de API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Obter mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer a `user.read` âmbito para ler um perfil de utilizador. Este âmbito é adicionado automaticamente por predefinição em todos os aplicativos que está registado no portal de registo. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor de back-end, pode necessitar de âmbitos adicionais. Por exemplo, a Microsoft Graph API requer a `Calendars.Read` âmbito para listar calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione o `Calendars.Read` delegado permissão para as informações de registo de aplicação. Em seguida, adicione a `Calendars.Read` definir o âmbito para o `acquireTokenSilent` chamar.

>[!NOTE]
>O utilizador pode ser pedido para consentimentos adicionais à medida que aumenta o número de âmbitos.

Se uma API de back-end não requer um âmbito (não recomendado), pode utilizar o `clientId` como o âmbito em chamadas para adquirir tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]