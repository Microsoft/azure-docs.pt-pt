---
title: Conectar usuários de um aplicativo Web-plataforma de identidade da Microsoft | Azure
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a53f88a97cdba8626c9d6c421093e4ec327e19
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961612"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: aplicativo Web que assina usuários

Saiba tudo o que você precisa para criar um aplicativo Web que usa a plataforma de identidade da Microsoft para conectar usuários.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Se você quiser criar seu primeiro aplicativo Web portátil (ASP.NET Core) que conecte os usuários, siga este guia de início rápido:

> [!div class="nextstepaction"]
> [Início rápido: ASP.NET Core aplicativo Web que assina usuários](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Se você quiser entender como adicionar a entrada a um aplicativo Web ASP.NET existente, tente o início rápido a seguir:

> [!div class="nextstepaction"]
> [Início rápido: aplicativo Web ASP.NET que assina usuários](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Se você for um desenvolvedor de Java, tente o início rápido a seguir:

> [!div class="nextstepaction"]
> [Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web Java](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Se você desenvolver com Python, experimente o início rápido a seguir:

> [!div class="nextstepaction"]
> [Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Visão geral

Você adiciona autenticação ao seu aplicativo Web para que ele possa conectar usuários. A adição de autenticação permite que seu aplicativo Web acesse informações de perfil limitadas para personalizar a experiência para os usuários. 

Os aplicativos Web autenticam um usuário em um navegador da Web. Nesse cenário, o aplicativo Web direciona o navegador do usuário para conectá-lo ao Azure Active Directory (Azure AD). O Azure AD retorna uma resposta de entrada por meio do navegador do usuário, que contém declarações sobre o usuário em um token de segurança. A entrada de usuários aproveita o protocolo padrão do [Open ID Connect](./v2-protocols-oidc.md) , simplificado pelo uso de [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)de middleware.

![Usuários do aplicativo Web](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como uma segunda fase, você pode habilitar seu aplicativo para chamar APIs da Web em nome do usuário conectado. Esta próxima fase é um cenário diferente, que você encontrará no [aplicativo Web que chama APIs da Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> A adição de entrada a um aplicativo Web é sobre a proteção do aplicativo Web e a validação de um token de usuário, que é o que as bibliotecas de **middleware** fazem. No caso do .NET, esse cenário ainda não requer a MSAL (biblioteca de autenticação da Microsoft), que é sobre a aquisição de um token para chamar APIs protegidas. As bibliotecas de autenticação serão introduzidas no cenário de acompanhamento, quando o aplicativo Web precisar chamar APIs da Web.

## <a name="specifics"></a>Especificações

- Durante o registro do aplicativo, você precisará fornecer um ou vários URIs de resposta (se você implantar seu aplicativo em vários locais). Em alguns casos (ASP.NET e ASP.NET Core), você precisará habilitar o token de ID. Por fim, você desejará configurar um URI de saída para que seu aplicativo reaja aos usuários saindo.
- No código do seu aplicativo, você precisará fornecer a autoridade para a qual seu aplicativo Web delega a entrada. Talvez você queira personalizar a validação de token (em particular, em cenários de parceiros).
- Os aplicativos Web oferecem suporte a qualquer tipo de conta. Para obter mais informações, consulte [tipos de conta com suporte](v2-supported-account-types.md).

## <a name="next-steps"></a>Passos seguintes

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
