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
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: c8e5e4f826d7835a1fd38d1db5bfeab19b679b30
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203758"
---
## <a name="test-your-app"></a>Testar a aplicação

1. Execute o código para o emulador/dispositivo.
2. Tente iniciar sessão com uma conta do Azure Active Directory (conta escolar ou profissional) ou uma conta Microsoft (live.com, outlook.com). 

    ![Testar a sua aplicação](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Introduza o nome de utilizador e palavra-passe](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Consentimento para a sua aplicação

Na primeira vez que um utilizador inicia sessão sua aplicação, será solicitado a eles consentimento às permissões necessidades da sua aplicação, como mostrado aqui: 

![Forneça o seu consentimento para acesso à aplicação](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>Êxito!

Depois de iniciar sessão e dar consentimento, a aplicação irá apresentar a resposta do Microsoft Graph API. Esta chamada específica é para o **/me** ponto final e devolve o [perfil de utilizador](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Para obter uma lista de outros pontos de extremidade do Microsoft Graph, consulte [documentação de programador do Microsoft Graph API](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Âmbitos e permissões delegadas

O Microsoft Graph API requer a *User.Read* âmbito para ler um perfil de utilizador. Este âmbito é automaticamente em cada aplicação que está registrada no Portal de registo de aplicação. Outras APIs exigirá âmbitos adicionais. Por exemplo, a Microsoft Graph API requer a *Calendars.Read* âmbito para listar calendários do utilizador.

Para aceder aos calendários do utilizador, adicione a *Calendars.Read* delegado permissão para as informações de registo de aplicação. Em seguida, adicione a *Calendars.Read* definir o âmbito para o `acquireTokenSilent` chamar. 

> [!NOTE]
> Os utilizadores poderão ser solicitados consentimento adicionais se alterar o registo de aplicação.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
