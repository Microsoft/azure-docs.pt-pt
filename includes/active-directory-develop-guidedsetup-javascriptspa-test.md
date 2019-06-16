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
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133357"
---
## <a name="test-your-code"></a>Testar o seu código

Teste o seu código com qualquer um dos seguintes ambientes.

### <a name="test-with-nodejs"></a>Testar com node. js

Se não estiver a utilizar o Visual Studio, certifique-se de que o seu servidor web é iniciado.

1. Configurar o servidor para escutar numa porta TCP que baseia-se na localização dos seus *Index* ficheiro. Para node. js, inicie o servidor web para escutar a porta ao executar os seguintes comandos na linha de comandos a partir da pasta de aplicação:

    ```bash
    npm install
    node server.js
    ```
1. No seu browser, introduza **http://\<span >\</span > localhost:30662** ou **http://\<span >\</span > localhost: {porta}** , em que *porta* é a porta que está a escutar ao seu servidor web. Deverá ver o conteúdo da sua *Index. HTML* ficheiro e o **sessão** botão.

### <a name="test-with-visual-studio"></a>Testar com o Visual Studio

Se estiver a utilizar o Visual Studio, selecione a solução de projeto e, em seguida, selecione F5 para executar o seu projeto. O browser é aberto o http://<span></span>localhost: localização {porta} e o **sessão** botão deve estar visível.

## <a name="test-your-application"></a>Testar a sua aplicação

Depois do navegador carrega sua *Index. HTML* ficheiro, selecione **sessão**. Lhe for pedido para iniciar sessão com o ponto de extremidade de plataforma do Microsoft identity:

![A SPA do JavaScript início de sessão janela conta](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Forneça o consentimento para acesso à aplicação

A primeira vez que iniciar sessão na sua aplicação, lhe for pedido para fornecer acesso ao seu perfil e o início de sessão:

![A janela "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, as informações do perfil de utilizador são devolvidas na resposta da API do Microsoft Graph, que é apresentada na página.

![Resultados da chamada de API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Obter mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer a *user.read* âmbito para ler um perfil de utilizador. Este âmbito é adicionado automaticamente por predefinição em todos os aplicativos que está registado no portal de registo. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor de back-end, pode necessitar de âmbitos adicionais. Por exemplo, a Microsoft Graph API requer a *Calendars.Read* âmbito para listar calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione a *Calendars.Read* delegado permissão para as informações de registo de aplicação. Em seguida, adicione a *Calendars.Read* definir o âmbito para o `acquireTokenSilent` chamar.

>[!NOTE]
>O utilizador pode ser pedido para consentimentos adicionais à medida que aumenta o número de âmbitos.

Se uma API de back-end não requer um âmbito (não recomendado), pode utilizar o *clientId* como o âmbito em chamadas para adquirir tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
