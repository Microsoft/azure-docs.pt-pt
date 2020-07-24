---
title: Ligue para uma api web a partir de uma aplicação web - Plataforma de identidade microsoft / Rio Azure
description: Saiba como construir uma aplicação web que chame APIs web (chamando uma API web protegida)
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
ms.openlocfilehash: 1e448f52f4e8c24dd8552cae873edac841e57fc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058434"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Uma aplicação web que chama APIs web: Ligue para uma API web

Agora que tem um símbolo, pode chamar uma API protegida da web.

## <a name="call-a-protected-web-api"></a>Chame uma API web protegida

Chamar uma API web protegida depende da sua linguagem e enquadramento de escolha:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está o código simplificado para a ação do `HomeController` . Este código recebe um símbolo para ligar para o Microsoft Graph. O código foi adicionado para mostrar como chamar o Microsoft Graph como uma API REST. O URL para a API do Gráfico da Microsoft é fornecido no appsettings.jsem ficheiro e é lido numa variável chamada `webOptions` :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```

> [!NOTE]
> Pode usar o mesmo princípio para chamar a API web.
>
> A maioria das APIs web Azure fornecem um SDK que simplifica a chamada API. Isto também é verdade no Microsoft Graph. No próximo artigo, você vai aprender onde encontrar um tutorial que ilustra o uso da API.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

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
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-web-app-call-api-production.md)
