---
title: Obtenha um símbolo para uma API web que chama APIs web | Rio Azure
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
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756396"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API web que chama APIs web: Adquira um símbolo para a app

Depois de ter construído um objeto de aplicação do cliente, use-o para adquirir um símbolo que pode usar para chamar uma API web.

## <a name="code-in-the-controller"></a>Código no controlador

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* adiciona métodos de extensão que fornecem serviços de conveniência para ligar para o Microsoft Graph ou uma API web a jusante. Estes métodos são explicados em detalhe numa [API web que chama APIs web: Chame uma API](scenario-web-api-call-api-call-api.md). Com estes métodos de ajuda, não precisa de adquirir manualmente um símbolo.

Se, no entanto, pretender adquirir manualmente um token, o seguinte código mostra um exemplo de utilização do *Microsoft.Identity.Web* para o fazer num controlador API. Chama a abaixo a API chamada *lista de todos os.*
Para obter um token para ligar para a API a jusante, injeta o `ITokenAcquisition` serviço por injeção de dependência no construtor do seu controlador (ou no construtor de página se utilizar o Blazor), e utiliza-o nas ações do seu controlador, obtendo um sinal para o utilizador ( `GetAccessTokenForUserAsync` ) ou para a própria aplicação ( `GetAccessTokenForAppAsync` ) em caso de cenário de daemon.

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

Para obter mais informações sobre o `callTodoListService` método, consulte  [uma API web que chama APIs web: Ligue para uma API](scenario-web-api-call-api-call-api.md).

### <a name="java"></a>[Java](#tab/java)

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

### <a name="python"></a>[Python](#tab/python)
 
Uma API web Python requer o uso de middleware para validar o sinal do portador recebido do cliente. A API web pode então obter o token de acesso para uma API a jusante usando a biblioteca MSAL Python, chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método.
 
Aqui está um exemplo de código que adquire um token de acesso usando o `acquire_token_on_behalf_of` método e a estrutura do Flask. Chama a API a jusante - o ponto final das Assinaturas de Gestão Azure.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>(Avançado) Aceder à cache simbólica do utilizador inscrito a partir de aplicações de fundo, APIs e serviços

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [Chame uma API](scenario-web-api-call-api-call-api.md).
