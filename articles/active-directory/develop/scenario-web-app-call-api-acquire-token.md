---
title: Obtenha um símbolo numa aplicação web que chama APIs web - plataforma de identidade microsoft / Azure
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
ms.openlocfilehash: 1069b4288f8253ccb9a7774b3144d10d85dcdd36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537122"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma aplicação web que chama APIs web: Adquirir um símbolo para a app

Construíste o objeto de aplicação do teu cliente. Agora, vaiusá-lo para adquirir um símbolo para chamar uma API web. Em ASP.NET ou ASP.NET Core, chamar uma API web é feito no controlador:

- Obtenha um símbolo para a Web API usando a cache simbólica. Para obter este símbolo, `AcquireTokenSilent` chame o método.
- Chame a API protegida, passando o sinal de acesso como parâmetro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os métodos do controlador `[Authorize]` são protegidos por um atributo que obriga os utilizadores a serem autenticados a usar a aplicação web. Aqui está o código que chama microsoft graph:

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

Aqui está o código simplificado `HomeController`para a ação do , que recebe um símbolo para chamar Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Para melhor compreender o código necessário para este cenário, consulte a fase 2[(2-1-Web app Call Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) passo do tutorial [ms-identity-aspnetcore-webapp-tutorial.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Existem outras variações complexas, tais como:

- Chamando várias APIs.
- Processamento de consentimento incremental e acesso condicional.

Estes passos avançados são cobertos no capítulo 3 do tutorial [3-WebApp-multi-APIs.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código para ASP.NET é semelhante ao código mostrado para ASP.NET Core:

- Uma ação do controlador, protegida por um atributo [Autorizar], extrai o ID do inquilino e a identificação do utilizador do `ClaimsPrincipal` membro do controlador. (ASP.NET `HttpContext.User`utiliza .)
- A partir daí, constrói `IConfidentialClientApplication` um objeto MSAL.NET.
- Finalmente, chama `AcquireTokenSilent` o método da aplicação confidencial do cliente.

# <a name="java"></a>[Java](#tab/java)

Na amostra java, o código que chama a API está no método getUsersFromGraph em [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

O método tenta `getAuthResultBySilentFlow`chamar. Se o utilizador necessitar de consentir com mais `MsalInteractionRequiredException` âmbitos, o código processa o objeto para desafiar o utilizador.

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

O código tenta obter um símbolo da cache simbólica. Em seguida, depois de definir o cabeçalho de autorização, chama a Web API. Se não conseguir um símbolo, volta a entrar o utilizador.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-web-app-call-api-call-api.md)
