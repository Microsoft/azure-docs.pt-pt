---
title: Funções Azure HTTP gatilhos e encadernações
description: Compreenda como utilizar gatilhos e encadernações HTTP em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 25f3cb6f03e3a3c581d8977168e572554b5e0cc7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168112"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Funções Azure HTTP gatilhos e encadernações

Este artigo explica como trabalhar com gatilhos HTTP e encadernações de saída em Funções Azure.

Um gatilho HTTP pode ser personalizado para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

O código deste artigo incorre na sintaxe que utiliza o Núcleo .NET, utilizado nas Funções versão 2.x e superior. Para obter informações sobre a sintaxe 1.x, consulte os modelos de [funções 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As encadernações HTTP são fornecidas no [pacote Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet, versão 1.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

As encadernações HTTP são fornecidas no [pacote Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet, versão 3.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/)

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Acionador

O gatilho HTTP permite-lhe invocar uma função com um pedido HTTP. Pode utilizar um gatilho HTTP para construir APIs sem servidor e responder a webhooks.

Por predefinição, um gatilho HTTP devolve HTTP 200 OK com um corpo vazio nas Funções 1.x, ou HTTP 204 No Content com um corpo vazio nas Funções 2.x e superior. Para modificar a resposta, configure uma [ligação](#output)de saída HTTP .

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que procura um parâmetro `name`, quer na corda de consulta, quer no corpo do pedido HTTP. Note que o valor de retorno é usado para a encadernação de saída, mas não é necessário um atributo de valor de retorno.

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

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função procura um parâmetro `name` quer na corda de consulta quer no corpo do pedido HTTP.

Aqui está o ficheiro *função.json:*

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

A secção de [configuração](#trigger---configuration) explica estas propriedades.

Aqui está C# o código de script que se liga a `HttpRequest`:

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

Pode ligar-se a um objeto personalizado em vez de `HttpRequest`. Este objeto é criado a partir do corpo do pedido e analisado como JSON. Da mesma forma, um tipo pode ser passado para a ligação de saída de resposta HTTP e devolvido como o corpo de resposta, juntamente com um código de estado de 200.

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

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função procura um parâmetro `name` quer na corda de consulta quer no corpo do pedido HTTP.

Aqui está o ficheiro *função.json:*

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

A secção de [configuração](#trigger---configuration) explica estas propriedades.

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

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que utiliza a ligação. A função procura um parâmetro `name` quer na corda de consulta quer no corpo do pedido HTTP.

Aqui está o ficheiro *função.json:*

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

A secção de [configuração](#trigger---configuration) explica estas propriedades.

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

# <a name="javatabjava"></a>[Java](#tab/java)

* [Leia o parâmetro da corda de consulta](#read-parameter-from-the-query-string)
* [Ler corpo de um pedido post](#read-body-from-a-post-request)
* [Ler parâmetro de uma rota](#read-parameter-from-a-route)
* [Leia o corpo pojo de um pedido post](#read-pojo-body-from-a-post-request)

Os exemplos seguintes mostram a ligação do gatilho HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Leia o parâmetro da corda de consulta

Este exemplo lê um parâmetro, chamado ```id```, a partir da corda de consulta, e usa-o para construir um documento JSON devolvido ao cliente, com tipo de conteúdo ```application/json```. 

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

#### <a name="read-body-from-a-post-request"></a>Ler corpo de um pedido post

Este exemplo lê o corpo de um pedido post, como um ```String```, e usa-o para construir um documento JSON devolvido ao cliente, com o tipo de conteúdo ```application/json```.

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

Este exemplo lê um parâmetro obrigatório, chamado ```id```, e um parâmetro opcional ```name``` da rota, e usa-os para construir um documento JSON devolvido ao cliente, com tipo de conteúdo ```application/json```. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Leia o corpo pojo de um pedido post

Aqui está o código para a classe ```ToDoItem```, referenciado neste exemplo:

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

Este exemplo lê o corpo de um pedido do POST. O organismo de pedido é automaticamente desserializado num objeto ```ToDoItem```, e é devolvido ao cliente, com o tipo de conteúdo ```application/json```. O parâmetro ```ToDoItem``` é serializado pelo tempo de funcionamento das Funções, uma vez que é atribuído à propriedade ```body``` da classe ```HttpMessageResponse.Builder```.

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

Nas [ C# bibliotecas](functions-dotnet-class-library.md) de classes e em Java, o atributo `HttpTrigger` está disponível para configurar a função.

Pode definir o nível de autorização e métodos HTTP admissíveis em parâmetros de construção de atributos, tipo webhook e um modelo de rota. Para obter mais informações sobre estas definições, consulte [Trigger - configuração](#trigger---configuration).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Este exemplo demonstra como usar o atributo [HttpTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs)

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Para um exemplo completo, consulte o exemplo do [gatilho](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Este exemplo demonstra como usar o atributo [HttpTrigger.](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java)

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

Para um exemplo completo, consulte o exemplo do [gatilho](#trigger---example).

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `HttpTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Obrigatório - deve ser definido para `httpTrigger`. |
| **direção** | n/d| Obrigatório - deve ser definido para `in`. |
| **nome** | n/d| Obrigatório - o nome variável utilizado no código de função para o corpo de pedido ou pedido. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Determina quais as teclas, se houver, que devem estar presentes no pedido para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;não é necessária nenhuma chave API.</li><li><code>function</code>&mdash;é necessária uma chave API específica para a função. Este é o valor padrão se nenhum for fornecido.</li><li><code>admin</code>&mdash;É necessária a chave principal.</li></ul> Para mais informações, consulte a secção sobre [as chaves](#authorization-keys)de autorização . |
| **métodos** |**Métodos** | Uma série dos métodos HTTP aos quais a função responde. Se não especificada, a função responde a todos os métodos HTTP. Consulte [personalizar o ponto final http](#customize-the-http-endpoint). |
| **rota** | **Rota** | Define o modelo de rota, controlando a que solicitam URLs a sua função responde. O valor predefinido se nenhum for fornecido é `<functionname>`. Para mais informações, consulte [personalizar o ponto final http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Suportado apenas para o tempo de execução da versão 1.x._<br/><br/>Configures o gatilho HTTP para funcionar como um recetor [webhook](https://en.wikipedia.org/wiki/Webhook) para o fornecedor especificado. Não deprete a propriedade `methods` se você definir esta propriedade. O tipo webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto final de webhook de uso geral sem lógica para um fornecedor específico. Esta definição restringe os pedidos apenas a quem utiliza HTTP POST e com o tipo de conteúdo `application/json`.</li><li><code>github</code>&mdash;A função responde aos [webhooks GitHub](https://developer.github.com/webhooks/). Não utilize a propriedade _authLevel_ com webhooks GitHub. Para mais informações, consulte a secção de webhooks GitHub mais tarde neste artigo.</li><li><code>slack</code>&mdash;A função responde aos [webhooks Slack](https://api.slack.com/outgoing-webhooks). Não utilize a propriedade _authLevel_ com webhooks Slack. Para mais informações, consulte a secção Slack webhooks mais tarde neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Acionador - utilização

O tipo de entrada do gatilho é declarado como `HttpRequest` ou tipo personalizado. Se optar por `HttpRequest`, terá acesso total ao objeto de pedido. Para um tipo personalizado, o tempo de execução tenta analisar o corpo de pedido da JSON para definir as propriedades do objeto.

### <a name="customize-the-http-endpoint"></a>Personalize o ponto final http

Por predefinição quando cria uma função para um gatilho HTTP, a função é endereçada com uma rota do formulário:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Pode personalizar esta rota utilizando a propriedade opcional `route` na ligação de entrada do gatilho HTTP. Como exemplo, o seguinte ficheiro *função.json* define uma propriedade `route` para um gatilho HTTP:

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

Isto permite que o código de função suporte dois parâmetros no endereço, _categoria_ e _id_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pode utilizar qualquer restrição de [rota Web API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os seus parâmetros. O C# seguinte código de função utiliza ambos os parâmetros.

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

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Pode utilizar qualquer restrição de [rota Web API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os seus parâmetros. O C# seguinte código de função utiliza ambos os parâmetros.

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

No Nó, o tempo de funcionamento das funções fornece o corpo de pedido do `context` objeto. Para mais informações, consulte o exemplo do [gatilho javaScript](#trigger---example).

O exemplo que se segue mostra como ler os parâmetros da rota a partir de `context.bindingData`.

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

# <a name="pythontabpython"></a>[python](#tab/python)

O contexto de execução da função é exposto através de um parâmetro declarado como `func.HttpRequest`. Esta instância permite que uma função aceda a parâmetros de rota de dados, valores de cordas de consulta e métodos que lhe permitam devolver respostas HTTP.

Uma vez definidos, os parâmetros da rota estão disponíveis para a função, chamando o método `route_params`.

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

O contexto de execução da função é propriedades declaradas no atributo `HttpTrigger`. O atributo permite definir parâmetros de rota, níveis de autorização, verbos HTTP e a instância de pedido de entrada.

Os parâmetros da rota são definidos através do atributo `HttpTrigger`.

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

Por predefinição, todas as rotas de função são pré-fixadas com *api*. Também pode personalizar ou remover o prefixo utilizando a propriedade `http.routePrefix` no seu ficheiro [host.json.](functions-host-json.md) O exemplo seguinte remove o prefixo da rota *api* utilizando uma corda vazia para o prefixo no ficheiro *host.json.*

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="using-route-parameters"></a>Utilização de parâmetros de rota

Os parâmetros de rota que definem o padrão de `route` de uma função estão disponíveis para cada encadernação. Por exemplo, se tiver uma rota definida como `"route": "products/{id}"` então uma ligação de armazenamento de mesa pode usar o valor do parâmetro `{id}` na configuração de encadernação.

A configuração seguinte mostra como o parâmetro `{id}` é passado para o `rowKey`da ligação .

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


### <a name="working-with-client-identities"></a>Trabalhar com identidades de clientes

Se a sua aplicação de funções estiver a utilizar a [Autenticação /Autorização](../app-service/overview-authentication-authorization.md)do Serviço app, pode ver informações sobre clientes autenticados a partir do seu código. Esta informação está disponível como [cabeçalhos de pedido injetados pela plataforma](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Pode também ler esta informação a partir de dados vinculativos. Esta capacidade só está disponível para o tempo de funcionamento das Funções em 2.x e superior. Atualmente, apenas está disponível para línguas .NET.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

As informações relativas aos clientes autenticados estão disponíveis como Diretor de [Sinistros.](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) O ClaimsPrincipal está disponível como parte do contexto de pedido, como mostra o seguinte exemplo:

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

Alternativamente, o ClaimsPrincipal pode simplesmente ser incluído como um parâmetro adicional na assinatura da função:

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

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

As informações relativas aos clientes autenticados estão disponíveis como Diretor de [Sinistros.](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) O ClaimsPrincipal está disponível como parte do contexto de pedido, como mostra o seguinte exemplo:

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

Alternativamente, o ClaimsPrincipal pode simplesmente ser incluído como um parâmetro adicional na assinatura da função:

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

O utilizador autenticado está disponível através de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="pythontabpython"></a>[python](#tab/python)

O utilizador autenticado está disponível através de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="javatabjava"></a>[Java](#tab/java)

O utilizador autenticado está disponível através de [cabeçalhos HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

### <a name="authorization-keys"></a>Chaves de autorização

As funções permitem-lhe utilizar chaves para dificultar o acesso aos pontos finais da função HTTP durante o desenvolvimento.  Um gatilho HTTP padrão pode exigir que tal chave API esteja presente no pedido. 

> [!IMPORTANT]
> Embora as teclas possam ajudar a obfusizar os seus pontos finais HTTP durante o desenvolvimento, não se destinam a assegurar um gatilho HTTP na produção. Para saber mais, consulte [Secure um ponto final HTTP na produção.](#secure-an-http-endpoint-in-production)

> [!NOTE]
> Nas Funções 1.x, os fornecedores de webhook podem utilizar chaves para autorizar pedidos de várias maneiras, dependendo do que o fornecedor suporta. Isto está coberto por [Webhooks e chaves](#webhooks-and-keys). O tempo de funcionamento das Funções na versão 2.x e superior não inclui suporte incorporado para fornecedores de webhook.

Existem dois tipos de chaves:

* **Teclas do hospedeiro**: Estas teclas são partilhadas por todas as funções dentro da aplicação de função. Quando utilizados como uma chave API, estes permitem o acesso a qualquer função dentro da aplicação de função.
* **Teclas de função**: Estas teclas aplicam-se apenas às funções específicas sob as quais são definidas. Quando utilizados como chave API, estas apenas permitem o acesso a essa função.

Cada chave é nomeada para referência, e há uma chave padrão (chamada "padrão") na função e nível de anfitrião. As teclas de função têm precedência sobre as teclas do hospedeiro. Quando duas teclas são definidas com o mesmo nome, a chave de função é sempre utilizada.

Cada aplicação de função também tem uma **chave master**especial. Esta chave é uma chave de acolhimento chamada `_master`, que proporciona acesso administrativo às APIs em tempo de execução. Esta chave não pode ser revogada. Quando definir um nível de autorização de `admin`, os pedidos devem utilizar a chave principal; qualquer outro resultado chave na falha de autorização.

> [!CAUTION]  
> Devido às permissões elevadas na sua app de funções concedidas pela chave principal, não deve partilhar esta chave com terceiros ou distribuí-la em aplicações de clientes nativos. Tenha cuidado ao escolher o nível de autorização de administração.

### <a name="obtaining-keys"></a>Obtenção de chaves

As chaves são armazenadas como parte da sua aplicação de funções em Azure e são encriptadas em repouso. Para ver as suas teclas, crie novas, ou role chaves para novos valores, navegue para uma das suas funções desencadeadas pelo HTTP no [portal Azure](https://portal.azure.com) e selecione **Manage**.

![Gerencie as teclas de função no portal.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Pode obter chaves de função programáticamente utilizando APIs de [gestão chave](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Autorização chave DaPi

A maioria dos modelos de gatilho http requerem uma chave API no pedido. Assim, o seu pedido http normalmente se parece com o seguinte URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

A chave pode ser incluída numa variável de cadeia de consulta chamada `code`, como acima. Também pode ser incluído num cabeçalho `x-functions-key` HTTP. O valor da chave pode ser qualquer chave de função definida para a função, ou qualquer chave hospedeira.

Pode permitir pedidos anónimos, que não requerem chaves. Também pode exigir que a chave principal seja utilizada. Altera o nível de autorização por defeito utilizando a propriedade `authLevel` no JSON vinculativo. Para mais informações, consulte [Trigger - configuração](#trigger---configuration).

> [!NOTE]
> Quando o funcionamento funciona localmente, a autorização é desativada independentemente da definição de nível de autorização especificada. Depois de publicar no Azure, a configuração `authLevel` no gatilho é executada. As chaves ainda são necessárias quando se funciona [localmente num recipiente](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


### <a name="secure-an-http-endpoint-in-production"></a>Assegurar um ponto final http na produção

Para garantir totalmente os pontos finais da sua função em produção, deve considerar a implementação de uma das seguintes opções de segurança ao nível da aplicação:

* Ligue a autenticação do serviço de aplicações / Autorização para a sua aplicação de funções. A plataforma App Service permite-lhe utilizar o Azure Ative Directory (AAD) e vários fornecedores de identidade de terceiros para autenticar clientes. Pode usá-lo para implementar regras de autorização personalizadas para as suas funções, e pode trabalhar com informações do utilizador a partir do seu código de função. Para saber mais, consulte autenticação e autorização no Serviço de [Aplicações Azure](../app-service/overview-authentication-authorization.md) e [trabalhando com identidades de clientes.](#working-with-client-identities)

* Utilize a Azure API Management (APIM) para autenticar pedidos. A APIM fornece uma variedade de opções de segurança da API para pedidos de entrada. Para saber mais, consulte as políticas de [autenticação da API Management.](../api-management/api-management-authentication-policies.md) Com a APIM no lugar, pode configurar a sua aplicação de funções para aceitar pedidos apenas a partir do endereço IP da sua instância APIM. Para saber mais, consulte [as restrições de endereço IP](ip-addresses.md#ip-address-restrictions).

* Implemente a sua aplicação de funções para um Azure App Service Environment (ASE). A ASE proporciona um ambiente de hospedagem dedicado para executar as suas funções. A ASE permite configurar um único portal frontal que pode usar para autenticar todos os pedidos de entrada. Para mais informações, consulte configurar uma firewall de [aplicação web (WAF) para o ambiente](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)do serviço de aplicações .

Ao utilizar um destes métodos de segurança ao nível da aplicação de função, deverá definir o nível de autorização de função acionado pelo HTTP para `anonymous`.

### <a name="webhooks"></a>Webhooks

> [!NOTE]
> O modo Webhook só está disponível para a versão 1.x do tempo de execução das funções. Esta alteração foi feita para melhorar o desempenho dos gatilhos HTTP na versão 2.x e superior.

Na versão 1.x, os modelos de webhook fornecem validação adicional para cargas de webhook. Na versão 2.x e superior, o gatilho base HTTP ainda funciona e é a abordagem recomendada para webhooks. 

#### <a name="github-webhooks"></a>Webhooks GitHub

Para responder aos webhooks GitHub, crie primeiro a sua função com um Gatilho HTTP e detetete a propriedade **webHookType** para `github`. Em seguida, copie a sua url e a chave API na página **Add webhook** do seu repositório GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Ganchos de teia slack

O webhook Slack gera um símbolo para si em vez de o deixar especificar, por isso deve configurar uma chave específica da função com o símbolo de Slack. Ver [Chaves de Autorização](#authorization-keys).

### <a name="webhooks-and-keys"></a>Webhooks e chaves

A autorização do webhook é manuseada pelo componente recetor webhook, parte do gatilho HTTP, e o mecanismo varia com base no tipo webhook. Cada mecanismo depende de uma chave. Por predefinição, a tecla de função denominada "padrão" é utilizada. Para utilizar uma tecla diferente, configure o fornecedor de webhook para enviar o nome chave com o pedido de uma das seguintes formas:

* **Cadeia de consulta**: O fornecedor passa o nome-chave no parâmetro de corda de consulta `clientid`, como `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Cabeçalho de pedido**: O fornecedor passa o nome-chave no cabeçalho `x-functions-clientid`.

## <a name="trigger---limits"></a>Gatilho - limites

O comprimento do pedido HTTP é limitado a 100 MB (104.857.600 bytes), e o comprimento do URL é limitado a 4 KB (4.096 bytes). Estes limites são especificados pelo elemento `httpRuntime` do [ficheiro Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)do tempo de execução .

Se uma função que utiliza o gatilho HTTP não estiver concluída dentro de 230 segundos, o Balancer de [Carga Azure](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) irá esteriro e devolverá um erro HTTP 502. A função continuará a funcionar, mas não poderá devolver uma resposta HTTP. Para funções de longa duração, recomendamos que siga padrões de asincronização e devolva um local onde possa obter o estado do pedido. Para obter informações sobre quanto tempo uma função pode decorrer, consulte [Escala e hospedagem - Plano](functions-scale.md#timeout)de consumo .

## <a name="output"></a>Saída

Utilize a ligação de saída HTTP para responder ao remetente de pedidos HTTP. Esta ligação requer um gatilho HTTP e permite-lhe personalizar a resposta associada ao pedido do gatilho. Se não for fornecida uma ligação de saída HTTP, um gatilho HTTP devolve HTTP 200 OK com um corpo vazio nas Funções 1.x, ou HTTP 204 No Content com um corpo vazio nas Funções 2.x e superior.

## <a name="output---configuration"></a>Saída - configuração

A tabela que se segue explica as propriedades de configuração de ligação que definiu no ficheiro *função.json.* Para C# bibliotecas de classes, não existem propriedades de atributos que correspondam a estas *propriedades funções.json.*

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |deve ser definido para `http`. |
| **direção** | deve ser definido para `out`. |
| **nome** | O nome variável utilizado no código de função para a resposta, ou `$return` utilizar o valor de devolução. |

## <a name="output---usage"></a>Saída - utilização

Para enviar uma resposta HTTP, utilize os padrões de resposta padrão de linguagem. Dentro C# C# ou no script, faça o tipo de retorno da função `IActionResult` ou `Task<IActionResult>`. Não C#é necessário um atributo de valor de retorno.

Por exemplo, respostas, consulte o exemplo do [gatilho](#trigger---example).

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x e superiores. O ficheiro exemplo host.json abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais de configuração nas versões 2.x e além, consulte a [referência host.json para funções azure](functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md#http).

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
| personalizadoHeaders|nenhuma|Permite-lhe definir cabeçalhos personalizados na resposta HTTP. O exemplo anterior adiciona o cabeçalho `X-Content-Type-Options` à resposta para evitar cheirar o tipo de conteúdo. |
|dynamicThrottlesEnabled|verdadeiro<sup>\*</sup>|Quando ativada, esta definição faz com que o gasoduto de processamento de pedidos verifique periodicamente os contadores de desempenho do sistema, tais como ligações/fios/processos/memória/cpu/etc. e se algum desses contadores estiver acima de um limiar elevado incorporado (80%), os pedidos serão rejeitados com uma resposta de 429 "Demasiado Ocupado" até que o contador(s) volte aos níveis normais.<br/><sup>\*</sup> O padrão num plano de consumo é `true`. O padrão num plano dedicado é `false`.|
|hsts|não habilitado|Quando `isEnabled` está definida para `true`, o [comportamento http strict transport security (HSTS) de .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) é aplicado, conforme definido na classe [`HstsOptions`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). O exemplo acima também define a [propriedade`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) para 10 dias. As propriedades suportadas de `hsts` são: <table><tr><th>Propriedade</th><th>Descrição</th></tr><tr><td>anfitriões excluídos</td><td>Uma série de nomes de anfitriões para os quais o cabeçalho HSTS não é adicionado.</td></tr><tr><td>incluirSubDomínios</td><td>Valor booleano que indica se o parâmetro SubDomain incluído do cabeçalho De Segurança Estrita-Transporte está ativado.</td></tr><tr><td>maxAge</td><td>Corda que define o parâmetro de idade máxima do cabeçalho De Segurança De Transporte Rigoroso.</td></tr><tr><td>pré-carga</td><td>Boolean que indica se o parâmetro de pré-carga do cabeçalho de segurança de transporte rigoroso está ativado.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|O número máximo de funções HTTP que são executadas em paralelo. Isto permite controlar a conmoeda, o que pode ajudar a gerir a utilização de recursos. Por exemplo, pode ter uma função HTTP que utiliza muitos recursos do sistema (memória/cpu/tomadas) de tal forma que causa problemas quando a moeda é demasiado elevada. Ou pode ter uma função que faz pedidos de saída para um serviço de terceiros, e essas chamadas precisam de ser limitadas. Nestes casos, aplicar um acelerador aqui pode ajudar. <br/><sup>*</sup> O padrão para um plano de consumo é 100. O padrão para um plano dedicado é ilimitado (`-1`).|
|maxOutstandingRequests|200<sup>\*</sup>|O número máximo de pedidos pendentes que são realizados a qualquer momento. Este limite inclui pedidos que estão em fila mas que não começaram a executar, bem como quaisquer execuções em curso. Quaisquer pedidos de entrada acima deste limite são rejeitados com uma resposta 429 "Demasiado Ocupada". Isso permite que os chamadores empreguem estratégias de retry baseadas no tempo, e também o ajuda a controlar as lateências máximas de pedido. Isto apenas controla a fila que ocorre dentro do caminho de execução do hospedeiro script. Outras filas, como a fila de pedidos de ASP.NET, continuarão em vigor e não serão afetadas por este cenário. <br/><sup>\*</sup> O padrão para um plano de consumo é de 200. O padrão para um plano dedicado é ilimitado (`-1`).|
|routePrefix|api|O prefixo da rota que se aplica a todas as rotas. Utilize uma corda vazia para remover o prefixo predefinido. |


## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
