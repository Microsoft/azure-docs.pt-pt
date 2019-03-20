---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 918e4016f638555bfe2dbaeaa849e963e352c78e
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203518"
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testar a consultar o Microsoft Graph API a partir da sua aplicação iOS

Para executar o código no simulador, prima **comando** + **R**.

![Testar a sua aplicação no simulador](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Quando estiver pronto para testar, selecione **chamar o Microsoft Graph API**. Quando lhe for pedido, introduza o seu nome de utilizador e palavra-passe.

### <a name="provide-consent-for-application-access"></a>Forneça o consentimento para acesso à aplicação

A primeira vez que iniciar sessão na sua aplicação, lhe for pedido para fornecer o seu consentimento para permitir que a aplicação para aceder ao seu perfil e para o início de sessão:

![Forneça o seu consentimento para acesso à aplicação](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Ver resultados da aplicação
Depois de iniciar sessão, deverá ver as informações do perfil de utilizador devolvidas pela chamada no Microsoft Graph API a **registo** secção. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Obter mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer a **user.read** âmbito para ler um perfil de utilizador. Este âmbito é adicionado automaticamente por predefinição em todos os aplicativos que está registado no portal de registo. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor de back-end, pode necessitar de âmbitos adicionais. O Microsoft Graph API requer a **Calendars.Read** âmbito para listar calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione a **Calendars.Read** delegado permissão para as informações de registo de aplicação. Em seguida, adicione a **Calendars.Read** definir o âmbito para o **acquireTokenSilent** chamar.

>[!NOTE]
>O utilizador pode ser pedido para consentimentos adicionais à medida que aumenta o número de âmbitos.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
