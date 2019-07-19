---
title: Configurar logon único do aplicativo | Microsoft Docs
description: Como configurar o logon único para um aplicativo personalizado que você está desenvolvendo e registrando com o Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc9f20903d65fbde161d26ed3e194e63d341ccb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320961"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar o logon único para um aplicativo

Habilitar o SSO (logon único) federado em seu aplicativo é habilitado automaticamente ao federar por meio do Azure AD para OpenID Connect, SAML 2,0 ou WS-enalimentado. Se os usuários finais estiverem tendo de entrar apesar de já ter uma sessão existente com o Azure AD, é provável que seu aplicativo possa estar configurado incorretamente.

* Se você estiver usando o ADAL/MSAL, verifique se você tem o **PromptBehavior** definido como **automático** em vez de **sempre**.

* Se você estiver criando um aplicativo móvel, talvez precise de configurações adicionais para habilitar o SSO orientado ou não-orientado.

Para o Android, consulte [habilitando SSO entre aplicativos no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Para iOS, consulte [habilitando SSO entre aplicativos no Ios](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Passos Seguintes

[SSO do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Habilitando o SSO entre aplicativos no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Habilitando o SSO entre aplicativos no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrando aplicativos ao AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentimento e permissão para aplicativos convergidos do AzureAD v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
