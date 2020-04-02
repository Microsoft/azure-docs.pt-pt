---
title: Construa uma aplicação móvel que chama APIs web Azure
titleSuffix: Microsoft identity platform | Azure
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
ms.openlocfilehash: acd44298e401aabaef03f5ddd84f37f32a3d8bcd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546098"
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
> [Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API a partir de uma aplicação Xamarin iOS e Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Descrição geral

Uma experiência personalizada e perfeita do utilizador é essencial para aplicações móveis.  A plataforma de identidade da Microsoft permite que os desenvolvedores móveis criem essa experiência para utilizadores de iOS e Android. A sua aplicação pode inscrever utilizadores do Azure Ative Directory (Azure AD), utilizadores pessoais da conta da Microsoft e utilizadores do Azure AD B2C. Também pode adquirir fichas para chamar uma API web em seu nome. Para implementar estes fluxos, usaremos a Microsoft Authentication Library (MSAL). A MSAL implementa o fluxo de código de [autorização OAuth2.0](v2-oauth2-auth-code-flow.md)padrão da indústria.

![Aplicações daemon](./media/scenarios/mobile-app.svg)

Considerações para aplicações móveis:

- **A experiência**do utilizador é fundamental : Permita que os utilizadores vejam o valor da sua aplicação antes de pedir o inserição. Solicite apenas as permissões necessárias.
- **Suporte todas as configurações do utilizador**: Muitos utilizadores de empresas móveis devem aderir a políticas de acesso condicional e políticas de conformidade com dispositivos. Certifique-se de apoiar estes cenários-chave.
- **Implementar um único sinal (SSO)**: Ao utilizar a plataforma de identidade MSAL e Microsoft, pode ativar um único sinal através do navegador do dispositivo ou do Microsoft Authenticator (e do Portal da Empresa Intune no Android).
- **Implementar o modo de dispositivo partilhado**: Permitir que a sua aplicação seja utilizada em cenários de dispositivos partilhados, por exemplo hospitais, fabrico, retalho e finanças. [Leia mais sobre o modo de suporte do dispositivo partilhado](msal-shared-devices.md).

## <a name="specifics"></a>Especificidades

Tenha em mente as seguintes considerações quando construir uma aplicação móvel na plataforma de identidade da Microsoft:

- Dependendo da plataforma, poderá ser necessária alguma interação do utilizador na primeira vez que os utilizadores iniciarem o seu início. Por exemplo, o iOS requer aplicações para mostrar a interação do utilizador quando utilizam o SSO pela primeira vez através do Microsoft Authenticator (e intune Company Portal on Android).
- No iOS e Android, o MSAL poderá utilizar um navegador externo para iniciar sessão nos utilizadores. O navegador externo pode aparecer em cima da sua aplicação.
- Nunca use um segredo numa aplicação móvel. Nestas aplicações, os segredos são acessíveis a todos os utilizadores.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-mobile-app-registration.md)
