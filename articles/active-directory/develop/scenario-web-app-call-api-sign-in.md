---
title: Remova as contas da cache simbólica no | de assinatura Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como remover uma conta da cache simbólica na sinse-out
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9fc271dfa9edbedac8527009dd2b2180b7c5e7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756257"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Uma aplicação web que chama APIs web: Remova as contas da cache simbólica no sign-out global

Aprendeu a adicionar o s-in à sua aplicação web na [aplicação Web que assina nos utilizadores: Iniciar sôm e iniciar s-out](scenario-web-app-sign-user-sign-in.md).

O sign-out é diferente para uma aplicação web que chama apis web. Quando o utilizador se despede da sua aplicação, ou de qualquer aplicação, deve remover os tokens associados a esse utilizador da cache simbólica.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercete a chamada após uma única sdecisão

Para limpar a entrada em cache simbólico associada à conta que assinou, a sua aplicação pode intercetar o `logout` pós-evento. As aplicações web armazenam fichas de acesso para cada utilizador numa cache simbólica. Ao intercetar o retorno após a `logout` chamada, a sua aplicação web pode remover o utilizador da cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web cuida de implementar o sismo para si. Para mais detalhes consulte [microsoft.identity.web source code](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A amostra ASP.NET não remove as contas da cache na assinatura global.

# <a name="java"></a>[Java](#tab/java)

A amostra de Java não remove as contas da cache na assinatura global.

# <a name="python"></a>[Python](#tab/python)

A amostra python não remove as contas da cache na assinatura global.

---

## <a name="next-steps"></a>Passos seguintes

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a aplicação web.](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a aplicação web.](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a aplicação web.](./scenario-web-app-call-api-acquire-token.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

Passe para o próximo artigo neste cenário, [adquira um símbolo para a aplicação web.](./scenario-web-app-call-api-acquire-token.md?tabs=python)

---