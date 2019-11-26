---
title: Web app that signs in users (move to production) - Microsoft identity platform
description: Learn how to build a web app that signs in users (move to production)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efcc7ed4f80d0e3e9750d19ff95d010052a08b87
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481889"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Web app that signs in users: Move to production

Now that you know how to get a token to call web APIs, learn how to move it to production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passos seguintes

### <a name="scenario-for-calling-web-apis"></a>Scenario for calling web APIs

After your web app signs in users, it can call web APIs on behalf of the signed-in users. Calling web APIs from the web app is the object of the following scenario:

> [!div class="nextstepaction"]
> [Aplicação Web que chama as APIs Web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Deep dive: ASP.NET Core web app tutorial

Learn about other ways to sign in users with this ASP.NET Core tutorial: 

> [!div class="nextstepaction"]
> [Enable your web apps to sign in users and call APIs with the Microsoft identity platform for developers](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

This progressive tutorial has production-ready code for a web app, including how to add sign-in with accounts in:

- Your organization
- Multiple organizations
- Work or school accounts, or personal Microsoft accounts
- [Azure AD B2C](https://aka.ms/aadb2c)
- National clouds

### <a name="sample-code-java-web-app"></a>Sample code: Java web app

Learn more about the Java web app from this sample on GitHub: 

> [!div class="nextstepaction"]
> [A Java Web application that signs in users with the Microsoft identity platform and calls Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
