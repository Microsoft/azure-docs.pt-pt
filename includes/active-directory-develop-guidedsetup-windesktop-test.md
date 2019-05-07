---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "60297712"
---
## <a name="test-your-code"></a>Testar o seu código

Para executar o seu projeto, no Visual Studio, selecione **F5**. Seu aplicativo **MainWindow** é apresentada, conforme mostrado aqui:

![Testar a sua aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

A primeira vez que executa a aplicação e selecione o **chamar o Microsoft Graph API** botão, lhe for pedido para iniciar sessão. Utilize uma conta do Azure Active Directory (conta escolar ou profissional) ou uma conta Microsoft (live.com, outlook.com) para testá-lo.

![Iniciar sessão na aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Forneça o consentimento para acesso à aplicação

A primeira vez que iniciar sessão na sua aplicação, também será avisado para fornecer o consentimento para permitir que a aplicação aceder ao seu perfil e iniciar a sessão in, como mostrado aqui:

![Forneça o seu consentimento para acesso à aplicação](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, deverá ver as informações de perfil de utilizador que são devolvidas pela chamada para o Microsoft Graph API. Os resultados são exibidos no **os resultados da chamada API** caixa. Informações básicas sobre o token que foi adquirido através de uma chamada para `AcquireTokenInteractive` ou `AcquireTokenSilent` devem estar visíveis na **informações sobre o Token** caixa. Os resultados contêm as seguintes propriedades:

|Propriedade  |Formato  |Descrição |
|---------|---------|---------|

|**Nome de utilizador**  | <span> user@domain.com </span> | O nome de utilizador que é utilizado para identificar o utilizador. | | **Token expira** | DateTime | A hora em que o token expira. A MSAL expande a data de expiração ao renovar o token conforme necessário. |


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Obter mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer a *user.read* âmbito para ler um perfil de utilizador. Este âmbito é adicionado automaticamente por predefinição em todos os aplicativos que está registado no Portal de registo de aplicação. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor de back-end, pode necessitar de âmbitos adicionais. O Microsoft Graph API requer a *Calendars.Read* âmbito para listar calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione a *Calendars.Read* delegado permissão para as informações de registo de aplicação. Em seguida, adicione a *Calendars.Read* definir o âmbito para o `acquireTokenSilent` chamar.

>[!NOTE]
>O utilizador pode ser pedido para consentimentos adicionais à medida que aumenta o número de âmbitos.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
