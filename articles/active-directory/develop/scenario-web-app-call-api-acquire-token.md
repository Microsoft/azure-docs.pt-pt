---
title: Obtenha um token numa aplicação web que chama APIs web - Plataforma de identidade microsoft / Rio Azure
description: Saiba como adquirir um símbolo para uma aplicação web que chama APIs web
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
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181737"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma aplicação web que chama APIs web: Adquira um símbolo para a app

Construíste o objeto de candidatura ao teu cliente. Agora, vai usá-lo para adquirir um símbolo para chamar uma API web. Em ASP.NET ou ASP.NET Core, chamar uma API web é feito no controlador:

- Obtenha um símbolo para a API web usando a cache simbólica. Para obter este token, ligue para o método MSAL `AcquireTokenSilent` (ou o equivalente em Microsoft.Identity.Web).
- Chame a API protegida, passando o sinal de acesso para ele como um parâmetro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os métodos do controlador estão protegidos por um `[Authorize]` atributo que obriga os utilizadores a serem autenticados a utilizarem a aplicação web. Aqui está o código que chama o Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

O `ITokenAcquisition` serviço é injetado por ASP.NET utilizando a injeção de dependência.

Aqui está o código simplificado para a ação do `HomeController` , que recebe um símbolo para ligar para o Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Para melhor entender o código necessário para este cenário, consulte o passo da fase 2[(2-1-Web apps Calls Microsoft Graph)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)do tutorial [ms-identidade-aspnetcore-webapp-tutorial.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Existem outras variações complexas, tais como:

- Chamando várias APIs.
- Processamento de consentimento incremental e acesso condicional.

Estes passos avançados estão cobertos no capítulo 3 do tutorial [3-WebApp-multi-APIs.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código para ASP.NET é semelhante ao código mostrado para ASP.NET Core:

- Uma ação de controlador, protegida por um atributo [Authorize], extrai o ID do inquilino e a identificação do utilizador `ClaimsPrincipal` do membro do controlador. (ASP.NET `HttpContext.User` utiliza.)
- A partir daí, constrói um `IConfidentialClientApplication` objeto MSAL.NET.
- Finalmente, chama o `AcquireTokenSilent` método da aplicação confidencial do cliente.

# <a name="java"></a>[Java](#tab/java)

Na amostra de Java, o código que chama API está no método getUsersFromGraph em [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

O método tenta `getAuthResultBySilentFlow` chamar. Se o utilizador precisar de consentir com mais âmbitos, o código processa o `MsalInteractionRequiredException` objeto para desafiar o utilizador.

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

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
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
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

Na amostra Python, o código que chama Microsoft Graph está em [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

O código tenta obter um símbolo da cache simbólica. Em seguida, depois de definir o cabeçalho de autorização, chama a API web. Se não conseguir um token, assina o utilizador de novo.

```python
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
> [Chamar uma API Web](scenario-web-app-call-api-call-api.md)
