---
title: Obtenha um símbolo para uma API web que chama APIs web Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs web que requerem a aquisição de um símbolo para a app.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026463"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API web que chama APIs web: Adquira um símbolo para a app

Depois de ter construído um objeto de aplicação do cliente, use-o para adquirir um símbolo que pode usar para chamar uma API web.

## <a name="code-in-the-controller"></a>Código no controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está um exemplo de código usando Microsoft.Identity.Web que é chamado nas ações dos controladores API. Chama a abaixo a API chamada *lista de todos os.* Para obter um token para ligar para a API a jusante, injeta o `ITokenAcquisition` serviço por injeção de dependência no construtor do seu controlador (ou no construtor de página se utilizar o Blazor), e utiliza-o nas ações do seu controlador, obtendo um sinal para o utilizador ( `GetAccessTokenForUserAsync` ) ou para a própria aplicação ( `GetAccessTokenForAppAsync` ) em caso de cenário de daemon.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Para obter mais informações sobre o `callTodoListService` método, consulte [uma API web que chama APIs web: Ligue para uma API](scenario-web-api-call-api-call-api.md).

# <a name="java"></a>[Java](#tab/java)
Aqui está um exemplo de código que é chamado nas ações dos controladores da API. Chama a API a jusante - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Uma API web Python terá de usar algum middleware para validar o sinal do portador recebido do cliente. A API web pode então obter o token de acesso para a API a jusante usando a biblioteca MSAL Python, chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método. Uma amostra que demonstra este fluxo com o MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: Chame uma API](scenario-web-api-call-api-call-api.md)
