---
title: Inscreva-se nos utilizadores a partir de uma aplicação Web | Rio Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: a7e33f950bc5f13372962694abc8e3e40d8ad5c0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753238"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Cenário: aplicação web que assina nos utilizadores

Saiba tudo o que precisa para construir uma aplicação web que utilize a plataforma de identidade da Microsoft para iniciar sismo nos utilizadores.

## <a name="getting-started"></a>Introdução

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Se pretender criar a sua primeira aplicação web portátil (ASP.NET Core) que assina nos utilizadores, siga este arranque rápido:

[Quickstart: ASP.NET web app core que assina nos utilizadores](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Se quiser entender como adicionar o início de sing a uma aplicação web ASP.NET existente, experimente o seguinte quickstart:

[Quickstart: ASP.NET aplicação web que assina nos utilizadores](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Se você é um desenvolvedor java, experimente o seguinte quickstart:

[Quickstart: Adicione o início de sôs com a Microsoft a uma aplicação web java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Se desenvolver com Python, experimente o seguinte quickstart:

[Quickstart: Adicione o início de sôs com a Microsoft a uma aplicação web Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Descrição geral

Adiciona autenticação à sua aplicação web para que possa iniciar sação nos utilizadores. A adição de autenticação permite à sua aplicação web aceder a informações de perfil limitadas de forma a personalizar a experiência para os utilizadores.

As aplicações web autenticam um utilizador num navegador web. Neste cenário, a aplicação web direciona o navegador do utilizador para os inscrever no Azure Ative Directory (Azure AD). O Azure AD devolve uma resposta de súmis através do browser do utilizador, que contém reclamações sobre o utilizador num sinal de segurança. A assinatura nos utilizadores tira partido do protocolo [padrão Open ID Connect,](./v2-protocols-oidc.md) simplificado pela utilização de [bibliotecas](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)de middleware .

![Sinais de aplicações web nos utilizadores](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Como segunda fase, pode ativar a sua aplicação para ligar para APIs web em nome do utilizador inscrito. Esta próxima fase é um cenário diferente, que encontrará na [aplicação Web que chama APIs web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Adicionar o s-in a uma aplicação web é proteger a aplicação web e validar um token do utilizador, que é o que as bibliotecas  **de middleware** fazem. No caso de .NET, este cenário ainda não requer a Microsoft Authentication Library (MSAL), que se trata de adquirir um token para chamar APIs protegidas. As bibliotecas de autenticação serão introduzidas no cenário de seguimento, quando a aplicação web necessitar de chamar APIs web.

## <a name="specifics"></a>Especificidades

- Durante o registo da aplicação, terá de fornecer um ou vários (se implementar a sua aplicação em vários locais) responder a URIs. Em alguns casos (ASP.NET e ASP.NET Core), terá de ativar o token de ID. Por fim, irá querer configurar um URI de inscrição para que a sua aplicação reaja à assinatura dos utilizadores.
- No código da sua aplicação, terá de fornecer a autoridade à qual os delegados da sua aplicação web se inscrevem. É possível que queira personalizar a validação de símbolos (em particular, em cenários de parceiros).
- As aplicações web suportam quaisquer tipos de conta. Para obter mais informações, consulte [os tipos de conta suportados.](v2-supported-account-types.md)

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximos passos

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Passe para o próximo artigo neste cenário, [registo de aplicações.](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Passe para o próximo artigo neste cenário, [registo de aplicações.](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

Passe para o próximo artigo neste cenário, [registo de aplicações.](./scenario-web-app-sign-user-app-registration.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

Passe para o próximo artigo neste cenário, [registo de aplicações.](./scenario-web-app-sign-user-app-registration.md?tabs=python)

---