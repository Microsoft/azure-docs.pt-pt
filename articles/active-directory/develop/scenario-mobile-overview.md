---
title: Construa uma aplicação móvel que chama APIs web Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chama APIs web (visão geral)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0a33b5bb4fc7220a9cf66f40e9805d3fde9fccef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702050"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Cenário: Aplicação móvel que chama APIs web

Aprenda a construir uma aplicação móvel que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Crie a sua primeira aplicação móvel e experimente um arranque rápido.

> [!div class="nextstepaction"]
> [Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API a partir de uma aplicação Android](./quickstart-v2-android.md)
>
> [Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API de uma aplicação iOS](./quickstart-v2-ios.md)
>
> [Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API a partir de uma aplicação Xamarin iOS & Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Visão geral

Uma experiência personalizada e perfeita do utilizador é essencial para aplicações móveis.  A plataforma de identidade da Microsoft permite que os desenvolvedores móveis criem essa experiência para utilizadores de iOS e Android. A sua aplicação pode inscrever utilizadores do Azure Ative Directory (Azure AD), utilizadores pessoais da conta da Microsoft e utilizadores de Azure AD B2C e adquirir fichas para ligar para uma API web em seu nome. Para implementar estes fluxos, usaremos a Microsoft Authentication Library (MSAL), que implementa o fluxo de código de autorização padrão da indústria [OAuth2.0](v2-oauth2-auth-code-flow.md).

![Aplicações daemon](./media/scenarios/mobile-app.svg)

Considerações para aplicações móveis:

- **A experiência**do utilizador é fundamental : Permita que os utilizadores vejam o valor da sua aplicação antes de pedir em prever e solicite apenas as permissões necessárias.
- **Suporte todas as configurações do utilizador**: Muitos utilizadores de empresas móveis estão sob as políticas de Acesso Condicional e conformidade do dispositivo. Certifique-se de apoiar estes cenários-chave.
- **Implementar um único sinal (SSO)** : A plataforma de identidade MSAL e Microsoft permite um único sinal de acesso simples através do navegador do dispositivo ou do Autenticador Microsoft (e do Portal da Empresa Intune no Android).

## <a name="specifics"></a>Especificações

Tenha em mente estas considerações quando construir uma aplicação móvel na plataforma de identidade da Microsoft:

- Dependendo da plataforma, poderá ser necessária alguma interação do utilizador na primeira vez que os utilizadores se inscrevam. Por exemplo, o iOS requer aplicações para mostrar a interação do utilizador ao utilizar o SSO pela primeira vez através do Microsoft Authenticator (e intune Company Portal on Android).
- No iOS e Android, o MSAL poderá utilizar um navegador externo (que pode aparecer em cima da sua aplicação) para iniciar sessão nos utilizadores. Em vez disso, pode personalizar a configuração para utilizar webViews na aplicação.
- Nunca use um segredo numa aplicação móvel. Será acessível a todos os utilizadores.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-mobile-app-registration.md)
