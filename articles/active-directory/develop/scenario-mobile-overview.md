---
title: Construa uma aplicação móvel que ligue para APIs web | Rio Azure
titleSuffix: Microsoft identity platform | Azure
description: Saiba como construir uma aplicação móvel que chame APIs web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a678e8545f761e98abbdf026886afa624f9d2b4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753650"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Cenário: Aplicação móvel que chama APIs web

Saiba como construir uma aplicação móvel que chame APIs web.

## <a name="getting-started"></a>Introdução

Se ainda não o fez, crie a sua primeira aplicação completando um arranque rápido:

- [Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação Android](./quickstart-v2-android.md)
- [Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação para iOS](./quickstart-v2-ios.md)
- [Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação Xamarin iOS e Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (GitHub)

## <a name="overview"></a>Descrição geral

Uma experiência personalizada e sem emenda do utilizador é essencial para aplicações móveis. A plataforma de identidade da Microsoft permite que os desenvolvedores móveis criem essa experiência para utilizadores de iOS e Android. A sua aplicação pode iniciar súbs em utilizadores do Azure Ative Directory (Azure AD), utilizadores pessoais da conta microsoft e utilizadores Azure AD B2C. Também pode adquirir fichas para chamar uma API web em seu nome. Para implementar estes fluxos, usaremos a Biblioteca de Autenticação da Microsoft (MSAL). A MSAL implementa o fluxo de [código de autorização OAuth2.0 padrão da](v2-oauth2-auth-code-flow.md)indústria.

![Aplicações daemon](./media/scenarios/mobile-app.svg)

Considerações para aplicações móveis:

- **A experiência do utilizador é fundamental**: Permita que os utilizadores vejam o valor da sua aplicação antes de pedir o seu sôm. Solicite apenas as permissões necessárias.
- **Suportar todas as configurações do utilizador**: Muitos utilizadores de empresas móveis devem aderir a políticas de acesso condicional e políticas de conformidade com o dispositivo. Certifique-se de apoiar estes cenários-chave.
- **Implementar um único sign-on (SSO)**: Utilizando a plataforma de identidade MSAL e Microsoft, pode ativar um único sinal através do navegador do dispositivo ou do Microsoft Authenticator (e portal da Empresa Intune no Android).
- **Implementar o modo de dispositivo partilhado**: Permita que a sua aplicação seja usada em cenários de dispositivos partilhados, como hospitais, fabricação, retalho e finanças. [Leia mais sobre o modo de suporte ao dispositivo partilhado](msal-shared-devices.md).

## <a name="specifics"></a>Especificidades

Tenha em mente as seguintes considerações quando construir uma aplicação móvel na plataforma de identidade da Microsoft:

- Dependendo da plataforma, poderá ser necessária alguma interação com o utilizador na primeira vez que os utilizadores iniciarem sedudo. Por exemplo, o iOS requer que as aplicações mostrem a interação dos utilizadores quando utilizam o SSO pela primeira vez através do Microsoft Authenticator (e do Portal da Empresa Intune no Android).
- No iOS e Android, o MSAL pode utilizar um navegador externo para iniciar snus nos utilizadores. O navegador externo pode aparecer em cima da sua aplicação.
- Nunca use um segredo numa aplicação móvel. Nestas aplicações, os segredos são acessíveis a todos os utilizadores.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [registo de aplicações.](scenario-mobile-app-registration.md)
