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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a259fbcf3fde84edccafbcd2fd6594ddb623edfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175323"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplicativo Web que chama APIs da Web – adquirir um token para o aplicativo

Agora que você criou o objeto de aplicativo cliente, você o usará para adquirir um token para chamar uma API da Web. Em ASP.NET ou ASP.NET Core, a chamada a uma API da Web é feita no controlador. É sobre:

- Obter um token para a API Web usando o cache de token. Para obter esse token, você chama `AcquireTokenSilent`.
- Chamar a API protegida com o token de acesso.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Os métodos do controlador são protegidos por um atributo `[Authorize]` que força os usuários que estão sendo autenticados a usar o aplicativo Web. Aqui está o código que chama Microsoft Graph.

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
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
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

- Uma ação de controlador protegida por um atributo [autorizar] extrai a ID de locatário e a ID de usuário do membro de `ClaimsPrincipal` do controlador. (ASP.NET usa `HttpContext.User`.)
- A partir daí, ele cria um MSAL.NET `IConfidentialClientApplication`.
- Por fim, ele chama o método `AcquireTokenSilent` do aplicativo cliente confidencial.

O código é semelhante ao código mostrado para ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

No exemplo de Java, o código que chama uma API está no método getUsersFromGraph [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Ele tenta chamar `getAuthResultBySilentFlow`. Se o usuário precisar dar consentimento a mais escopos, o código processará o `MsalInteractionRequiredException` para desafiar o usuário.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
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
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
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
