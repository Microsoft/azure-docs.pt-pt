---
title: Obtenha um símbolo numa aplicação web que ligue para APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como adquirir um símbolo para uma aplicação web que chama APIs web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfd479382cb69e7355b033312e165699223fdbf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756306"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma aplicação web que chama APIs web: Adquira um símbolo para a app

Construíste o objeto de candidatura ao teu cliente. Agora, vai usá-lo para adquirir um símbolo para chamar uma API web. Em ASP.NET ou ASP.NET Core, chamar uma API web é feito no controlador:

- Obtenha um símbolo para a API web usando a cache simbólica. Para obter este token, ligue para o método MSAL `AcquireTokenSilent` (ou o equivalente em Microsoft.Identity.Web).
- Chame a API protegida, passando o sinal de acesso para ele como um parâmetro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* adiciona métodos de extensão que fornecem serviços de conveniência para ligar para o Microsoft Graph ou uma API web a jusante. Estes métodos são explicados em detalhe numa [aplicação web que chama APIs web: Call a API](scenario-web-app-call-api-call-api.md). Com estes métodos de ajuda, não precisa de adquirir manualmente um símbolo.

Se, no entanto, pretender adquirir manualmente um token, o seguinte código mostra um exemplo de utilização do *Microsoft.Identity.Web* para o fazer num controlador doméstico. Chama o Microsoft Graph utilizando a API REST (em vez do Microsoft Graph SDK). Para obter um token para ligar para a API a jusante, injeta o `ITokenAcquisition` serviço por injeção de dependência no construtor do seu controlador (ou no seu construtor de página se utilizar o Blazor), e utiliza-o nas ações do seu controlador, obtendo um sinal para o utilizador `GetAccessTokenForUserAsync` () ou para a própria aplicação `GetAccessTokenForAppAsync` () num cenário de daemon.

Os métodos do controlador estão protegidos por um `[Authorize]` atributo que garante que apenas os utilizadores autenticados podem utilizar a aplicação web.

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
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
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

O `AuthorizeForScopes` atributo em cima da ação do controlador (ou da página Razor se utilizar um modelo Razor) é fornecido pelo Microsoft.Identity.Web. Garante que o utilizador é solicitado o consentimento se necessário, e incrementalmente.

Existem outras variações complexas, tais como:

- Chamando várias APIs.
- Processamento de consentimento incremental e acesso condicional.

Estes passos avançados estão cobertos no capítulo 3 do tutorial [3-WebApp-multi-APIs.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código para ASP.NET é semelhante ao código mostrado para ASP.NET Core:

- Uma ação de controlador, protegida por um atributo [Authorize], extrai o ID do inquilino e a identificação do utilizador `ClaimsPrincipal` do membro do controlador. (ASP.NET `HttpContext.User` utiliza.)
- A partir daí, constrói um `IConfidentialClientApplication` objeto MSAL.NET.
- Finalmente, chama o `AcquireTokenSilent` método da aplicação confidencial do cliente.
- Se for necessária interação, a aplicação web precisa de desafiar o utilizador (re-signiná-lo) e pedir mais reclamações.

O seguinte corte de código é extraído do [HomeController.cs#L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) na amostra [de código ms-identidade-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET amostra de código MVC:

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

Para mais detalhes consulte o código para [BuildConfidentialClientApplication()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) e [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) na amostra de código


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

Na amostra Python, o código que chama Microsoft Graph está na [aplicação.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

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

Passe para o próximo artigo neste cenário, [Chame uma API web](scenario-web-app-call-api-call-api.md).
