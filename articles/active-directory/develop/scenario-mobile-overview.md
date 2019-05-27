---
title: Aplicação móvel que chama o web APIs - descrição geral | Plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação móvel que chamadas de web APIs (descrição geral)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5b53ccdcd33e2542834769baff5b759ff7b170
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962363"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Cenário: Aplicações móveis, chamadas de APIs web

Saiba tudo o que precisa saber para criar uma aplicação móvel que chama a web APIs.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Crie seu primeiro aplicativo móvel e experimente um início rápido!

> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação Android](./quickstart-v2-android.md)
>
> [Quickstart: Adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação iOS](./quickstart-v2-ios.md)
>
> [Quickstart: Adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação Android e o Xamarin iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Descrição geral

Uma experiência de utilizador personalizadas e totalmente integrada é essencial para as aplicações móveis.  Plataforma de identidade da Microsoft permite que os desenvolvedores móveis criar essa experiência para iOS e os utilizadores do Android. Seu aplicativo pode iniciar sessão de utilizadores do Azure Active Directory (Azure AD), os utilizadores da conta Microsoft pessoas e utilizadores do Azure AD B2C e adquirir tokens para chamar uma API web em nome deles. Para implementar estes fluxos, usaremos o Microsoft Authentication Library (MSAL), que implementa o padrão da indústria [fluxo de código de autorização de OAuth2.0](v2-oauth2-auth-code-flow.md).

![Aplicações daemon](./media/scenarios/mobile-app.svg)

Considerações para aplicações móveis:

- **Experiência do usuário é a chave**: Permitir que os utilizadores ver o valor da sua aplicação antes de fazer para início de sessão e solicitar apenas as permissões necessárias.
- **Suporte a todas as configurações de usuário**: Muitos usuários móveis empresariais estão sob o acesso condicional e políticas de conformidade do dispositivo. Certifique-se de que suportam esses cenários-chave.
- **Implementar o início de sessão único (SSO)**: A MSAL e plataforma de identidade do Microsoft simplificam a ativar o início de sessão único através do browser do dispositivo ou o Microsoft Authenticator (e Portal da empresa do Intune no Android).

## <a name="specifics"></a>Informações específicas

Tenha em mente ao criar uma aplicação móvel na plataforma de identidade da Microsoft:

- Consoante a plataforma, alguma interação do utilizador poderão ser necessária na primeira vez que os utilizadores iniciam sessão. Por exemplo, iOS requer que as aplicações para mostrar a interação do utilizador ao utilizar SSO na primeira vez por meio do Microsoft Authenticator (e o Portal da empresa do Intune no Android).
- No iOS e Android, MSAL poderá utilizar um browser externo (que pode aparecer com base no seu aplicativo) para iniciar sessão dos utilizadores. Pode personalizar a configuração para utilizar as vistas de na aplicação Web em vez disso.
- Nunca use um segredo num aplicativo móvel. Estará acessível a todos os utilizadores.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-mobile-app-registration.md)
