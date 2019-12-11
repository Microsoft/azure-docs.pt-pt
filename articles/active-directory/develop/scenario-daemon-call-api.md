---
title: Chamar uma API da Web de um aplicativo daemon-plataforma Microsoft Identity | Azure
description: Saiba como criar um aplicativo daemon que chama APIs da Web
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f1676ab1bdcf2b23907824bb9bf543b5f28ce6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962615"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplicativo daemon que chama APIs Web – chama uma API Web do aplicativo

Um aplicativo de daemon pode chamar uma API da Web de um aplicativo de daemon do .NET ou chamar várias APIs Web previamente aprovadas.

## <a name="calling-a-web-api-daemon-application"></a>Chamando um aplicativo Web API daemon

Aqui está como usar o token para chamar uma API:

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

Para aplicativos daemon, as APIs da Web que você chama precisam ser aprovadas previamente. Não haverá nenhum consentimento incremental com aplicativos de daemon (não há interação do usuário). O administrador de locatários precisa consentir previamente o aplicativo e todas as permissões de API. Se você quiser chamar várias APIs, precisará adquirir um token para cada recurso, sempre chamando `AcquireTokenForClient`. MSAL usará o cache de token de aplicativo para evitar chamadas de serviço desnecessárias.

## <a name="next-steps"></a>Passos seguintes

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplicativo de daemon – mover para produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplicativo de daemon – mover para produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplicativo de daemon – mover para produção](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
