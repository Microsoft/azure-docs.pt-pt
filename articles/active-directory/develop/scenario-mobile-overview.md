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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076499"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Cenário: Aplicações móveis, chamadas de APIs web

Saiba tudo o que precisa para criar uma aplicação móvel que chama a web APIs.

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

Ao criar uma aplicação móvel, uma experiência de utilizador final de personalizadas e totalmente integrada é essencial.  Plataforma de identidade da Microsoft permite aos desenvolvedores móveis a fazer exatamente isso para iOS e os utilizadores do Android. Seu aplicativo pode iniciar sessão no Azure AD, a conta Microsoft pessoal e os utilizadores do Azure AD B2C e adquirir tokens para chamar uma API web em nome deles. Para implementar estes fluxos, usaremos o Microsoft Authentication Library (MSAL) que implementa o padrão da indústria [fluxo de código de autorização de OAuth2.0](v2-oauth2-auth-code-flow.md).

![Aplicações daemon](./media/scenarios/mobile-app.svg)

Considerações de aplicação móvel:

- ***Experiência do usuário é a chave***: Permitir que os utilizadores ver o valor da sua aplicação antes de fazer para início de sessão e apenas solicitar as permissões necessárias.
- ***Suporte a todas as configurações de usuário***: Muitos usuários móveis empresariais estão sob o acesso condicional e políticas de conformidade do dispositivo. Certifique-se de que suportam esses cenários-chave.
- ***Implementar o início de sessão único (SSO)***: A MSAL e plataforma de identidade do Microsoft simplificam a ativar o início de sessão único através do browser do dispositivo ou o Microsoft Authenticator (e Portal da empresa do Intune no Android).

## <a name="specifics"></a>Informações específicas

Ao criar uma aplicação móvel na plataforma de identidade da Microsoft, a experiência de ponta a ponta tem algumas considerações:

- Consoante a plataforma, iniciar sessão sem qualquer interação pode não ser possível no primeiro início de sessão. iOS, por exemplo, requer que as aplicações para mostrar a interação do usuário ao obter o tempo SSO a primeira através do Microsoft Authenticator (e o Portal da empresa do Intune no Android).
- No iOS e Android, MSAL pode utilizar um navegadores externos (que podem aparecer com base no seu aplicativo) para iniciar sessão dos utilizadores. Isso pode ser personalizado para utilizar as vistas de na aplicação Web em vez disso.
- Nunca usar um segredo num aplicativo móvel, é acessível a todos os utilizadores.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-mobile-app-registration.md)
