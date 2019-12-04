---
title: Azure Functions associações de serviço Signalr
description: Entenda como usar associações de serviço do Signalr com Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: a1de59ebb5ef0d7f5522a388aa9a2f5818495a9f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786338"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Enlaces do Serviço SignalR para as Funções do Azure

Este artigo explica como autenticar e enviar mensagens em tempo real para clientes conectados ao [serviço de signaler do Azure](https://azure.microsoft.com/services/signalr-service/) usando associações de serviço de sinalização no Azure functions. O Azure Functions dá suporte a associações de entrada e saída para o serviço Signalr.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes – funções 2. x

As associações de serviço Signalr são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) , versão 1. *. O código-fonte do pacote está no repositório GitHub [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

Para obter detalhes sobre como configurar e usar o serviço de sinalização e Azure Functions juntos, consulte [Azure Functions desenvolvimento e configuração com o serviço de signaler do Azure](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteca de anotações (somente Java)

Para usar as anotações do serviço Signalr em funções Java, você precisa adicionar uma dependência ao artefato do *Azure-Functions-Java-library-signalr* (versão 1,0 ou superior) para o pom. xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>Input

Antes que um cliente possa se conectar ao serviço de Signaler do Azure, ele deve recuperar a URL do ponto de extremidade de serviço e um token de acesso válido. A associação de entrada *SignalRConnectionInfo* produz a URL do ponto de extremidade do serviço signalr e um token válido que são usados para se conectar ao serviço. Como o token é limitado por tempo e pode ser usado para autenticar um usuário específico em uma conexão, você não deve armazenar em cache o token nem compartilhá-lo entre clientes. Um gatilho HTTP usando essa associação pode ser usado por clientes para recuperar as informações de conexão.

Para obter mais informações sobre como essa associação é usada para criar uma função "Negotiate" que pode ser consumida por um SDK de cliente do Signalr, consulte o [artigo Azure Functions desenvolvimento e configuração](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação conceitos do serviço signalr.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que adquire informações de conexão do signalr usando a associação de entrada e a retorna sobre http.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do signalr em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o C# código de script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do Signalr em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de entrada de informações de conexão do Signalr em um arquivo *Function. JSON* e uma [função do Python](functions-reference-python.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o código Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que adquire informações de conexão do signalr usando a associação de entrada e a retorna sobre http.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

A autenticação do serviço de aplicativo define cabeçalhos HTTP chamados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de serviço do usuário autenticado, respectivamente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Você pode definir a propriedade `UserId` da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Você pode definir a propriedade `userId` da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

Function. JSON de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o C# código de script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Você pode definir a propriedade `userId` da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

Function. JSON de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Você pode definir a propriedade `userId` da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

Function. JSON de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Este é o código Python:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Você pode definir a propriedade `userId` da associação com o valor de um dos cabeçalhos usando uma [expressão de associação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="output"></a>Saída

Use a associação de saída do *signalr* para enviar uma ou mais mensagens usando o serviço de Signaler do Azure. Você pode transmitir uma mensagem para todos os clientes conectados ou pode transmiti-la somente para clientes conectados que foram autenticados para um determinado usuário.

Você também pode usá-lo para gerenciar os grupos aos quais um usuário pertence.

### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

O exemplo a seguir mostra uma função que envia uma mensagem usando a associação de saída para todos os clientes conectados. O *destino* é o nome do método a ser invocado em cada cliente. *Argumentos* é uma matriz de zero ou mais objetos a serem passados para o método de cliente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o C# código de script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o código Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem somente para conexões que foram autenticadas para um usuário, definindo a *ID de usuário* na mensagem do signalr.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o C# código de script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o código Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo definindo o *nome do grupo* na mensagem do signalr.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o C# código de script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Este é o código Python:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="group-management"></a>Gerenciamento de grupo

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a associação de saída `SignalR` para gerenciar a associação de grupo de um usuário.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Para obter o `ClaimsPrincipal` corretamente associado, você deve ter definido as configurações de autenticação no Azure Functions.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

*Function. JSON* de exemplo

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

*Function. JSON* de exemplo

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run. CSX*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Para obter o `ClaimsPrincipal` corretamente associado, você deve ter definido as configurações de autenticação no Azure Functions.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

*Function. JSON* de exemplo

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

*Function. JSON* de exemplo

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index. js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

*Function. JSON* de exemplo

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

*Function. JSON* de exemplo

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Remover usuário de um grupo

O exemplo a seguir remove um usuário de um grupo.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Configuração

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `SignalRConnectionInfo`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**|| Deve ser definido como `signalRConnectionInfo`.|
|**direção**|| Deve ser definido como `in`.|
|**nomes**|| Nome da variável usada no código de função para objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do Hub do Signalr para o qual as informações de conexão são geradas.|
|**ID**|**ID**| Opcional: o valor da declaração do identificador de usuário a ser definido no token de chave de acesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do serviço Signalr (o padrão é "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `SignalR`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**|| Deve ser definido como `signalR`.|
|**direção**|| Deve ser definido como `out`.|
|**nomes**|| Nome da variável usada no código de função para objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do Hub do Signalr para o qual as informações de conexão são geradas.|
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do serviço Signalr (o padrão é "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os gatilhos e associações do Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
