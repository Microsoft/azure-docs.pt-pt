---
title: Ligue para uma API web a partir de uma app daemon - plataforma de identidade Microsoft / Azure
description: Saiba como construir uma app daemon que chama APIs web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76775199"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>App Daemon que chama APIs web - ligue para uma API web da app

As aplicações da ememon .NET podem chamar uma API web. As aplicações da ememon .NET também podem ligar para várias APIs web pré-aprovadas.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Chamando uma API web de uma aplicação daemon

Aqui está como usar o símbolo para chamar uma API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Pitão](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Chamando várias APIs

Para aplicações daemon, as APIs web a que chama precisam de ser pré-aprovadas. Não há consentimento incremental com aplicações daemon. (Não há interação do utilizador.) O administrador do inquilino precisa de dar o seu consentimento antecipadamente para o pedido e todas as permissões da API. Se quiser ligar para várias APIs, tem de adquirir um `AcquireTokenForClient`símbolo para cada recurso, cada vez que ligar . A MSAL utilizará a cache de ficha de aplicação para evitar chamadas de serviço desnecessárias.

## <a name="next-steps"></a>Passos seguintes

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App Daemon - mude para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Pitão](#tab/python)

> [!div class="nextstepaction"]
> [App Daemon - mude para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App Daemon - mude para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
