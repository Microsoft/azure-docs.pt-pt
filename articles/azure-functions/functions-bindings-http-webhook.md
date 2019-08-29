---
title: Azure Functions gatilhos e associações HTTP
description: Entenda como usar gatilhos e associações HTTP no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funções, processamento de eventos, WebHooks, computação dinâmica, arquitetura sem servidor, HTTP, API, REST
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 983cf250f3a7188741c41386aac256bfdb28749b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097329"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions gatilhos e associações HTTP

Este artigo explica como trabalhar com gatilhos HTTP e associações de saída no Azure Functions.

Um gatilho HTTP pode ser personalizado para responder a [](https://en.wikipedia.org/wiki/Webhook)WebHooks.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

O código neste artigo usa como padrão a sintaxe do Functions 2. x, que usa o .NET Core. Para obter informações sobre a sintaxe 1. x, consulte os [modelos de funções 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações HTTP são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) , versão 1. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

As associações HTTP são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) , versão 3. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) .

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho HTTP permite invocar uma função com uma solicitação HTTP. Você pode usar um gatilho HTTP para criar APIs sem servidor e responder a WebHooks.

Por padrão, um gatilho HTTP retorna HTTP 200 OK com um corpo vazio em Functions 1. x ou HTTP 204 sem conteúdo com um corpo vazio no functions 2. x. Para modificar a resposta, configure uma [Associação de saída http](#output).

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de idioma específico:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Acionador - exemplo do c#

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que procura um `name` parâmetro na cadeia de caracteres de consulta ou no corpo da solicitação HTTP. Observe que o valor de retorno é usado para a associação de saída, mas um atributo de valor de retorno não é necessário.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função procura um `name` parâmetro na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

Aqui está o *Function* ficheiro:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

O [configuração](#trigger---configuration) seção explica essas propriedades.

Aqui está C# o código de script que se `HttpRequest`associa a:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Você pode associar a um objeto personalizado em vez `HttpRequest`de. Esse objeto é criado a partir do corpo da solicitação e analisado como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída de resposta HTTP e retornado como o corpo da resposta, junto com um código de status 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Acionador - F# exemplo

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [ F# função](functions-reference-fsharp.md) que usa a associação. A função procura um `name` parâmetro na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

Aqui está o *Function* ficheiro:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O [configuração](#trigger---configuration) seção explica essas propriedades.

Aqui está o F# código:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Você precisa de `project.json` um arquivo que usa o NuGet para `FSharp.Interop.Dynamic` fazer `Dynamitey` referência aos assemblies e, conforme mostrado no exemplo a seguir:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função procura um `name` parâmetro na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

Aqui está o *Function* ficheiro:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

O [configuração](#trigger---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### <a name="trigger---python-example"></a>Gatilho-exemplo de Python

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função procura um `name` parâmetro na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

Aqui está o *Function* ficheiro:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

O [configuração](#trigger---configuration) seção explica essas propriedades.

Este é o código Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>Gatilho-exemplos de Java

* [Ler parâmetro da cadeia de caracteres de consulta](#read-parameter-from-the-query-string-java)
* [Ler o corpo de uma solicitação POST](#read-body-from-a-post-request-java)
* [Ler parâmetro de uma rota](#read-parameter-from-a-route-java)
* [Ler o corpo do POJO de uma solicitação POST](#read-pojo-body-from-a-post-request-java)

Os exemplos a seguir mostram a associação de gatilho HTTP em um arquivo *Function. JSON* e as respectivas [funções Java](functions-reference-java.md) que usam a associação. 

Aqui está o *Function* ficheiro:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>Parâmetro de leitura da cadeia de caracteres de consulta (Java)  

Este exemplo lê um parâmetro, chamado ```id```, da cadeia de caracteres de consulta e o usa para criar um documento JSON retornado ao cliente, com o tipo ```application/json```de conteúdo. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Ler o corpo de uma solicitação POST (Java)  

Este exemplo lê o corpo de uma solicitação post, como uma ```String```, e a usa para criar um documento JSON retornado ao cliente, com o tipo ```application/json```de conteúdo.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Ler parâmetro de uma rota (Java)  

Este exemplo lê um parâmetro obrigatório, chamado ```id```, e um parâmetro ```name``` opcional do caminho de rota e os usa para criar um documento JSON retornado ao cliente, com o tipo ```application/json```de conteúdo. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Ler o corpo do POJO de uma solicitação POST (Java)  

Este é o código para a ```ToDoItem``` classe, referenciado neste exemplo:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Este exemplo lê o corpo de uma solicitação POST. O corpo da solicitação é desserializado automaticamente em ```ToDoItem``` um objeto e é retornado para o cliente, com o tipo ```application/json```de conteúdo. O ```ToDoItem``` parâmetro é serializado pelo tempo de execução do functions, pois ele ```body``` é atribuído à ```HttpMessageResponse.Builder``` propriedade da classe.

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Acionador - atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

Você pode definir o nível de autorização e os métodos HTTP permitidos em parâmetros de construtor de atributo e há propriedades para tipo de webhook e modelo de rota. Para obter mais informações sobre essas configurações, consulte [gatilho-configuração](#trigger---configuration). Aqui está um `HttpTrigger` atributo em uma assinatura de método:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para obter um exemplo completo, consulte [acionador - exemplo do c#](#trigger---c-example).

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `HttpTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **type** | n/d| Obrigatório-deve ser definido como `httpTrigger`. |
| **direction** | n/d| Obrigatório-deve ser definido como `in`. |
| **name** | n/d| Obrigatório-o nome da variável usada no código de função para a solicitação ou o corpo da solicitação. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Nenhuma chave de API é necessária.</li><li><code>function</code>&mdash;Uma chave de API específica de função é necessária. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave mestra é necessária.</li></ul> Para obter mais informações, consulte a seção sobre [chaves de autorização](#authorization-keys). |
| **methods** |**Methods** | Uma matriz dos métodos HTTP para os quais a função responde. Se não for especificado, a função responderá a todos os métodos HTTP. Consulte [Personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **rota** | **Rota** | Define o modelo de rota, controlando a quais URLs de solicitação sua função responde. O valor padrão se nenhum for fornecido é `<functionname>`. Para obter mais informações, consulte [Personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Com suporte apenas para o tempo de execução da versão 1. x._<br/><br/>Configura o gatilho HTTP para atuar como um receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para o provedor especificado. Não defina a `methods` Propriedade se você definir essa propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto de extremidade de webhook de uso geral sem lógica para um provedor específico. Essa configuração restringe as solicitações para apenas as que usam http post e `application/json` com o tipo de conteúdo.</li><li><code>github</code>&mdash;A função responde a WebHooks do [GitHub](https://developer.github.com/webhooks/). Não use a propriedade _authLevel_ com WebHooks do github. Para obter mais informações, consulte a seção WebHooks do GitHub posteriormente neste artigo.</li><li><code>slack</code>&mdash;A função responde a WebHooks de [margem de atraso](https://api.slack.com/outgoing-webhooks). Não use a propriedade _authLevel_ com WebHooks de margem de atraso. Para obter mais informações, consulte a seção da margem de atraso WebHooks posteriormente neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Acionador - utilização

Para C# funções F# e, você pode declarar o tipo de sua entrada de gatilho `HttpRequest` para ser ou um tipo personalizado. Se você escolher `HttpRequest`, obterá acesso completo ao objeto de solicitação. Para um tipo personalizado, o tempo de execução tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto.

Para funções de JavaScript, o tempo de execução do Functions fornece o corpo da solicitação em vez do objeto de solicitação. Para obter mais informações, consulte o [exemplo de gatilho de JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Personalizar o ponto de extremidade HTTP

Por padrão, quando você cria uma função para um gatilho HTTP, a função é endereçável com uma rota do formulário:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Você pode personalizar essa rota usando a propriedade `route` opcional na associação de entrada do gatilho http. Por exemplo, o seguinte arquivo *Function. JSON* define uma `route` propriedade para um gatilho http:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Usando essa configuração, a função é agora endereçável com a rota a seguir em vez da rota original.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Isso permite que o código de função dê suporte a dois parâmetros no endereço, na _categoria_ e na _ID_. Você pode usar qualquer [restrição de rota da API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O código C# de função a seguir usa ambos os parâmetros.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Aqui está o código de função node. js que usa os mesmos parâmetros de rota.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Por padrão, todas as rotas de função são prefixadas com a *API*. Você também pode personalizar ou remover o prefixo usando a `http.routePrefix` Propriedade em seu arquivo [host. JSON](functions-host-json.md) . O exemplo a seguir remove o prefixo de rota da *API* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host. JSON* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Trabalhando com identidades de cliente

Se seu aplicativo de funções estiver usando a [autenticação/autorização do serviço de aplicativo](../app-service/overview-authentication-authorization.md), você poderá exibir informações sobre clientes autenticados do seu código. Essas informações estão disponíveis como [cabeçalhos de solicitação injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Você também pode ler essas informações de dados de associação. Esse recurso só está disponível para o tempo de execução do Functions 2. x. Ele também está disponível apenas para linguagens .NET.

Em linguagens .NET, essas informações estão disponíveis como um [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Como alternativa, o ClaimsPrincipal pode simplesmente ser incluído como um parâmetro adicional na assinatura da função:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Chaves de autorização

As funções permitem usar chaves para tornar mais difícil acessar seus pontos de extremidade de função HTTP durante o desenvolvimento.  Um gatilho HTTP padrão pode exigir que tal chave de API esteja presente na solicitação. 

> [!IMPORTANT]
> Embora as chaves possam ajudar a ofuscar seus pontos de extremidade HTTP durante o desenvolvimento, elas não são pretendidas como uma maneira de proteger um gatilho HTTP na produção. Para saber mais, confira [proteger um ponto de extremidade http em produção](#secure-an-http-endpoint-in-production).

> [!NOTE]
> No tempo de execução do Functions 1. x, os provedores de webhook podem usar chaves para autorizar solicitações de várias maneiras, dependendo do suporte do provedor. Isso é abordado em WebHooks [e chaves](#webhooks-and-keys). O tempo de execução da versão 2. x não inclui suporte interno para provedores de webhook.

Há dois tipos de chaves:

* **Chaves de host**: Essas chaves são compartilhadas por todas as funções no aplicativo de funções. Quando usado como uma chave de API, eles permitem o acesso a qualquer função dentro do aplicativo de funções.
* **Teclas de função**: Essas chaves se aplicam somente às funções específicas sob as quais elas são definidas. Quando usado como uma chave de API, eles só permitem o acesso a essa função.

Cada chave é nomeada para referência, e há uma chave padrão (denominada "padrão") no nível da função e do host. As teclas de função têm precedência sobre as chaves de host. Quando duas chaves são definidas com o mesmo nome, a tecla de função sempre é usada.

Cada aplicativo de funções também tem uma **chave mestra**especial. Essa chave é uma chave de host `_master`chamada, que fornece acesso administrativo às APIs de tempo de execução. Esta chave não pode ser revogada. Quando você define um nível de autorização `admin`de, as solicitações devem usar a chave mestra; qualquer outra chave resulta em falha de autorização.

> [!CAUTION]  
> Devido às permissões elevadas em seu aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros ou distribuí-la em aplicativos cliente nativos. Tome cuidado ao escolher o nível de autorização do administrador.

### <a name="obtaining-keys"></a>Obtendo chaves

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e são criptografadas em repouso. Para exibir suas chaves, criar novas ou reverter chaves para novos valores, navegue até uma das funções disparadas por HTTP no [portal do Azure](https://portal.azure.com) e selecione **gerenciar**.

![Gerenciar chaves de função no Portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Você pode obter as chaves de função programaticamente usando a [API de gerenciamento de chaves](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Autorização de chave de API

A maioria dos modelos de gatilho HTTP exigem uma chave de API na solicitação. Portanto, sua solicitação HTTP normalmente se parece com a seguinte URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A chave pode ser incluída em uma variável de cadeia de `code`caracteres de consulta chamada, como acima. Ele também pode ser incluído em um `x-functions-key` cabeçalho http. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, que não exigem chaves. Você também pode exigir que a chave mestra seja usada. Você altera o nível de autorização padrão usando a `authLevel` Propriedade no JSON de associação. Para obter mais informações, consulte [gatilho-configuração](#trigger---configuration).

> [!NOTE]
> Ao executar funções localmente, a autorização é desabilitada, independentemente da configuração do nível de autenticação especificado. Após a publicação no Azure, `authLevel` a configuração em seu gatilho é imposta.



### <a name="secure-an-http-endpoint-in-production"></a>Proteger um ponto de extremidade HTTP na produção

Para proteger totalmente seus pontos de extremidade de função em produção, você deve considerar a implementação de uma das seguintes opções de segurança no nível do aplicativo de funções:

* Ative a autenticação/autorização do serviço de aplicativo para seu aplicativo de funções. A plataforma do serviço de aplicativo permite usar Azure Active Directory (AAD) e vários provedores de identidade de terceiros para autenticar clientes. Você pode usar isso para implementar regras de autorização personalizadas para suas funções e pode trabalhar com informações de usuário do seu código de função. Para saber mais, consulte [autenticação e autorização no serviço Azure app](../app-service/overview-authentication-authorization.md) e [trabalhando com identidades de cliente](#working-with-client-identities).

* Use o APIM (gerenciamento de API do Azure) para autenticar solicitações. O APIM fornece uma variedade de opções de segurança de API para solicitações de entrada. Para saber mais, consulte [políticas de autenticação de gerenciamento de API](../api-management/api-management-authentication-policies.md). Com o APIM em vigor, você pode configurar seu aplicativo de funções para aceitar solicitações somente do endereço IP da sua instância do APIM. Para saber mais, consulte [restrições de endereço IP](ip-addresses.md#ip-address-restrictions).

* Implante seu aplicativo de funções em um Ambiente do Serviço de Aplicativo do Azure (ASE). O ASE fornece um ambiente de hospedagem dedicado no qual executar suas funções. O ASE permite configurar um único gateway de front-end que você pode usar para autenticar todas as solicitações de entrada. Para obter mais informações, consulte Configurando [um WAF (firewall do aplicativo Web) para ambiente do serviço de aplicativo](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ao usar um desses métodos de segurança de nível de aplicativo de função, você deve definir o nível de autenticação da função `anonymous`disparada por http como.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> O modo de webhook só está disponível para a versão 1. x do tempo de execução do functions. Essa alteração foi feita para melhorar o desempenho de gatilhos HTTP na versão 2. x.

Na versão 1. x, os modelos de webhook fornecem validação adicional para cargas de webhook. Na versão 2. x, o gatilho HTTP base ainda funciona e é a abordagem recomendada para WebHooks. 

#### <a name="github-webhooks"></a>WebHooks do GitHub

Para responder a WebHooks do GitHub, primeiro crie sua função com um gatilho HTTP e defina a Propriedade webhooktype como `github`. Em seguida, copie sua URL e a chave de API na página **Adicionar webhook** do seu repositório github. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Subganchos de margem de atraso

O webhook de margem de atraso gera um token para você em vez de permitir que você o especifique, portanto, você deve configurar uma chave específica de função com o token da margem de atraso. Consulte [chaves de autorização](#authorization-keys).

### <a name="webhooks-and-keys"></a>WebHooks e chaves

A autorização de webhook é tratada pelo componente receptor de webhook, parte do gatilho HTTP e o mecanismo varia de acordo com o tipo de webhook. Cada mecanismo depende de uma chave. Por padrão, a chave de função chamada "default" é usada. Para usar uma chave diferente, configure o provedor de webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

* **Cadeia de caracteres de consulta**: O provedor passa o nome da chave no `clientid` parâmetro de cadeia de caracteres de `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`consulta, como.
* **Cabeçalho da solicitação**: O provedor passa o nome da chave no `x-functions-clientid` cabeçalho.

## <a name="trigger---limits"></a>Gatilho-limites

O comprimento da solicitação HTTP é limitado a 100 MB (104.857.600 bytes) e o comprimento da URL é limitado a 4 KB (4.096 bytes). Esses limites são especificados pelo `httpRuntime` elemento do [arquivo Web. config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)do tempo de execução.

Se uma função que usa o gatilho HTTP não for concluída em cerca de 2,5 minutos, o gateway atingirá o tempo limite e retornará um erro HTTP 502. A função continuará em execução, mas não poderá retornar uma resposta HTTP. Para funções de execução longa, recomendamos que você siga os padrões assíncronos e retorne um local em que você possa executar o ping do status da solicitação. Para obter informações sobre quanto tempo uma função pode ser executada, consulte [escala e hospedagem – plano de consumo](functions-scale.md#timeout).

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O arquivo [host. JSON](functions-host-json.md) contém configurações que controlam o comportamento do gatilho http.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Essa associação requer um gatilho HTTP e permite que você personalize a resposta associada à solicitação do gatilho. Se uma associação de saída HTTP não for fornecida, um gatilho HTTP retornará HTTP 200 OK com um corpo vazio em Functions 1. x ou HTTP 204 sem conteúdo com um corpo vazio no functions 2. x.

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definiu no arquivo *Function. JSON* . Para C# bibliotecas de classes, não há propriedades de atributo que correspondam a essas propriedades *Function. JSON* .

|Propriedade  |Descrição  |
|---------|---------|
| **type** |Tem de ser definido como `http`. |
| **direction** | Tem de ser definido como `out`. |
|**name** | O nome da variável usada no código de função para a resposta `$return` ou para usar o valor de retorno. |

## <a name="output---usage"></a>Saída - utilização

Para enviar uma resposta HTTP, use os padrões de resposta padrão de idioma. Em C# ou C# script, torne o tipo `IActionResult` de retorno da função ou. `Task<IActionResult>` No C#, um atributo de valor de retorno não é necessário.

Por exemplo, respostas, consulte o [exemplo de gatilho](#trigger---example).

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
