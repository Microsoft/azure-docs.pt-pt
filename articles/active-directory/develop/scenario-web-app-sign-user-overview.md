---
title: Aplicativo Web que conecta usuários (visão geral)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que faz logon de usuários (visão geral)
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b898a93b87811fa5139e148a3273d7051af851b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056279"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: Aplicação web que inicia a sessão de utilizadores

Saiba tudo o que você precisa para criar um aplicativo Web que faz logon de usuários com a plataforma de identidade da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Se você quiser criar seus primeiros aplicativos Web (ASP.NET Core) portáteis que se conectam aos usuários, siga este guia de início rápido:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET Core aplicativo Web que faz logon de usuários](quickstart-v2-aspnet-core-webapp.md)

Se você preferir permanecer com ASP.NET, experimente o tutorial a seguir:

> [!div class="nextstepaction"]
> [Quickstart: Aplicativo Web ASP.NET que faz logon de usuários](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Descrição geral

Você adiciona autenticação ao seu aplicativo Web, para que ele possa conectar usuários. A adição de autenticação permite que seu aplicativo Web acesse informações de perfil limitadas e, por exemplo, personalize a experiência que você oferece a seus usuários. Os aplicativos Web autenticam um usuário em um navegador da Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para conectá-lo ao Azure AD. O Azure AD retorna uma resposta de entrada por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Os usuários de entrada aproveitam o protocolo padrão de [Open ID Connect](./v2-protocols-oidc.md) simplificado pelo uso de [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)de middleware.

![Usuários de entrada do aplicativo Web](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como uma segunda fase, você também pode habilitar seu aplicativo para chamar APIs da Web em nome do usuário conectado. Esta próxima fase é um cenário diferente, que você encontrará no [aplicativo Web chama APIs da Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> A adição de entrada a um aplicativo Web é sobre a proteção do aplicativo Web e a validação de um token de usuário, que é o que as bibliotecas de **middleware** fazem. Esse cenário não requer ainda as MSAL (bibliotecas de autenticação da Microsoft), que tratam de adquirir um token para chamar APIs protegidas. As bibliotecas de autenticação só serão introduzidas no cenário de acompanhamento quando o aplicativo Web precisar chamar APIs da Web.

## <a name="specifics"></a>Especificações

- Durante o registro do aplicativo, você precisará fornecer um ou vários (se você implantar seu aplicativo em vários locais) URIs de resposta. Em alguns casos (ASP.NET/ASP.NET Core), você precisará habilitar o token de ID. Por fim, você desejará configurar um URI de saída para que seu aplicativo reaja aos usuários que se desconectam.
- No código do seu aplicativo, você precisará fornecer a autoridade à qual seu aplicativo Web delega a entrada. Talvez você queira personalizar a validação de token (em particular em cenários de ISV).
- Os aplicativos Web oferecem suporte a qualquer tipo de conta. Para obter mais informações, consulte [tipos de conta com suporte](v2-supported-account-types.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-app-sign-user-app-registration.md)
