---
title: Inscreva-se nos utilizadores a partir de uma aplicação Web - plataforma de identidade microsoft / Azure
description: Saiba como construir uma aplicação web que assina nos utilizadores (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 65d254cec5735c54e19f5adfde57fb6aed776a2c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881490"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: Web app que assina nos utilizadores

Saiba tudo o que precisa para construir uma aplicação web que utilize a plataforma de identidade da Microsoft para assinar nos utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se quiser criar a sua primeira aplicação web portátil (ASP.NET Core) que assina nos utilizadores, siga este quickstart:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET aplicação web Core que assina nos utilizadores](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Se quiser entender como adicionar o início de sessão a uma aplicação web ASP.NET existente, tente o seguinte arranque rápido:

> [!div class="nextstepaction"]
> [Quickstart: ASP.NET aplicação web que assina nos utilizadores](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se é um desenvolvedor de Java, tente o seguinte arranque rápido:

> [!div class="nextstepaction"]
> [Quickstart: Adicione o sessão com a Microsoft a uma aplicação web java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Se desenvolver com python, tente o seguinte arranque rápido:

> [!div class="nextstepaction"]
> [Quickstart: Adicione o sessão com a Microsoft a uma aplicação web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Descrição geral

Adiciona autenticação à sua aplicação web para que possa inscrever os utilizadores. A adição de autenticação permite à sua aplicação web aceder a informações de perfil limitados de forma a personalizar a experiência para os utilizadores. 

As aplicações web autenticam um utilizador num navegador web. Neste cenário, a aplicação web direciona o navegador do utilizador para inscrevê-los no Azure Ative Directory (Azure AD). A Azure AD devolve uma resposta de entrada através do navegador do utilizador, que contém reclamações sobre o utilizador num token de segurança. A assinatura nos utilizadores tira partido do protocolo padrão [Open ID Connect,](./v2-protocols-oidc.md) simplificado pela utilização de [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)middleware .

![Sinais de aplicativo web nos utilizadores](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como segunda fase, pode ativar a sua aplicação para ligar para a Web APIs em nome do utilizador inscrito. Esta próxima fase é um cenário diferente, que encontrará na [aplicação Web que chama APIs web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Adicionar sessão a uma aplicação web é proteger a aplicação web e validar um token do utilizador, que é o que as bibliotecas **middleware** fazem. No caso de .NET, este cenário ainda não requer a Microsoft Authentication Library (MSAL), que tem a ver com a aquisição de um símbolo para chamar APIs protegidos. As bibliotecas de autenticação serão introduzidas no cenário de seguimento, quando a aplicação web necessitar de chamadas de APIs web.

## <a name="specifics"></a>Especificidades

- Durante o registo da aplicação, terá de fornecer um ou vários (se implementar a sua aplicação em vários locais) responder a URIs. Em alguns casos (ASP.NET e ASP.NET Core), terá de ativar o símbolo de identificação. Por fim, vai querer configurar um URI de inscrição para que a sua aplicação reaja aos utilizadores que se inscrevam.
- No código da sua aplicação, terá de fornecer a autoridade para a qual a sua aplicação web delega o seu acesso. É possível personalizar a validação de token (em particular, em cenários de parceiros).
- As aplicações web suportam quaisquer tipos de conta. Para mais informações, consulte os tipos de [conta suportados](v2-supported-account-types.md).

## <a name="next-steps"></a>Passos seguintes

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Registo da aplicação](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
