---
title: Azure Funções HTTP Trigger
description: Saiba como chamar uma Função Azure via HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: e8641cada03577cf22e6627bdf53b2a8a0e836ef
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216388"
---
# <a name="azure-functions-http-trigger"></a>Azure Funções HTTP Trigger

O gatilho HTTP permite-lhe invocar uma função com um pedido HTTP. Pode utilizar um gatilho HTTP para construir APIs sem servidor e responder a webhooks.

O valor de retorno predefinido para uma função desencadeada por HTTP é:

- `HTTP 204 No Content` com um corpo vazio em Funções 2.x e superior
- `HTTP 200 OK` com um corpo vazio em Funções 1.x

Para modificar a resposta HTTP, configurar uma [ligação de saída](./functions-bindings-http-webhook-output.md).

Para obter mais informações sobre as ligações HTTP, consulte a [visão geral](./functions-bindings-http-webhook.md) e [a referência de encadernação de saída.](./functions-bindings-http-webhook-output.md)

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que procura um `name` parâmetro na cadeia de consulta ou no corpo do pedido HTTP. Note que o valor de devolução é usado para a ligação de saída, mas não é necessário um atributo de valor de devolução.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função procura um `name` parâmetro na cadeia de consulta ou no corpo do pedido HTTP.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código de script C# que se liga `HttpRequest` a:

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
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Pode ligar-se a um objeto personalizado em vez de `HttpRequest` . Este objeto é criado a partir do corpo do pedido e analisado como JSON. Da mesma forma, um tipo pode ser passado para a ligação de saída http e devolvido como o corpo de resposta, juntamente com um `200` código de estado.

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

# <a name="java"></a>[Java](#tab/java)

