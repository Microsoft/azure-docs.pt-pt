---
title: Ligue para um api web de uma aplicação web - plataforma de identidade Microsoft / Azure
description: Saiba como construir uma aplicação web que chama APIs web (chamando uma API web protegida)
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
ms.openlocfilehash: 84df33137566445015848655cfecb87ba67ef123
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181686"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Uma aplicação web que chama APIs web: Ligue para uma Web API

Agora que tem um símbolo, pode chamar uma API protegida.

## <a name="call-a-protected-web-api"></a>Ligue para uma API web protegida

Chamar uma API web protegida depende da sua linguagem e enquadramento de eleição:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está o código simplificado `HomeController`para a ação do . Este código recebe um símbolo para ligar para o Microsoft Graph. O código foi adicionado para mostrar como chamar o Microsoft Graph como um Rest API. O URL para o Microsoft Graph API é fornecido no ficheiro appsettings.json e é lido numa variável chamada: `webOptions`

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
   return me;
  }

  ViewData["Me"] = me;
  return View();
}
```

> [!NOTE]
> Pode usar o mesmo princípio para chamar qualquer API web.
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
> [Mover para produção](scenario-web-app-call-api-production.md)
