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
ms.openlocfilehash: 5a234e0b5d3a88b722257aa80249f4d80182d12f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702237"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>App Daemon que chama APIs web - ligue para uma API web da app

Uma aplicação daemon pode ligar para uma API web a partir de uma aplicação .NET daemon ou ligar para várias APIs web pré-aprovadas.

## <a name="calling-a-web-api-daemon-application"></a>Chamando uma aplicação web Daemon Daemon

Aqui está como usar o símbolo para chamar uma API:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

Para aplicações daemon, as APIs web a que chama precisam de ser pré-aprovadas. Não haverá qualquer consentimento incremental com aplicações daemon (não há interação do utilizador). O administrador do inquilino precisa de pré-consentimento do pedido e de todas as permissões da API. Se quiser ligar para várias APIs, terá de adquirir um símbolo para cada recurso, ligando cada vez `AcquireTokenForClient`. A MSAL utilizará a cache de ficha de aplicação para evitar chamadas de serviço desnecessárias.

## <a name="next-steps"></a>Passos seguintes

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [App Daemon - mude para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [App Daemon - mude para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [App Daemon - mude para a produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