* [Leia o parâmetro da cadeia de consulta](#read-parameter-from-the-query-string)
* [Leia o corpo a partir de um pedido de POST](#read-body-from-a-post-request)
* [Leia o parâmetro de uma rota](#read-parameter-from-a-route)
* [Leia o corpo do POJO a partir de um pedido de CORREIO](#read-pojo-body-from-a-post-request)

Os exemplos a seguir mostram a ligação do gatilho HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Leia o parâmetro da cadeia de consulta

Este exemplo lê um parâmetro, denominado `id` , a partir da cadeia de consulta, e usa-o para construir um documento JSON devolvido ao cliente, com o tipo de conteúdo `application/json` .

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

#### <a name="read-body-from-a-post-request"></a>Leia o corpo a partir de um pedido de POST

Este exemplo lê o corpo de um pedido POST, como `String` um, e usa-o para construir um documento JSON devolvido ao cliente, com tipo de `application/json` conteúdo.

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

#### <a name="read-parameter-from-a-route"></a>Leia o parâmetro de uma rota

Este exemplo lê um parâmetro obrigatório, `id` nomeado, e um parâmetro opcional `name` a partir do percurso, e usa-os para construir um documento JSON devolvido ao cliente, com tipo de `application/json` conteúdo. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Leia o corpo do POJO a partir de um pedido de CORREIO

Aqui está o código para a `ToDoItem` classe, referenciado neste exemplo:

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

Este exemplo lê o corpo de um pedido de CORREIO. O corpo de pedido é automaticamente des serializado num `ToDoItem` objeto, e é devolvido ao cliente, com o tipo de conteúdo `application/json` . O `ToDoItem` parâmetro é serializado pelo tempo de execução de Funções, uma vez que é atribuído à `body` propriedade da `HttpMessageResponse.Builder` classe.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função procura um `name` parâmetro na cadeia de consulta ou no corpo do pedido HTTP.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsem* ficheiro e numa [função PowerShell](functions-reference-node.md). A função procura um `name` parâmetro na cadeia de consulta ou no corpo do pedido HTTP.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsem* ficheiro e uma [função Python](functions-reference-python.md) que utiliza a ligação. A função procura um `name` parâmetro na cadeia de consulta ou no corpo do pedido HTTP.

Aqui está a *function.jsarquivada:*

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
            "name": "$return"
        }
    ]
}
```

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código Python:

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

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

Nas [bibliotecas de classe C#](functions-dotnet-class-library.md) e Java, o `HttpTrigger` atributo está disponível para configurar a função.

Pode definir o nível de autorização e os métodos HTTP admissíveis nos parâmetros de construção de atributos, tipo webhook e um modelo de rota. Para obter mais informações sobre estas definições, consulte [a configuração](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Este exemplo demonstra como utilizar o atributo [HttpTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs)

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para um exemplo completo, consulte o exemplo do [gatilho](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

Este exemplo demonstra como utilizar o atributo [HttpTrigger.](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java)

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Para um exemplo completo, consulte o exemplo do [gatilho](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `HttpTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/a| Necessário - deve ser definido para `httpTrigger` . |
| **direção** | n/a| Necessário - deve ser definido para `in` . |
| **nome** | n/a| Requerido - o nome variável utilizado no código de função para o órgão de pedido ou pedido. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais as teclas, se houver, que precisam de estar presentes no pedido para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Não é necessária nenhuma chave API.</li><li><code>function</code>&mdash;É necessária uma chave API específica para a função. Este é o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;A chave principal é necessária.</li></ul> Para mais informações, consulte a secção sobre [as chaves de autorização.](#authorization-keys) |
| **métodos** |**Métodos** | Uma matriz dos métodos HTTP aos quais a função responde. Se não for especificado, a função responde a todos os métodos HTTP. Consulte [personalizar o ponto final HTTP](#customize-the-http-endpoint). |
| **route** | **Rota** | Define o modelo de rota, controlando a que pedido URLs a sua função responde. O valor predefinido se nenhum for fornecido é `<functionname>` . Para mais informações, consulte [personalizar o ponto final HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Suportado apenas para a versão 1.x tempo de execução._<br/><br/>Configura o gatilho HTTP para funcionar como um recetor [webhook](https://en.wikipedia.org/wiki/Webhook) para o fornecedor especificado. Não desateia a `methods` propriedade se você definir esta propriedade. O tipo webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto final webhook de uso geral sem lógica para um fornecedor específico. Esta definição restringe os pedidos apenas a quem utiliza HTTP POST e com o `application/json` tipo de conteúdo.</li><li><code>github</code>&mdash;A função responde a [webhooks GitHub](https://developer.github.com/webhooks/). Não utilize a propriedade  _authLevel_ com webhooks GitHub. Para mais informações, consulte a secção de webhooks do GitHub mais tarde neste artigo.</li><li><code>slack</code>&mdash;A função responde a [webhooks Slack](https://api.slack.com/outgoing-webhooks). Não utilize a propriedade _authLevel_ com webhooks Slack. Para mais informações, consulte a secção De webhooks Slack mais tarde neste artigo.</li></ul>|

## <a name="payload"></a>Carga útil

O tipo de entrada do gatilho é declarado como `HttpRequest` um ou outro tipo personalizado. Se `HttpRequest` escolher, terá acesso total ao objeto pedido. Para um tipo personalizado, o tempo de execução tenta analisar o corpo de pedido JSON para definir as propriedades do objeto.

## <a name="customize-the-http-endpoint"></a>Personalize o ponto final HTTP

Por predefinição, quando cria uma função para um gatilho HTTP, a função é endereçada com uma rota do formulário:

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

Pode personalizar esta rota utilizando a propriedade opcional `route` na ligação de entrada do gatilho HTTP. Como exemplo, o seguinte *function.jsem* ficheiro define uma propriedade para um `route` gatilho HTTP:

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

Utilizando esta configuração, a função é agora endereçada com a seguinte rota em vez da rota original.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Esta configuração permite que o código de função suporte dois parâmetros no endereço, _categoria_ e _id_. Para obter mais informações sobre como os parâmetros de rota são tokenizados num URL, consulte [o Encaminhamento em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/routing#route-constraint-reference).

# <a name="c"></a>[C#](#tab/csharp)

Pode utilizar qualquer [Restrição de Rota da API web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os seus parâmetros. O seguinte código de função C# utiliza ambos os parâmetros.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Pode utilizar qualquer [Restrição de Rota da API web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os seus parâmetros. O seguinte código de função C# utiliza ambos os parâmetros.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

O contexto de execução da função são propriedades declaradas no `HttpTrigger` atributo. O atributo permite-lhe definir parâmetros de rota, níveis de autorização, verbos HTTP e a instância de pedido de entrada.

Os parâmetros de rota são definidos através do `HttpTrigger` atributo.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Em Nó, o tempo de execução de Funções fornece o corpo de pedido do `context` objeto. Para obter mais informações, consulte o exemplo do [gatilho JavaScript](#example).

O exemplo a seguir mostra como ler os parâmetros da rota a partir de `context.bindingData` .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os parâmetros de rota declarados no *function.jsem* ficheiro são acessíveis como propriedade do `$Request.Params` objeto.

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

O contexto de execução da função é exposto através de um parâmetro declarado como `func.HttpRequest` . Este caso permite que uma função aceda a parâmetros de rota de dados, valores de cadeia de consulta e métodos que lhe permitam devolver respostas HTTP.

Uma vez definidos, os parâmetros de rota estão disponíveis para a função, chamando o `route_params` método.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

Por predefinição, todas as vias de função são prefixadas com *api*. Também pode personalizar ou remover o prefixo usando a `extensions.http.routePrefix` propriedade na suahost.js[ficheiro.](functions-host-json.md) O exemplo a seguir remove o prefixo da rota *api* utilizando um fio vazio para o prefixo no *host.jsno* ficheiro.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>Usando parâmetros de rota

Os parâmetros de rota que definiram o padrão de uma função `route` estão disponíveis para cada encadernação. Por exemplo, se tiver uma rota definida como `"route": "products/{id}"` então uma ligação de armazenamento de mesa pode usar o valor do `{id}` parâmetro na configuração de encadernação.

A seguinte configuração mostra como o `{id}` parâmetro é passado para o da encadernação `rowKey` .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

Quando utiliza parâmetros de rota, `invoke_URL_template` um é automaticamente criado para a sua função. Os seus clientes podem usar o modelo de URL para entender os parâmetros que precisam de passar no URL ao ligar para a sua função usando o seu URL. Navegue para uma das suas funções desencadeadas por HTTP no [portal Azure](https://portal.azure.com) e selecione **Obter URL de função**.

Pode aceder programáticamente ao `invoke_URL_template` Azure Resource Manager APIs para [funções de lista](/rest/api/appservice/webapps/listfunctions) ou [obter função.](/rest/api/appservice/webapps/getfunction)

## <a name="working-with-client-identities"></a>Trabalhar com identidades de cliente

Se a sua aplicação de função estiver a utilizar [a Autenticação/Autorização do Serviço de Aplicações,](../app-service/overview-authentication-authorization.md)pode visualizar informações sobre clientes autenticados a partir do seu código. Esta informação está disponível como [cabeçalhos de pedido injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims).

Também pode ler estas informações a partir de dados vinculativos. Esta capacidade só está disponível para o tempo de funcionamento das Funções em 2.x ou superior. Atualmente também está disponível apenas para idiomas .NET.

# <a name="c"></a>[C#](#tab/csharp)

Informações relativas a clientes autenticados estão disponíveis como [SinistrosPrincipal.](/dotnet/api/system.security.claims.claimsprincipal) O ClaimsPrincipal está disponível como parte do contexto de pedido, como mostrado no seguinte exemplo:

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

Em alternativa, o ClaimsPrincipal pode simplesmente ser incluído como um parâmetro adicional na assinatura da função:

```csharp
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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Informações relativas a clientes autenticados estão disponíveis como [SinistrosPrincipal.](/dotnet/api/system.security.claims.claimsprincipal) O ClaimsPrincipal está disponível como parte do contexto de pedido, como mostrado no seguinte exemplo:

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

Em alternativa, o ClaimsPrincipal pode simplesmente ser incluído como um parâmetro adicional na assinatura da função:

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

# <a name="java"></a>[Java](#tab/java)

O utilizador autenticado está disponível através [de cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O utilizador autenticado está disponível através [de cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O utilizador autenticado está disponível através [de cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

O utilizador autenticado está disponível através [de cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>Teclas de acesso de função

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>Obtenção de chaves

As chaves são armazenadas como parte da sua aplicação de função em Azure e são encriptadas em repouso. Para visualizar as suas teclas, crie as novas ou role teclas para novos valores, navegue numa das suas funções desencadeadas por HTTP no [portal Azure](https://portal.azure.com) e selecione **Teclas de função**.

Também pode gerir as teclas do anfitrião. Navegue para a aplicação de função no [portal Azure](https://portal.azure.com) e selecione **as teclas de aplicação**.

Pode obter funções e anfitriões de chaves programáticamente utilizando as APIs do Gestor de Recursos Azure. Existem APIs para [listar teclas](/rest/api/appservice/webapps/listfunctionkeys) de função e [teclas de anfitrião da lista](/rest/api/appservice/webapps/listhostkeys), e quando se utilizam slots de APIs equivalentes são as ranhuras das [teclas de função da lista](/rest/api/appservice/webapps/listfunctionkeysslot) e a ranhura das [teclas do anfitrião da lista](/rest/api/appservice/webapps/listhostkeysslot).

Também pode criar novas funções e receber teclas programáticamente utilizando o Criar ou Atualizar o Segredo de [Função](/rest/api/appservice/webapps/createorupdatefunctionsecret), [criar ou atualizar a função secreta slot](/rest/api/appservice/webapps/createorupdatefunctionsecretslot), criar ou atualizar o segredo do [anfitrião](/rest/api/appservice/webapps/createorupdatehostsecret) e criar ou atualizar APIs [de slot secreta do anfitrião.](/rest/api/appservice/webapps/createorupdatehostsecretslot)

As teclas de função e de anfitrião podem ser eliminadas programáticamente utilizando o [Delete Function Secret](/rest/api/appservice/webapps/deletefunctionsecret), Delete Function Secret ( [Sessão Secreta)](/rest/api/appservice/webapps/deletefunctionsecretslot)e eliminar APIs de slot secreta do [](/rest/api/appservice/webapps/deletehostsecret) [anfitrião.](/rest/api/appservice/webapps/deletehostsecretslot)

Também pode utilizar as [APIs de gestão de chaves antigas para obter teclas de função](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), mas é recomendado utilizar as APIs do Gestor de Recursos Azure.

## <a name="api-key-authorization"></a>Autorização chave da API

A maioria dos modelos de gatilho HTTP requerem uma chave API no pedido. Assim, o seu pedido HTTP normalmente parece o seguinte URL:

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

A chave pode ser incluída numa variável de cadeia de consulta denominada `code` , como acima. Também pode ser incluído num `x-functions-key` cabeçalho HTTP. O valor da chave pode ser qualquer chave de função definida para a função, ou qualquer chave de anfitrião.

Pode permitir pedidos anónimos, que não requerem chaves. Também pode exigir que a chave principal seja usada. Altera o nível de autorização por defeito utilizando a `authLevel` propriedade no JSON de encadernação. Para obter mais informações, consulte [a configuração do Gatilho](#configuration).

> [!NOTE]
> Ao executar funções localmente, a autorização é desativada independentemente da definição de nível de autorização especificado. Após a publicação no Azure, a `authLevel` definição no seu gatilho é aplicada. As chaves ainda são necessárias quando [funcionam localmente num recipiente](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Garantir um ponto final HTTP na produção

Para garantir plenamente os pontos finais da sua função na produção, deve considerar a implementação de uma das seguintes opções de segurança ao nível da aplicação da função. Ao utilizar um destes métodos de segurança ao nível da aplicação de funções, deve definir o nível de autorização de função acionado pelo HTTP para `anonymous` .

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>Implemente a sua app de função isoladamente

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> O modo Webhook só está disponível para a versão 1.x do tempo de execução de Funções. Esta alteração foi feita para melhorar o desempenho dos gatilhos HTTP na versão 2.x e superior.

Na versão 1.x, os modelos webhook fornecem validação adicional para cargas webhook. Na versão 2.x e superior, o gatilho HTTP base ainda funciona e é a abordagem recomendada para webhooks. 

### <a name="github-webhooks"></a>Webhooks gitHub

Para responder a webhooks GitHub, primeiro crie a sua função com um trigger HTTP e desloque a propriedade **webHookType** para `github` . Em seguida, copie a sua chave URL e API na página **Add webhook** do seu repositório GitHub. 

![Screenshot que mostra como adicionar um webhook para a sua função.](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Webhooks de slack

O webhook Slack gera um símbolo para si em vez de o deixar especificar, por isso deve configurar uma chave específica da função com o token de Slack. Ver [chaves de autorização](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooks e chaves

A autorização webhook é manuseada pelo componente recetor webhook, parte do gatilho HTTP, e o mecanismo varia com base no tipo webhook. Cada mecanismo depende de uma chave. Por predefinição, é utilizada a chave de função denominada "predefinição". Para utilizar uma chave diferente, configuure o fornecedor webhook para enviar o nome chave com o pedido de uma das seguintes formas:

* **Cadeia de** consulta : O fornecedor passa o nome chave no parâmetro da `clientid` cadeia de consulta, tal como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` .
* **Cabeçalho de pedido**: O fornecedor passa o nome chave no `x-functions-clientid` cabeçalho.

## <a name="content-types"></a>Tipos de conteúdo

Passar dados binários e de formulário para uma função não-C# requer que utilize o cabeçalho apropriado do tipo conteúdo. Os tipos de conteúdo suportado incluem `octet-stream` dados binários e [tipos multipartes.](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart)

### <a name="known-issues"></a>Problemas conhecidos

Em funções não-C#, os pedidos enviados com o tipo de conteúdo `image/jpeg` resultam num `string` valor passado para a função. Em casos como este, pode converter manualmente o `string` valor num conjunto byte para aceder aos dados binários brutos.

## <a name="limits"></a>Limites

O comprimento do pedido HTTP é limitado a 100 MB (104.857.600 bytes), e o comprimento do URL é limitado a 4 KB (4.096 bytes). Estes limites são especificados pelo `httpRuntime` elemento do ficheiroWeb.config do tempo de [ execução](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config).

Se uma função que utiliza o gatilho HTTP não estiver concluída dentro de 230 segundos, o Balançador de [Carga Azure](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) irá esgotar-se e devolver-lhe um erro HTTP 502. A função continuará a funcionar mas não poderá devolver uma resposta HTTP. Para funções de longa duração, recomendamos que siga os padrões async e devolva um local onde possa verificar o estado do pedido. Para obter informações sobre quanto tempo uma função pode funcionar, consulte [Escala e hospedagem - Plano de consumo](functions-scale.md#timeout).


## <a name="next-steps"></a>Passos seguintes

- [Devolver uma resposta HTTP de uma função](./functions-bindings-http-webhook-output.md)
