---
title: Ligue para uma API web de uma aplicação da Daemon | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma app daemon que chama uma API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a94cbebffbe2c735515d9758a2353a9857676311
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756572"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon app que chama APIs web - ligue para uma API web a partir da app

.NET daemon apps pode chamar uma API web. .NET daemon apps também podem chamar várias APIs web pré-aprovadas.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Chamando uma API web de uma aplicação da daemon

Eis como usar o símbolo para chamar uma API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

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

Para aplicações daemon, as APIs web a que chama precisam de ser pré-aprovadas. Não há consentimento incremental com aplicações daemon. (Não há interação do utilizador.) O administrador inquilino precisa de dar consentimento antecipadamente para o pedido e todas as permissões da API. Se quiser ligar para várias APIs, tem de adquirir um símbolo para cada recurso, cada vez que `AcquireTokenForClient` ligue. A MSAL utilizará o cache de ficha de aplicação para evitar chamadas de serviço desnecessárias.

## <a name="next-steps"></a>Próximos passos

# <a name="net"></a>[.NET](#tab/dotnet)

Passe para o próximo artigo neste cenário, [passe para a produção](./scenario-daemon-production.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Passe para o próximo artigo neste cenário, [passe para a produção](./scenario-daemon-production.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Passe para o próximo artigo neste cenário, [passe para a produção](./scenario-daemon-production.md?tabs=java).

---
