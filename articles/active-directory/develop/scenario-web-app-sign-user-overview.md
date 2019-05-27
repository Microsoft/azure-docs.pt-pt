---
title: Aplicação Web que inicia sessão dos utilizadores (descrição geral) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que inicia sessão dos utilizadores (descrição geral)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833087"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: Aplicação web que inicia a sessão de utilizadores

Saiba tudo o que precisa para criar uma aplicação web que utilizadores inicia sessão com a plataforma de identidade da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Se pretende criar seu primeiro portáteis (ASP.NET Core) web as aplicações que iniciam sessão dos utilizadores, siga este guia de introdução:

> [!div class="nextstepaction"]
> [Quickstart: Aplicação de web de ASP.NET Core, que os utilizadores inicia sessão](quickstart-v2-aspnet-core-webapp.md)

Se preferir manter-se com o ASP.NET, experimente o tutorial seguinte:

> [!div class="nextstepaction"]
> [Quickstart: Aplicação de web de ASP.NET, que os utilizadores inicia sessão](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Descrição geral

Adicionar autenticação à sua aplicação web, para que ele pode iniciar sessão dos utilizadores. Adicionar autenticação permite à sua aplicação web aceder às informações de perfil limitada e, por exemplo, personalizar a experiência de que oferecer a seus usuários. Aplicações Web autenticar um usuário num navegador da web. Neste cenário, o aplicativo web direciona o navegador do usuário para iniciar sessão-las Azure AD. O Azure AD devolve uma resposta de início de sessão por meio de navegador do usuário, que contém as afirmações sobre o utilizador num token de segurança. Os utilizadores de início de sessão nas tirar partido da [abrir ID Connect](./v2-protocols-oidc.md) protocolo padrão em si simplificada pelo uso de middleware [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Utilizadores de sinais da aplicação de Web](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como uma segunda fase pode também ativar a sua aplicação chamar as APIs da Web em nome do utilizador com sessão iniciada. Esta fase seguinte é um cenário diferente, o que encontrará no [aplicação Web chama as APIs da Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Adicionar início de sessão a uma web aplicação é sobre como proteger a aplicação web e a validar um token de usuário, que é o que **middleware** fazer de bibliotecas. Este cenário não requer ainda que a Microsoft Authentication bibliotecas (MSAL), que está prestes a adquirir um token para chamar APIs de protegidos. As bibliotecas de autenticação só serão introduzidas no cenário de seguimento quando a aplicação web precisa chamar web APIs.

## <a name="specifics"></a>Informações específicas

- Durante o registo de aplicação, terá de fornecer uma ou várias (se implementar a sua aplicação para vários locais) responder URIs. Em alguns casos (ASP.NET/ASP.NET núcleos), terá de ativar o IDToken. Por fim desejará configurar um URI de fim de sessão para que seu aplicativo reage aos utilizadores de assinatura-out.
- No código para a sua aplicação, terá de fornecer a autoridade para que web de aplicação delegados início de sessão. Pode querer personalizar a validação do token (em especial em cenários de ISV).
- Aplicativos Web suportam todos os tipos de conta. Para mais informações, veja [tipos de conta suportados](v2-supported-account-types.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-app-sign-user-app-registration.md)
