---
title: Como configurar uma nova aplicação multi-inquilino | Documentos da Microsoft
description: Como configurar o início de sessão único para um aplicativo personalizado estiver a desenvolver e registar com o Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 2aa3efcdec57a3e236d2b9739636656b35d16cd6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724152"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Como configurar uma nova aplicação de multi-inquilino

Ativar único início de sessão Federado (SSO) na sua aplicação é ativado automaticamente quando o Federação através do Azure AD para o OpenID Connect, SAML 2.0 ou WS-Fed. Se os utilizadores finais são ter de iniciar sessão, apesar de já terem uma sessão existente com o Azure AD, é provável que a aplicação pode estar configurado incorretamente.

* Se estiver a utilizar a MSAL/ADAL, certifique-se de que tem **PromptBehavior** definida como **automática** vez **sempre**.

* Se estiver a criar uma aplicação móvel, poderá ter configurações adicionais para ativar o SSO mediada ou não mediada.

Para Android, consulte [ativar o SSO de aplicações em várias no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Para iOS, veja [ativar cruzada SSO de aplicações no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Passos Seguintes

[SSO do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Ativar cruzada aplicação SSO no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Ativar cruzada SSO de aplicações no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integração de aplicações para o AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Aplicações de convergência de consentimento e concessão de permissões para o AzureAD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Stack Overflow do AzureAD](http://stackoverflow.com/questions/tagged/azure-active-directory)
