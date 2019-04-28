---
title: Enlaces e acionadores de HTTP de funções do Azure
description: Aprenda a utilizar HTTP acionadores e enlaces nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, eventos de processamento, webhooks, dinâmicos REST de computação, arquitetura sem servidor, HTTP, API,
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: a1d66cf4506e3b8f58572576db908812f4e2be07
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104374"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Enlaces e acionadores de HTTP de funções do Azure

Este artigo explica como trabalhar com acionadores HTTP e enlaces de saída nas funções do Azure.

Um acionador HTTP pode ser personalizado para responder às [webhooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

O código neste artigo por predefinição, sintaxe de 2.x de funções que usa o .NET Core. Para obter informações sobre a sintaxe de 1.x, consulte a [modelos de funções de 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces de HTTP são fornecidos na [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pacote NuGet, versão 1.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces de HTTP são fornecidos na [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) pacote NuGet, versão 3.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O acionador HTTP permite-lhe invocar uma função com uma solicitação HTTP. Pode utilizar um acionador HTTP para criar APIs sem servidor e responder a webhooks.

Por predefinição, um acionador HTTP devolve HTTP 200 OK com um corpo vazio nas funções 1.x ou HTTP 204 sem conteúdo com um corpo vazio nas funções 2.x. Para modificar a resposta, configure uma [enlace de saída HTTP](#output).

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de idioma específico:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Acionador - exemplo do c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que procura um `name` parâmetro na cadeia de consulta ou o corpo da solicitação HTTP. Tenha em atenção que o valor de retorno é usado para o enlace de saída, mas um atributo de valor de retorno não é necessário.

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

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função procura um `name` parâmetro na cadeia de consulta ou o corpo da solicitação HTTP.

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

Aqui está o script código c# que liga `HttpRequest`:

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

É possível ligar a um objeto personalizado em vez de `HttpRequest`. Este objeto é criado a partir do corpo do pedido e analisar como JSON. Da mesma forma, um tipo pode ser transmitido para a resposta HTTP de enlace de saída e retornado como o corpo da resposta, juntamente com um código de 200 estado.

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

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [ F# função](functions-reference-fsharp.md) que utiliza o enlace. A função procura um `name` parâmetro na cadeia de consulta ou o corpo da solicitação HTTP.

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

É necessário um `project.json` ficheiro que utiliza o NuGet para fazer referência a `FSharp.Interop.Dynamic` e `Dynamitey` assemblies, conforme mostrado no exemplo a seguir:

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

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função procura um `name` parâmetro na cadeia de consulta ou o corpo da solicitação HTTP.

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

### <a name="trigger---python-example"></a>Acionador - exemplo de Python

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [função Python](functions-reference-python.md) que utiliza o enlace. A função procura um `name` parâmetro na cadeia de consulta ou o corpo da solicitação HTTP.

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

Aqui está o código de Python:

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

### <a name="trigger---java-examples"></a>Acionador - exemplos de Java

* [Leia o parâmetro da cadeia de consulta](#read-parameter-from-the-query-string-java)
* [Ler o corpo de um pedido POST](#read-body-from-a-post-request-java)
* [Parâmetro de leitura de uma rota](#read-parameter-from-a-route-java)
* [Corpo de POJO de leitura de um pedido POST](#read-pojo-body-from-a-post-request-java)

Os exemplos seguintes mostram o acionador HTTP de enlace num *Function* ficheiro e respetivos [das funções do Java](functions-reference-java.md) que usar a ligação. 

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

#### <a name="read-parameter-from-the-query-string-java"></a>Leia o parâmetro da cadeia de consulta (Java)  

Este exemplo lê um parâmetro, denominado ```id```, na cadeia de consulta e utiliza-o para criar um documento JSON devolvido ao cliente, com o tipo de conteúdo ```application/json```. 

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

#### <a name="read-body-from-a-post-request-java"></a>Ler o corpo de um pedido POST (Java)  

Este exemplo lê o corpo do pedido POST, como um ```String```e utiliza-o para criar um documento JSON devolvido ao cliente, com o tipo de conteúdo ```application/json```.

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

#### <a name="read-parameter-from-a-route-java"></a>Parâmetro de leitura de uma rota (Java)  

Este exemplo lê um parâmetro obrigatório, com o nome ```id```e um parâmetro opcional ```name``` partir do caminho de rota e utiliza-os para criar um documento JSON devolvido ao cliente, com o tipo de conteúdo ```application/json```. T

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

#### <a name="read-pojo-body-from-a-post-request-java"></a>Corpo de POJO de leitura de um pedido POST (Java)  

Aqui está o código para o ```ToDoItem``` classe, referenciado neste exemplo:

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

Este exemplo lê o corpo de um pedido POST. O corpo do pedido obtém automaticamente desserializado num ```ToDoItem``` de objeto e é devolvido ao cliente, com o tipo de conteúdo ```application/json```. O ```ToDoItem``` parâmetro é serializado pelo tempo de execução de funções à medida que é atribuído para o ```body``` propriedade do ```HttpMessageResponse.Builder``` classe.

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

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atributo.

A autorização pode definir métodos HTTP de nível e permitidos nos parâmetros do construtor de atributo e existem propriedades para o modelo de tipo e a rota do webhook. Para obter mais informações sobre estas definições, consulte [acionador - configuração](#trigger---configuration). Aqui está um `HttpTrigger` atributo numa assinatura do método:

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
| **tipo** | n/d| Necessário - tem de ser definido como `httpTrigger`. |
| **direção** | n/d| Necessário - tem de ser definido como `in`. |
| **name** | n/d| Necessário - o nome da variável no código de função para a pedido ou corpo do pedido. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina o chaves, se houver, precisam de estar presente no pedido para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Nenhuma chave de API é necessário.</li><li><code>function</code>&mdash;É necessária uma chave de API específicas. Este é o valor predefinido se não for fornecido nenhum.</li><li><code>admin</code>&mdash;A chave mestra é necessária.</li></ul> Para obter mais informações, consulte a secção [chaves de autorização](#authorization-keys). |
| **Métodos** |**Métodos** | Uma matriz dos métodos HTTP para o qual a função responde. Se não for especificado, a função responde a todos os métodos HTTP. Ver [personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **route** | **rota** | Define o modelo de rota, controlar a que URLs responde de sua função de pedido. O valor predefinido se não for fornecida nenhuma é `<functionname>`. Para obter mais informações, consulte [personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Suportado apenas para o tempo de execução do versão 1.x._<br/><br/>Configura o acionador HTTP para atuar como um [webhook](https://en.wikipedia.org/wiki/Webhook) recetor para o fornecedor especificado. Não definir o `methods` propriedade se definir esta propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto final do webhook para fins gerais sem lógica para um provedor específico. Esta definição limita pedidos apenas às através de HTTP POST e com o `application/json` tipo de conteúdo.</li><li><code>github</code>&mdash;A função responde às [GitHub webhooks](https://developer.github.com/webhooks/). Não utilize o _authLevel_ propriedade com o GitHub webhooks. Para obter mais informações, consulte a secção de webhooks do GitHub neste artigo.</li><li><code>slack</code>&mdash;A função responde às [Slack webhooks](https://api.slack.com/outgoing-webhooks). Não utilize o _authLevel_ propriedade com Slack webhooks. Para obter mais informações, consulte a secção de Slack webhooks neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Acionador - utilização

Para C# e F# as funções, é possível declarar o tipo de Acionador de entrada de ser um `HttpRequest` ou um tipo personalizado. Se escolher `HttpRequest`, obtém acesso total para o objeto de solicitação. Para um tipo personalizado, o tempo de execução tenta analisar o corpo do pedido JSON para definir as propriedades do objeto.

Para funções de JavaScript, o runtime das funções fornece o corpo do pedido em vez do objeto de solicitação. Para obter mais informações, consulte a [exemplo de Acionador de JavaScript](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Personalizar o ponto final HTTP

Por predefinição quando cria uma função para um acionador HTTP, a função é endereçável com uma rota do formulário:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Pode personalizar esta rota com o opcional `route` propriedade ao acionador de HTTP do enlace de entrada. Por exemplo, o seguinte procedimento *Function* arquivo define um `route` propriedade para um acionador HTTP:

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

Utilizando esta configuração, a função está agora acessível com a seguinte rota em vez da rota original.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Isso permite que o código de função oferecer suporte a dois parâmetros no endereço _categoria_ e _id_. Pode usar qualquer [restrição de rota da API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os parâmetros. O seguinte código de função do c# faz uso de ambos os parâmetros.

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

Aqui está o código de função de node. js que utiliza os mesmos parâmetros de rota.

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

Por predefinição, todas as rotas de função têm o prefixo *api*. Também pode personalizar ou remover a prefixo com o `http.routePrefix` propriedade em seu [Host. JSON](functions-host-json.md) ficheiro. O exemplo seguinte remove o *api* prefixo de rota, utilizando uma cadeia vazia para o prefixo no *Host. JSON* ficheiro.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Trabalhar com identidades de cliente

Se estiver a utilizar a aplicação de funções [aplicação serviço de autenticação / autorização](../app-service/overview-authentication-authorization.md), pode ver informações sobre os clientes autenticados a partir do código. Essas informações estão disponíveis como [injetados pela plataforma de cabeçalhos de pedido](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Também é possível ler essas informações de vinculação de dados. Esta funcionalidade só está disponível para o runtime 2.x de funções. Também está atualmente apenas disponível para linguagens .NET.

Em linguagens .NET, esta informação está disponível como um [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal?view=netstandard-2.0). A ClaimsPrincipal está disponível como parte do contexto do pedido, conforme mostrado no exemplo a seguir:

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

Em alternativa, o ClaimsPrincipal pode ser incluído como um parâmetro adicional na assinatura de função:

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

As funções permite-lhe utilizar as chaves para que seja mais difícil acessar os pontos finais de função HTTP durante o desenvolvimento.  Um acionador HTTP padrão pode exigir que uma chave de API de estar presente no pedido. 

> [!IMPORTANT]
> Embora as chaves podem ajudar a ofuscar os pontos finais HTTP durante o desenvolvimento, estas não recomendações pretendem como uma forma de proteger um acionador HTTP na produção. Para obter mais informações, consulte [proteger um ponto de final HTTP na produção](#secure-an-http-endpoint-in-production).

> [!NOTE]
> O runtime 1.x das funções, fornecedores de webhook podem utilizar as chaves para autorizar os pedidos de diversas formas, consoante o que o fornecedor suporta. Isto é explicado na [Webhooks e chaves](#webhooks-and-keys). A versão 2.x do runtime não inclui suporte incorporado para fornecedores de webhook.

Existem dois tipos de chaves:

* **Das chaves de anfitrião**: Estas chaves são partilhadas por todas as funções dentro da aplicação de função. Quando utilizado como uma chave de API, elas permitem o acesso a qualquer função dentro da aplicação de função.
* **Chaves de função**: Essas chaves aplicam-se apenas a funções específicas, sob a qual são definidas. Quando utilizado como uma chave de API, estes apenas permitem o acesso a essa função.

Cada chave é chamado para referência e há uma chave de padrão (denominada "predefinido") ao nível da função e o anfitrião. Teclas de função têm precedência sobre as chaves de anfitrião. Quando duas chaves forem definidas com o mesmo nome, a tecla de função é sempre usada.

Cada aplicação de função também tem um especial **chave mestra**. Esta chave é uma chave de anfitrião com o nome `_master`, que fornece acesso administrativo para o tempo de execução APIs. Esta chave não pode ser revogada. Quando define uma autorização ao nível de `admin`, pedidos têm de utilizar a chave mestra; qualquer outra chave resulta numa falha de autorização.

> [!CAUTION]  
> Devido a permissões elevadas na sua aplicação de função concedidas pela chave mestra, não deve partilhar esta chave com terceiros ou distribuí-la em aplicativos de cliente nativo. Tenha cuidado ao escolher o nível de autorização de administrador.

### <a name="obtaining-keys"></a>Obter chaves

As chaves são encriptadas em inatividade e são armazenadas como parte da sua aplicação de função no Azure. Para ver as suas chaves, criar novos, ou distribuir as chaves para novos valores, navegue para uma das suas funções acionada por HTTP no [portal do Azure](https://portal.azure.com) e selecione **gerir**.

![Gerir chaves de função no portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Para obter programaticamente teclas de função, não há nenhuma API suportada.

### <a name="api-key-authorization"></a>Autorização da chave de API

A maioria dos modelos de Acionador HTTP requerem uma chave de API no pedido. Portanto, o seu pedido HTTP normalmente é parecido com o seguinte URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A chave pode ser incluída numa variável de cadeia de caracteres de consulta com o nome `code`, como anteriormente. Também pode ser incluído num `x-functions-key` cabeçalho de HTTP. O valor da chave pode ser qualquer tecla de função definido para a função, ou qualquer chave de anfitrião.

Pode permitir pedidos anónimos, que não necessitam de chaves. Também pode exigir que a chave mestra de ser utilizada. Alterar o nível de autorização predefinido utilizando o `authLevel` propriedade no vínculo da JSON. Para obter mais informações, consulte [acionador - configuração](#trigger---configuration).

> [!NOTE]
> Ao executar as funções localmente, a autorização está desativada, independentemente da definição de nível de autenticação especificado. Após a publicação para o Azure, o `authLevel` é imposta a definição no seu acionador.



### <a name="secure-an-http-endpoint-in-production"></a>Proteger um ponto de final HTTP na produção

Para proteger completamente seus pontos de extremidade de função na produção, deve considerar a implementação de uma das seguintes opções de segurança ao nível da aplicação de função:

* Ativar a autenticação do serviço de aplicação / autorização para a sua aplicação de função. A plataforma de serviço de aplicações permite utilizar o Azure Active Directory (AAD) e de vários fornecedores de identidade de terceiros para autenticar clientes. Pode utilizá-lo para implementar regras de autorização personalizada para as suas funções e pode trabalhar com informações de utilizador a partir do código de função. Para obter mais informações, consulte [autenticação e autorização no serviço de aplicações do Azure](../app-service/overview-authentication-authorization.md) e [trabalhar com identidades-cliente](#working-with-client-identities).

* Utilize a gestão de API do Azure (APIM) para autenticar pedidos. APIM fornece uma variedade de opções de segurança de API para pedidos recebidos. Para obter mais informações, consulte [políticas de autenticação de gestão de API](../api-management/api-management-authentication-policies.md). Com APIM no local, pode configurar a aplicação de funções para aceitar pedidos apenas a partir do endereço IP da sua instância APIM. Para obter mais informações, consulte [restrições de endereço IP](ip-addresses.md#ip-address-restrictions).

* Implemente a sua aplicação de função para um ambiente de serviço do Azure aplicações (ASE). ASE fornece um ambiente de alojamento dedicado para executar as suas funções. ASE permite-lhe configurar um único gateway de front-end que pode utilizar para autenticar a todos os pedidos recebidos. Para obter mais informações, consulte [configurar uma Firewall de aplicações Web (WAF) para o ambiente de serviço de aplicações](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Quando utilizar um dos seguintes métodos de segurança ao nível da aplicação de função, deve definir a autenticação de função acionada por HTTP para `anonymous`.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> Modo de Webhook só está disponível para a versão 1.x do runtime das funções. Esta alteração foi feita para melhorar o desempenho de acionadores HTTP na versão 2.x.

Na versão 1.x, modelos de webhook fornecerem validação adicional para cargas de webhook. Na versão 2.x, o acionador HTTP base ainda funciona e é a abordagem recomendada para webhooks. 

#### <a name="github-webhooks"></a>GitHub webhooks

Para responder a webhooks do GitHub, primeiro crie a sua função com um acionador HTTP e defina a **webHookType** propriedade `github`. Em seguida, copie a chave de API e o URL para o **adicionar webhook** página do seu repositório do GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack webhooks

O webhook Slack gera um token para, em vez de permitir que especifique, pelo que tem de configurar uma chave específica de função com o token de Slack. Ver [chaves de autorização](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks e chaves

Autorização de Webhook é processada pelo componente de destinatário do webhook, parte do acionador HTTP, e o mecanismo varia consoante o tipo de webhook. Cada mecanismo de contar com uma chave. Por predefinição, é utilizada a tecla de função com o nome "predefinição". Para utilizar uma chave diferente, configure o fornecedor de webhook para enviar o nome da chave com o pedido de uma das seguintes formas:

* **Cadeia de consulta**: O fornecedor transmite o nome da chave no `clientid` consulta, como o parâmetro de cadeia de caracteres, `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Cabeçalho do pedido**: O fornecedor transmite o nome da chave no `x-functions-clientid` cabeçalho.

## <a name="trigger---limits"></a>Acionador - limites

O comprimento do pedido HTTP está limitado a 100 MB (104,857,600 bytes) e o comprimento de URL está limitado a 4 KB (4,096 bytes). Estes limites são especificados pela `httpRuntime` elemento do tempo de execução [arquivo Web. config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Se uma função que usa o acionador HTTP não concluída no prazo de cerca de 2,5 minutos, o tempo limite do gateway irá e devolver um erro de HTTP 502. A função vai continuar em execução, mas será possível devolver uma resposta HTTP. Para as funções de execução longa, recomendamos que siga os padrões do async e devolver uma localização onde pode efetuar o ping do Estado do pedido. Para obter informações sobre o tempo que pode executar uma função, veja [plano de consumo de dimensionamento e alojamento -](functions-scale.md#consumption-plan).

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O [Host. JSON](functions-host-json.md) ficheiro contém as definições que controlam o comportamento de Acionador HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Saída

Utilize a enlace de responder para o remetente de pedido HTTP de saída HTTP. Essa ligação requer um acionador HTTP e permite-lhe personalizar a resposta associada à solicitação do acionador. Se o enlace de saída de um HTTP não for fornecido, um acionador HTTP retorna HTTP 200 OK, com um corpo vazio nas funções 1.x ou HTTP 204 sem conteúdo com um corpo vazio nas funções 2.x.

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro. Para c# bibliotecas de classe, não há atributo propriedades que correspondem para cada uma delas *Function* propriedades.

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |Tem de ser definido como `http`. |
| **direção** | Tem de ser definido como `out`. |
|**name** | O nome da variável no código de função para a resposta, ou `$return` para utilizar o valor de retorno. |

## <a name="output---usage"></a>Saída - utilização

Para enviar uma resposta HTTP, use os padrões de resposta de idioma padrão. Em c# ou de script c#, tornar a função de tipo de retorno `IActionResult` ou `Task<IActionResult>`. No c#, um atributo de valor de retorno não a é necessário.

Por exemplo respostas, consulte a [exemplo de Acionador](#trigger---example).

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
