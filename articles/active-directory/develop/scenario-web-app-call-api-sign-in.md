---
title: Remova as contas do cache simbólico no sign-out - plataforma de identidade da Microsoft / Azure
description: Saiba como remover uma conta da cache simbólica no sign-out
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181652"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Uma aplicação web que chama APIs web: Remova as contas da cache simbólica no sign-out global

Aprendeu a adicionar sessão na sua aplicação web na [aplicação Web que assina nos utilizadores: Iniciar](scenario-web-app-sign-user-sign-in.md)sessão e iniciar sessão .

A inscrição é diferente para uma aplicação web que chama apis web. Quando o utilizador se signa da sua aplicação, ou de qualquer aplicação, deve remover as fichas associadas a esse utilizador da cache simbólica.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercete a chamada após a inscrição individual

Para limpar a entrada de cache de token associada à conta `logout` que assinou, a sua aplicação pode intercetar o evento seguinte. As aplicações web armazenam fichas de acesso para cada utilizador numa cache simbólica. Intercetando o `logout` backback posterior, a sua aplicação web pode remover o utilizador da cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web cuida de implementar o sign-out para si.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A amostra ASP.NET não remove contas da cache no sinal global.

# <a name="java"></a>[Java](#tab/java)

A amostra de Java não remove contas da cache na aprovação global.

# <a name="python"></a>[Python](#tab/python)

A amostra python não remove contas da cache na aprovação global.

---

## <a name="next-steps"></a>Passos seguintes

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
