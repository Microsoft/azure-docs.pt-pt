---
title: Aplicativo Web que chama APIs da Web (adquirir um token para o aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs da Web (adquirindo um token para o aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fd66dcd6e3845aad79ebffb3cad656d0a14c1a6
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720221"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplicativo Web que chama APIs da Web – adquirir um token para o aplicativo

Agora que você criou o objeto de aplicativo cliente, você o usará para adquirir um token para chamar uma API da Web. Em ASP.NET ou ASP.NET Core, a chamada a uma API da Web é feita no controlador. É sobre:

- Obter um token para a API Web usando o cache de token. Para obter esse token, você chama `AcquireTokenSilent`.
- Chamar a API protegida com o token de acesso.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Os métodos do controlador são protegidos por `[Authorize]` um atributo que força os usuários que estão sendo autenticados a usar o aplicativo Web. Aqui está o código que chama Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

O serviço `ITokenAcquisition` é injetado por ASP.NET por meio de injeção de dependência.


Aqui está um código simplificado da ação do HomeController, que obtém um token para chamar o Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

// use the access token to call a protected web API
HttpClient client = new HttpClient();
client.DefaultRequestHeaders.Add("Authorization", result.CreateAuthorizationHeader());
string json = await client.GetStringAsync(url);
}
```

Para entender com mais detalhes o código necessário para esse cenário, consulte a etapa 2 ([2-1-chamadas de aplicativo Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) do tutorial [MS-Identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Há muitas complexidades adicionais, como:

- Chamando várias APIs,
- processando o consentimento incremental e o acesso condicional.

Essas etapas avançadas são processadas no capítulo 3 do tutorial [3-webapp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

As coisas são semelhantes em ASP.NET:

- Uma ação do controlador protegida por um atributo [autorizar] extrai a ID do locatário e a ID de usuário do membro `ClaimsPrincipal` do controlador. (O ASP.NET `HttpContext.User`usa.)
- A partir daí, ele cria um `IConfidentialClientApplication`MSAL.net.
- Por fim, ele chama `AcquireTokenSilent` o método do aplicativo cliente confidencial.

O código é semelhante ao código mostrado para ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

No exemplo de Java, o código que chama uma API está no método getUsersFromGraph [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Ele tenta chamar `getAuthResultBySilentFlow`. Se o usuário precisar dar consentimento a mais escopos, o código processará o `MsalInteractionRequiredException` para desafiar o usuário.

```java
@RequestMapping("/msal4jsample/graph/users")
    public ModelAndView getUsersFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
            throws Throwable {

        IAuthenticationResult result;
        ModelAndView mav;
        try {
            result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
        } catch (ExecutionException e) {
            if (e.getCause() instanceof MsalInteractionRequiredException) {

                // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
                // so user can consent to new scopes
                String state = UUID.randomUUID().toString();
                String nonce = UUID.randomUUID().toString();

                SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

                String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                        httpRequest.getParameter("claims"),
                        "User.ReadBasic.all",
                        authHelper.getRedirectUriGraphUsers(),
                        state,
                        nonce);

                return new ModelAndView("redirect:" + authorizationCodeUrl);
            } else {

                mav = new ModelAndView("error");
                mav.addObject("error", e);
                return mav;
            }
        }
    // Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

No exemplo de Python, o código que chama o Microsoft Graph está em [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Ele tenta obter um token do cache de token e, em seguida, chama a API EB depois de definir o cabeçalho Authorization. Se não puder, ele assinará novamente o usuário.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-web-app-call-api-call-api.md)
