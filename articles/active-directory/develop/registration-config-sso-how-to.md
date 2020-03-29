---
title: Configure inscrição única de aplicação / Microsoft Docs
description: Como configurar um único início de inscrição para uma aplicação personalizada que está a desenvolver e a registar com a Azure AD.
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
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702646"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar um único início de inscrição para uma aplicação

Ativar um único sign-on federado (SSO) na sua aplicação é automaticamente ativado quando federado através do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os utilizadores finais tiverem de iniciar sessão apesar de já terem uma sessão existente com o Azure AD, é provável que a sua aplicação possa estar mal configurada.

* Se estiver a utilizar a ADAL/MSAL, certifique-se de que tem **o PromptBehavior** definido para **Auto** em vez de **Sempre**.

* Se estiver a construir uma aplicação móvel, poderá necessitar de configurações adicionais para ativar o SSO intermediado ou não intermediado.

Para Android, consulte [Enableing Cross App SSO no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Para iOS, consulte [Enableing Cross App SSO no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Passos seguintes

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Habilitar sSO de aplicativo so em Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Habilitar a App Transversal SSO no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integração de Apps para O AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentimento e Permissão para Aplicações Convergentes AzureAD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Empilhador AzureAdOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
