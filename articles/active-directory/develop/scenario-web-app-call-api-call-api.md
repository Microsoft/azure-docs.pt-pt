---
title: Ligue para uma api web a partir de uma aplicação web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação web que chame APIs web (chamando uma API web protegida)
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
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753291"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Uma aplicação web que chama APIs web: Ligue para uma API web

Agora que tem um símbolo, pode chamar uma API protegida da web. Normalmente, chama uma API a jusante do controlador ou páginas da sua aplicação web.

## <a name="call-a-protected-web-api"></a>Chame uma API web protegida

Chamar uma API web protegida depende da sua linguagem e enquadramento de escolha:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Quando utilizar *o Microsoft.Identity.Web,* tem três opções de utilização para chamar uma API:

- [Opção 1: Ligue para o Microsoft Graph com o Microsoft Graph SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Opção 2: Ligue para uma API web a jusante com a classe de ajudante](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Opção 3: Ligue para uma API web a jusante sem a classe de ajudante](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Opção 1: Ligue para o Gráfico da Microsoft com o SDK

Quer ligar para o Microsoft Graph. Neste cenário, adicionou `AddMicrosoftGraph` em *Startup.cs* conforme especificado na [configuração do Código](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph), e pode injetar diretamente o seu controlador ou construtor de página para `GraphServiceClient` utilização nas ações. O exemplo seguinte A página Razor exibe a foto do utilizador inscrito.

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

Pretende chamar uma API web diferente do Microsoft Graph. Nesse caso, adicionou `AddDownstreamWebApi` em *Startup.cs* conforme especificado na [configuração do Código](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), e pode injetar diretamente um serviço no seu controlador ou construtor de páginas e `IDownstreamWebApi` usá-lo nas ações:

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
}
```

O `CallWebApiForUserAsync` também tem sobreposições genéricas fortemente dactilografadas que lhe permitem receber diretamente um objeto. Por exemplo, o seguinte método recebe um `Todo` exemplo, que é uma representação fortemente dactilografada do JSON devolvido pela API web.

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

Você decidiu adquirir um símbolo manualmente usando o `ITokenAcquisition` serviço, e agora precisa usar o token. Nesse caso, o seguinte código continua o código de exemplo mostrado numa [aplicação web que chama APIs web: Adquira um símbolo para a aplicação](scenario-web-app-call-api-acquire-token.md). O código é chamado nas ações dos controladores de aplicações web.

Depois de adquirir o token, use-o como um símbolo portador para ligar para a API a jusante, neste caso Microsoft Graph.

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
> A maioria das APIs web Azure fornecem um SDK que simplifica a chamada de API como é o caso do Microsoft Graph. Consulte, por exemplo, [criar uma aplicação web que autorize o acesso ao armazenamento blob com Azure AD](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para um exemplo de uma aplicação web usando Microsoft.Identity.Web e usando o Azure Storage SDK.

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

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [passe para a produção](scenario-web-app-call-api-production.md).