---
title: Azure Functions gatilhos e associações HTTP
description: Entenda como usar gatilhos e associações HTTP no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 40456b2a756d5ae2241b54ff65f675004c22f0a2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890355"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions gatilhos e associações HTTP

Este artigo explica como trabalhar com gatilhos HTTP e associações de saída no Azure Functions.

Um gatilho HTTP pode ser personalizado para responder a [WebHooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

O código neste artigo assume como padrão a sintaxe que usa o .NET Core, usado nas funções versão 2. x e superior. Para obter informações sobre a sintaxe 1. x, consulte os [modelos de funções 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações HTTP são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) , versão 1. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As associações HTTP são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) , versão 3. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) .

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho HTTP permite invocar uma função com uma solicitação HTTP. Você pode usar um gatilho HTTP para criar APIs sem servidor e responder a WebHooks.

Por padrão, um gatilho HTTP retorna HTTP 200 OK com um corpo vazio em Functions 1. x ou HTTP 204 sem conteúdo com um corpo vazio no functions 2. x e superior. Para modificar a resposta, configure uma [Associação de saída http](#output).

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP. Observe que o valor de retorno é usado para a associação de saída, mas um atributo de valor de retorno não é necessário.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

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

Aqui está C# o código de script que se associa a `HttpRequest`:

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

Você pode associar a um objeto personalizado em vez de `HttpRequest`. Esse objeto é criado a partir do corpo da solicitação e analisado como JSON. Da mesma forma, um tipo pode ser passado para a associação de saída de resposta HTTP e retornado como o corpo da resposta, junto com um código de status 200.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação. A função procura um parâmetro `name` na cadeia de caracteres de consulta ou no corpo da solicitação HTTP.

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

# <a name="javatabjava"></a>[Java](#tab/java)

* [Ler parâmetro da cadeia de caracteres de consulta](#read-parameter-from-the-query-string)
* [Ler o corpo de uma solicitação POST](#read-body-from-a-post-request)
* [Ler parâmetro de uma rota](#read-parameter-from-a-route)
* [Ler o corpo do POJO de uma solicitação POST](#read-pojo-body-from-a-post-request)

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

#### <a name="read-parameter-from-the-query-string"></a>Ler parâmetro da cadeia de caracteres de consulta

Este exemplo lê um parâmetro, chamado ```id```, da cadeia de caracteres de consulta e o usa para criar um documento JSON retornado ao cliente, com o tipo de conteúdo ```application/json```. 

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

#### <a name="read-body-from-a-post-request"></a>Ler o corpo de uma solicitação POST

Este exemplo lê o corpo de uma solicitação POST, como uma ```String```e a usa para criar um documento JSON retornado ao cliente, com o tipo de conteúdo ```application/json```.

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

#### <a name="read-parameter-from-a-route"></a>Ler parâmetro de uma rota

Este exemplo lê um parâmetro obrigatório, chamado ```id```, e um parâmetro opcional ```name``` do caminho de rota e os usa para criar um documento JSON retornado ao cliente, com o tipo de conteúdo ```application/json```. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Ler o corpo do POJO de uma solicitação POST

Este é o código para a classe ```ToDoItem```, referenciada neste exemplo:

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

Este exemplo lê o corpo de uma solicitação POST. O corpo da solicitação é desserializado automaticamente em um objeto de ```ToDoItem``` e é retornado para o cliente, com o tipo de conteúdo ```application/json```. O parâmetro ```ToDoItem``` é serializado pelo tempo de execução do functions, pois ele é atribuído à propriedade ```body``` da classe ```HttpMessageResponse.Builder```.

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

---

## <a name="trigger---attributes"></a>Acionador - atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md) e Java, o atributo `HttpTrigger` está disponível para configurar a função.

Você pode definir o nível de autorização e os métodos HTTP permitidos em parâmetros de construtor de atributo, tipo de webhook e um modelo de rota. Para obter mais informações sobre essas configurações, consulte [gatilho-configuração](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para obter um exemplo completo, consulte o [exemplo de gatilho](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Este exemplo demonstra como usar o atributo [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

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

Para obter um exemplo completo, consulte o [exemplo de gatilho](#trigger---example).

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `HttpTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório-deve ser definido como `httpTrigger`. |
| **direção** | n/d| Obrigatório-deve ser definido como `in`. |
| **name** | n/d| Obrigatório-o nome da variável usada no código de função para a solicitação ou o corpo da solicitação. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais chaves, se houver, precisam estar presentes na solicitação para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;nenhuma chave de API é necessária.</li><li><code>function</code>&mdash;uma chave de API específica de função é necessária. Esse será o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;chave mestra é necessária.</li></ul> Para obter mais informações, consulte a seção sobre [chaves de autorização](#authorization-keys). |
| **methods** |**Methods** | Uma matriz dos métodos HTTP para os quais a função responde. Se não for especificado, a função responderá a todos os métodos HTTP. Consulte [Personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **rota** | **Rota** | Define o modelo de rota, controlando a quais URLs de solicitação sua função responde. O valor padrão, se nenhum for fornecido, será `<functionname>`. Para obter mais informações, consulte [Personalizar o ponto de extremidade http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Com suporte apenas para o tempo de execução da versão 1. x._<br/><br/>Configura o gatilho HTTP para atuar como um receptor de [webhook](https://en.wikipedia.org/wiki/Webhook) para o provedor especificado. Não defina a propriedade `methods` se você definir essa propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;um ponto de extremidade de webhook de finalidade geral sem lógica para um provedor específico. Essa configuração restringe as solicitações para apenas as que usam HTTP POST e com o tipo de conteúdo `application/json`.</li><li><code>github</code>&mdash;a função responde a [WebHooks do GitHub](https://developer.github.com/webhooks/). Não use a propriedade _authLevel_ com WebHooks do github. Para obter mais informações, consulte a seção WebHooks do GitHub posteriormente neste artigo.</li><li><code>slack</code>&mdash;a função responde a [WebHooks de margem de atraso](https://api.slack.com/outgoing-webhooks). Não use a propriedade _authLevel_ com WebHooks de margem de atraso. Para obter mais informações, consulte a seção da margem de atraso WebHooks posteriormente neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Acionador - utilização

O tipo de entrada do gatilho é declarado como `HttpRequest` ou um tipo personalizado. Se você escolher `HttpRequest`, obterá acesso completo ao objeto de solicitação. Para um tipo personalizado, o tempo de execução tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto.

### <a name="customize-the-http-endpoint"></a>Personalizar o ponto de extremidade HTTP

Por padrão, quando você cria uma função para um gatilho HTTP, a função é endereçável com uma rota do formulário:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Você pode personalizar essa rota usando a propriedade opcional `route` na associação de entrada do gatilho HTTP. Por exemplo, o arquivo *Function. JSON* a seguir define uma propriedade `route` para um gatilho http:

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Isso permite que o código de função dê suporte a dois parâmetros no endereço, na _categoria_ e na _ID_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Você pode usar qualquer [restrição de rota da API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O código C# de função a seguir usa ambos os parâmetros.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Você pode usar qualquer [restrição de rota da API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com seus parâmetros. O código C# de função a seguir usa ambos os parâmetros.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

No nó, o tempo de execução do Functions fornece o corpo da solicitação do objeto `context`. Para obter mais informações, consulte o [exemplo de gatilho de JavaScript](#trigger---example).

O exemplo a seguir mostra como ler parâmetros de rota de `context.bindingData`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O contexto de execução da função é exposto por meio de um parâmetro declarado como `func.HttpRequest`. Essa instância permite que uma função acesse parâmetros de rota de dados, valores de cadeia de caracteres de consulta e métodos que permitem retornar respostas HTTP.

Uma vez definidas, os parâmetros de rota estão disponíveis para a função chamando o método `route_params`.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="javatabjava"></a>[Java](#tab/java)

O contexto de execução da função é propriedades como declaradas no atributo `HttpTrigger`. O atributo permite que você defina parâmetros de rota, níveis de autorização, verbos HTTP e a instância de solicitação de entrada.

Os parâmetros de rota são definidos por meio do atributo `HttpTrigger`.

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

---

Por padrão, todas as rotas de função são prefixadas com a *API*. Você também pode personalizar ou remover o prefixo usando a propriedade `http.routePrefix` em seu arquivo [host. JSON](functions-host-json.md) . O exemplo a seguir remove o prefixo de rota da *API* usando uma cadeia de caracteres vazia para o prefixo no arquivo *host. JSON* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="using-route-parameters"></a>Usando parâmetros de rota

Os parâmetros de rota que definiram o padrão de `route` de uma função estão disponíveis para cada associação. Por exemplo, se você tiver uma rota definida como `"route": "products/{id}"`, uma associação de armazenamento de tabela poderá usar o valor do parâmetro `{id}` na configuração de associação.

A configuração a seguir mostra como o parâmetro `{id}` é passado para a `rowKey`da associação.

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


### <a name="working-with-client-identities"></a>Trabalhando com identidades de cliente

Se seu aplicativo de funções estiver usando a [autenticação/autorização do serviço de aplicativo](../app-service/overview-authentication-authorization.md), você poderá exibir informações sobre clientes autenticados do seu código. Essas informações estão disponíveis como [cabeçalhos de solicitação injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Você também pode ler essas informações de dados de associação. Esse recurso está disponível somente para o tempo de execução do Functions no 2. x e superior. Ele também está disponível apenas para linguagens .NET.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

As informações sobre clientes autenticados estão disponíveis como um [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

As informações sobre clientes autenticados estão disponíveis como um [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). O ClaimsPrincipal está disponível como parte do contexto da solicitação, conforme mostrado no exemplo a seguir:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="pythontabpython"></a>[Python](#tab/python)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javatabjava"></a>[Java](#tab/java)

O usuário autenticado está disponível por meio de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

### <a name="authorization-keys"></a>Chaves de autorização

As funções permitem usar chaves para tornar mais difícil acessar seus pontos de extremidade de função HTTP durante o desenvolvimento.  Um gatilho HTTP padrão pode exigir que tal chave de API esteja presente na solicitação. 

> [!IMPORTANT]
> Embora as chaves possam ajudar a ofuscar seus pontos de extremidade HTTP durante o desenvolvimento, elas não são pretendidas como uma maneira de proteger um gatilho HTTP na produção. Para saber mais, confira [proteger um ponto de extremidade http em produção](#secure-an-http-endpoint-in-production).

> [!NOTE]
> No tempo de execução do Functions 1. x, os provedores de webhook podem usar chaves para autorizar solicitações de várias maneiras, dependendo do suporte do provedor. Isso é abordado em [WebHooks e chaves](#webhooks-and-keys). O tempo de execução do Functions na versão 2. x e superior não inclui suporte interno para provedores de webhook.

Há dois tipos de chaves:

* **Chaves de host**: essas chaves são compartilhadas por todas as funções dentro do aplicativo de funções. Quando usado como uma chave de API, eles permitem o acesso a qualquer função dentro do aplicativo de funções.
* **Teclas de função**: essas chaves se aplicam somente às funções específicas sob as quais elas são definidas. Quando usado como uma chave de API, eles só permitem o acesso a essa função.

Cada chave é nomeada para referência, e há uma chave padrão (denominada "padrão") no nível da função e do host. As teclas de função têm precedência sobre as chaves de host. Quando duas chaves são definidas com o mesmo nome, a tecla de função sempre é usada.

Cada aplicativo de funções também tem uma **chave mestra**especial. Essa chave é uma chave de host chamada `_master`, que fornece acesso administrativo às APIs de tempo de execução. Esta chave não pode ser revogada. Quando você define um nível de autorização de `admin`, as solicitações devem usar a chave mestra; qualquer outra chave resulta em falha de autorização.

> [!CAUTION]  
> Devido às permissões elevadas em seu aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros ou distribuí-la em aplicativos cliente nativos. Tome cuidado ao escolher o nível de autorização do administrador.

### <a name="obtaining-keys"></a>Obtendo chaves

As chaves são armazenadas como parte do seu aplicativo de funções no Azure e são criptografadas em repouso. Para exibir suas chaves, criar novas ou reverter chaves para novos valores, navegue até uma das funções disparadas por HTTP no [portal do Azure](https://portal.azure.com) e selecione **gerenciar**.

![Gerenciar chaves de função no Portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Você pode obter as chaves de função programaticamente usando [APIs de gerenciamento de chaves](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Autorização de chave de API

A maioria dos modelos de gatilho HTTP exigem uma chave de API na solicitação. Portanto, sua solicitação HTTP normalmente se parece com a seguinte URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A chave pode ser incluída em uma variável de cadeia de caracteres de consulta chamada `code`, como acima. Ele também pode ser incluído em um cabeçalho HTTP `x-functions-key`. O valor da chave pode ser qualquer chave de função definida para a função ou qualquer chave de host.

Você pode permitir solicitações anônimas, que não exigem chaves. Você também pode exigir que a chave mestra seja usada. Você altera o nível de autorização padrão usando a propriedade `authLevel` no JSON de associação. Para obter mais informações, consulte [gatilho-configuração](#trigger---configuration).

> [!NOTE]
> Ao executar funções localmente, a autorização é desabilitada independentemente da configuração do nível de autorização especificado. Após a publicação no Azure, a configuração de `authLevel` em seu gatilho é imposta. As chaves ainda são necessárias ao executar [localmente em um contêiner](functions-create-function-linux-custom-image.md#run-the-image-locally).


### <a name="secure-an-http-endpoint-in-production"></a>Proteger um ponto de extremidade HTTP na produção

Para proteger totalmente seus pontos de extremidade de função em produção, você deve considerar a implementação de uma das seguintes opções de segurança no nível do aplicativo de funções:

* Ative a autenticação/autorização do serviço de aplicativo para seu aplicativo de funções. A plataforma do serviço de aplicativo permite que você use Azure Active Directory (AAD) e vários provedores de identidade de terceiros para autenticar clientes. Você pode usar isso para implementar regras de autorização personalizadas para suas funções e pode trabalhar com informações de usuário do seu código de função. Para saber mais, consulte [autenticação e autorização no serviço Azure app](../app-service/overview-authentication-authorization.md) e [trabalhando com identidades de cliente](#working-with-client-identities).

* Use o APIM (gerenciamento de API do Azure) para autenticar solicitações. O APIM fornece uma variedade de opções de segurança de API para solicitações de entrada. Para saber mais, consulte [políticas de autenticação de gerenciamento de API](../api-management/api-management-authentication-policies.md). Com o APIM em vigor, você pode configurar seu aplicativo de funções para aceitar solicitações somente do endereço IP da sua instância do APIM. Para saber mais, consulte [restrições de endereço IP](ip-addresses.md#ip-address-restrictions).

* Implante seu aplicativo de funções em um Ambiente do Serviço de Aplicativo do Azure (ASE). O ASE fornece um ambiente de hospedagem dedicado no qual executar suas funções. O ASE permite configurar um único gateway de front-end que você pode usar para autenticar todas as solicitações de entrada. Para obter mais informações, consulte [Configurando um WAF (firewall do aplicativo Web) para ambiente do serviço de aplicativo](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Ao usar um desses métodos de segurança de nível de aplicativo de função, você deve definir o nível de autorização da função disparada por HTTP como `anonymous`.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> O modo de webhook só está disponível para a versão 1. x do tempo de execução do functions. Essa alteração foi feita para melhorar o desempenho de gatilhos HTTP na versão 2. x e superior.

Na versão 1. x, os modelos de webhook fornecem validação adicional para cargas de webhook. Na versão 2. x e superior, o gatilho HTTP base ainda funciona e é a abordagem recomendada para WebHooks. 

#### <a name="github-webhooks"></a>WebHooks do GitHub

Para responder a WebHooks do GitHub, primeiro crie sua função com um gatilho HTTP e defina a propriedade **Webhooktype** como `github`. Em seguida, copie sua URL e a chave de API na página **Adicionar webhook** do seu repositório github. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Subganchos de margem de atraso

O webhook de margem de atraso gera um token para você em vez de permitir que você o especifique, portanto, você deve configurar uma chave específica de função com o token da margem de atraso. Consulte [chaves de autorização](#authorization-keys).

### <a name="webhooks-and-keys"></a>WebHooks e chaves

A autorização de webhook é tratada pelo componente receptor de webhook, parte do gatilho HTTP e o mecanismo varia de acordo com o tipo de webhook. Cada mecanismo depende de uma chave. Por padrão, a chave de função chamada "default" é usada. Para usar uma chave diferente, configure o provedor de webhook para enviar o nome da chave com a solicitação de uma das seguintes maneiras:

* **Cadeia de caracteres de consulta**: o provedor passa o nome da chave no parâmetro de cadeia de caracteres de consulta `clientid`, como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Cabeçalho da solicitação**: o provedor passa o nome da chave no cabeçalho `x-functions-clientid`.

## <a name="trigger---limits"></a>Gatilho-limites

O comprimento da solicitação HTTP é limitado a 100 MB (104.857.600 bytes) e o comprimento da URL é limitado a 4 KB (4.096 bytes). Esses limites são especificados pelo elemento `httpRuntime` do [arquivo Web. config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)do tempo de execução.

Se uma função que usa o gatilho HTTP não for concluída dentro de 230 segundos, o [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) atingirá o tempo limite e retornará um erro http 502. A função continuará em execução, mas não poderá retornar uma resposta HTTP. Para funções de execução longa, recomendamos que você siga os padrões assíncronos e retorne um local em que você possa executar o ping do status da solicitação. Para obter informações sobre quanto tempo uma função pode ser executada, consulte [escala e hospedagem – plano de consumo](functions-scale.md#timeout).

## <a name="output"></a>Saída

Use a associação de saída HTTP para responder ao remetente da solicitação HTTP. Esse enlace requer um acionador HTTP e permite-lhe personalizar a resposta associada ao pedido do acionador. Se uma associação de saída HTTP não for fornecida, um gatilho HTTP retornará HTTP 200 OK com um corpo vazio em Functions 1. x ou HTTP 204 sem conteúdo com um corpo vazio no functions 2. x e superior.

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definiu no arquivo *Function. JSON* . Para C# bibliotecas de classes, não há propriedades de atributo que correspondam a essas propriedades *Function. JSON* .

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |Tem de ser definido como `http`. |
| **direção** | Tem de ser definido como `out`. |
| **name** | O nome da variável usada no código de função para a resposta ou `$return` para usar o valor de retorno. |

## <a name="output---usage"></a>Saída - utilização

Para enviar uma resposta HTTP, use os padrões de resposta padrão de idioma. Em C# ou C# script, torne o tipo de retorno da função `IActionResult` ou `Task<IActionResult>`. No C#, um atributo de valor de retorno não é necessário.

Por exemplo, respostas, consulte o [exemplo de gatilho](#trigger---example).

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O arquivo host. JSON de exemplo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre definições de configuração global nas versões 2. x e posteriores, consulte [referência de host. JSON para Azure Functions](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
| customHeaders|nenhuma|Permite que você defina cabeçalhos personalizados na resposta HTTP. O exemplo anterior adiciona o cabeçalho `X-Content-Type-Options` à resposta para evitar a detecção de tipo de conteúdo. |
|dynamicThrottlesEnabled|verdadeiro<sup>\*</sup>|Quando habilitado, essa configuração faz com que o pipeline de processamento de solicitações Verifique periodicamente os contadores de desempenho do sistema como conexões/threads/processos/memória/CPU/etc. e, se qualquer um desses contadores estiver acima de um limite alto interno (80%), as solicitações serão rejeitadas com uma resposta 429 "muito ocupado" até que os contadores retornem aos níveis normais.<br/><sup>\*</sup> O padrão em um plano de consumo é `true`. O padrão em um plano dedicado é `false`.|
|hsts|não habilitado|Quando `isEnabled` é definido como `true`, o [comportamento de HSTS (segurança de transporte estrito http) do .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) é imposto, conforme definido na [classe`HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). O exemplo acima também define a propriedade [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) como 10 dias. As propriedades com suporte de `hsts` são: <table><tr><th>Propriedade</th><th>Descrição</th></tr><tr><td>excludedHosts</td><td>Uma matriz de cadeia de caracteres de nomes de host para a qual o cabeçalho HSTS não é adicionado.</td></tr><tr><td>includeSubDomains</td><td>Valor booliano que indica se o parâmetro includeSubDomain do cabeçalho Strict-Transport-Security está habilitado.</td></tr><tr><td>Período</td><td>Cadeia de caracteres que define o parâmetro Max-age do cabeçalho Strict-Transport-Security.</td></tr><tr><td>pré-carregamento</td><td>Booliano que indica se o parâmetro PreLoad do cabeçalho Strict-Transport-Security está habilitado.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|O número máximo de funções HTTP que são executadas em paralelo. Isso permite que você controle a simultaneidade, o que pode ajudar a gerenciar a utilização de recursos. Por exemplo, você pode ter uma função HTTP que usa muitos recursos do sistema (memória/CPU/soquetes), de modo que isso cause problemas quando a simultaneidade for muito alta. Ou você pode ter uma função que faça solicitações de saída para um serviço de terceiros, e essas chamadas precisam ser limitadas por taxa. Nesses casos, a aplicação de uma limitação aqui pode ajudar. <br/><sup>*</sup> O padrão para um plano de consumo é 100. O padrão para um plano dedicado é não associado (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|O número máximo de solicitações pendentes que são mantidas em um determinado momento. Esse limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Todas as solicitações de entrada acima desse limite são rejeitadas com uma resposta de 429 "muito ocupado". Isso permite que os chamadores empreguem estratégias de repetição baseadas em tempo, além de ajudar você a controlar as latências de solicitação máximas. Isso controla somente o enfileiramento que ocorre no caminho de execução do host de script. Outras filas, como a fila de solicitações ASP.NET, ainda estarão em vigor e não serão afetadas por essa configuração. <br/><sup>\*</sup> O padrão para um plano de consumo é 200. O padrão para um plano dedicado é não associado (`-1`).|
|routePrefix|api|O prefixo de rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |


## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
