---
title: Configurar aplicação de inscrição única
description: Como configurar um único sign-on para uma aplicação personalizada que está a desenvolver e a registar-se com a Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 9b6451d29a3a874e09d74cbe664662a395b647f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82890470"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar um único sinal para uma aplicação

Ativar o único sign-on federado (SSO) na sua aplicação é automaticamente ativado quando federa através do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os utilizadores finais estiverem a ter de fazer sessão apesar de já terem uma sessão existente com o AZure AD, é provável que a sua aplicação possa estar mal configurada.

* Se estiver a utilizar a ADAL/MSAL, certifique-se de que tem **o Pedido de Comportamento** de Alerta para **Automóvel** e não **para Sempre**.

* Se estiver a construir uma aplicação móvel, poderá necessitar de configurações adicionais para ativar SSO intermediado ou não intermediado.

Para Android, consulte [Enableing Cross App SSO no Android.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

Para iOS, consulte [Enableing Cross App SSO no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Próximos passos

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Ativar o SSO da App Cross no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Ativar o SSO da Aplicação Cross no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integração de Apps para AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Permissões e consentimento no ponto final da plataforma de identidades da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Fluxo de stack AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)
