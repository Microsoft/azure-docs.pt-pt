---
title: API web que chama APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4dfd2c7f9dbdd111f70a5dd5a648c11eacbf7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038548"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API web que chama APIs web: Chame uma API

Depois de ter um token, pode chamar uma API web protegida. Normalmente, liga para as APIs a jusante do controlador ou páginas da sua API web.

## <a name="controller-code"></a>Código do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Quando utilizar *o Microsoft.Identity.Web,* tem três cenários de utilização:

- [Opção 1: Ligue para o Gráfico da Microsoft com o SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Opção 2: Ligue para uma API web a jusante com a classe de ajudante](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opção 3: Ligue para uma API web a jusante sem a classe de ajudante](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opção 1: Ligue para o Gráfico da Microsoft com o SDK

Neste cenário, adicionou `.AddMicrosoftGraph()` no *Startup.cs* conforme especificado na [configuração de Código](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph), e pode injetar diretamente o seu controlador ou construtor de página para `GraphServiceClient` utilização nas ações. O exemplo seguinte A página Razor exibe a foto do utilizador inscrito.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Opção 2: Ligue para uma API web a jusante com a classe de ajudante

Neste cenário, adicionou `.AddDownstreamWebApi()` no *Startup.cs* conforme especificado na [configuração do Código,](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)e pode injetar diretamente um `IDownstreamWebApi` serviço no seu controlador ou construtor de página e usá-lo nas ações:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

O `CallWebApiForUserAsync` método também tem sobreposições genéricas fortemente dactilografadas que lhe permitem receber diretamente um objeto. Por exemplo, o seguinte método recebeu um `Todo` exemplo, que é uma representação fortemente dactilografada do JSON devolvido pela API web.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Opção 3: Ligue para uma API web a jusante sem a classe de ajudante

Se decidiu adquirir um símbolo manualmente usando o `ITokenAcquisition` serviço, agora precisa usar o token. Nesse caso, o seguinte código continua o código de exemplo mostrado numa [API web que chama APIs web: Adquira um símbolo para a aplicação](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores da API. Chama a abaixo a API chamada *lista de todos os.*

 Depois de ter adquirido o símbolo, use-o como símbolo portador para chamar a API a jusante.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

O seguinte código continua o código de exemplo que é mostrado numa [API web que chama APIs web: Adquira um símbolo para a aplicação](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores da API. Chama o gráfico a jusante da API MS.

Depois de ter adquirido o símbolo, use-o como símbolo portador para chamar a API a jusante.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Uma amostra que demonstra este fluxo com o MSAL Python está disponível em [ms-identidade-python-on-behalf-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of).

---

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [passe para a produção](scenario-web-api-call-api-production.md).
