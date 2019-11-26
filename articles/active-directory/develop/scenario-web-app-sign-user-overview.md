---
title: Web app that signs in users (overview) - Microsoft identity platform
description: Learn how to build a web app that signs in users (overview)
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
ms.openlocfilehash: 316ab055a077b251e88421ab26997f8556a6e31f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482482"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: Web app that signs in users

Learn all you need to build a web app that uses the Microsoft identity platform to sign in users.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

If you want to create your first portable (ASP.NET Core) web app that signs in users, follow this quickstart:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET Core web app that signs in users](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

If you want to understand how to add sign-in to an existing ASP.NET web application, try the following quickstart:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET web app that signs in users](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

If you're a Java developer, try the following quickstart:

> [!div class="nextstepaction"]
> [Quickstart: Add sign-in with Microsoft to a Java web app](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

If you develop with Python, try the following quickstart:

> [!div class="nextstepaction"]
> [Quickstart: Add sign-in with Microsoft to a Python web app](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Visão geral

You add authentication to your web app so that it can sign in users. Adding authentication enables your web app to access limited profile information in order to customize the experience for users. 

Web apps authenticate a user in a web browser. In this scenario, the web app directs the user's browser to sign them in to Azure Active Directory (Azure AD). Azure AD returns a sign-in response through the user's browser, which contains claims about the user in a security token. Signing in users takes advantage of the [Open ID Connect](./v2-protocols-oidc.md) standard protocol, simplified by the use of middleware [libraries](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps).

![Web app signs in users](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

As a second phase, you can enable your application to call web APIs on behalf of the signed-in user. This next phase is a different scenario, which you'll find in [Web app that calls web APIs](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Adding sign-in to a web app is about protecting the web app and validating a user token, which is what  **middleware** libraries do. In the case of .NET, this scenario does not yet require the Microsoft Authentication Library (MSAL), which is about acquiring a token to call protected APIs. Authentication libraries will be introduced in the follow-up scenario, when the web app needs to call web APIs.

## <a name="specifics"></a>Specifics

- During the application registration, you'll need to provide one or several (if you deploy your app to several locations) reply URIs. In some cases (ASP.NET and ASP.NET Core), you'll need to enable the ID token. Finally, you'll want to set up a sign-out URI so that your application reacts to users signing out.
- In the code for your application, you'll need to provide the authority to which your web app delegates sign-in. You might want to customize token validation (in particular, in partner scenarios).
- Web applications support any account types. For more information, see [Supported account types](v2-supported-account-types.md).

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
