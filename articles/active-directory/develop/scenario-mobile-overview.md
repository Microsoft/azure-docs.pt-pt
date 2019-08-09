---
title: Aplicativo móvel que chama APIs Web-visão geral | Plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chama APIs da Web (visão geral)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67f063ab374dec8bac3905ea46f1dfa9d8ed1fb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852594"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Cenário: Aplicativo móvel que chama APIs da Web

Saiba tudo o que você precisa saber para criar um aplicativo móvel que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Crie seu primeiro aplicativo móvel e experimente um início rápido!

> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar Microsoft Graph API de um aplicativo Android](./quickstart-v2-android.md)
>
> [Quickstart: Adquirir um token e chamar Microsoft Graph API de um aplicativo iOS](./quickstart-v2-ios.md)
>
> [Quickstart: Adquirir um token e chamar Microsoft Graph API de um aplicativo Xamarin iOS & Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Descrição geral

Uma experiência de usuário personalizada e direta é essencial para aplicativos móveis.  A plataforma de identidade da Microsoft permite que os desenvolvedores móveis criem essa experiência para usuários de iOS e Android. Seu aplicativo pode entrar Azure Active Directory (Azure AD) usuários, usuários pessoais conta Microsoft e Azure AD B2C usuários e adquirir tokens para chamar uma API da Web em seu nome. Para implementar esses fluxos, usaremos a MSAL (biblioteca de autenticação da Microsoft), que implementa o fluxo de código de autorização padrão do setor [OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplicações daemon](./media/scenarios/mobile-app.svg)

Considerações para aplicativos móveis:

- A **experiência do usuário é a chave**: Permita que os usuários vejam o valor do seu aplicativo antes de solicitar a entrada e solicite apenas as permissões necessárias.
- **Suporte a todas as configurações de usuário**: Muitos usuários de negócios móveis estão sob políticas de acesso condicional e de conformidade do dispositivo. Certifique-se de dar suporte a esses cenários principais.
- **Implementar logon único (SSO)** : O MSAL e a plataforma de identidade da Microsoft facilitam a habilitação do logon único por meio do navegador do dispositivo ou do Microsoft Authenticator (e Portal da Empresa do Intune no Android).

## <a name="specifics"></a>Especificações

Lembre-se destas considerações ao criar um aplicativo móvel na plataforma Microsoft Identity:

- Dependendo da plataforma, é possível que alguma interação do usuário seja necessária na primeira vez que os usuários entrarem. Por exemplo, o iOS requer que os aplicativos mostrem a interação do usuário ao usar o SSO na primeira vez que Microsoft Authenticator (e Portal da Empresa do Intune no Android).
- No iOS e no Android, o MSAL pode usar um navegador externo (que pode aparecer na parte superior do seu aplicativo) para conectar usuários. Em vez disso, você pode personalizar a configuração para usar webviews no aplicativo.
- Nunca use um segredo em um aplicativo móvel. Ele poderá ser acessado por todos os usuários.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-mobile-app-registration.md)
