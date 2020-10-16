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
ms.openlocfilehash: 465adbb71abaa45160399ecba2ebfb692a8307c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88120682"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Como configurar um único sinal para uma aplicação

Ativar o único sign-on federado (SSO) na sua aplicação é automaticamente ativado quando federa através do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os utilizadores finais estiverem a ter de fazer sessão apesar de já terem uma sessão existente com o AZure AD, é provável que a sua aplicação possa estar mal configurada.

* Se estiver a utilizar a ADAL/MSAL, certifique-se de que tem **o Pedido de Comportamento** de Alerta para **Automóvel** e não **para Sempre**.

* Se estiver a construir uma aplicação móvel, poderá necessitar de configurações adicionais para ativar SSO intermediado ou não intermediado.

Para Android, consulte [Enableing Cross App SSO no Android.](../azuread-dev/howto-v1-enable-sso-android.md)<br>

Para iOS, consulte [Enableing Cross App SSO no iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Passos seguintes

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[Ativar o SSO da App Cross no Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Ativar o SSO da Aplicação Cross no iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integração de Apps para AzureAD](./quickstart-register-app.md)<br>

[Permissões e consentimento no ponto final da plataforma de identidades da Microsoft](./v2-permissions-and-consent.md)<br>

[Fluxo de stack AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)