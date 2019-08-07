---
title: Azure Functions associações de serviço Signalr
description: Entenda como usar associações de serviço do Signalr com Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: b4622321dc25025eb2f7752755490eb5bc105069
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741790"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Enlaces do Serviço SignalR para as Funções do Azure

Este artigo explica como autenticar e enviar mensagens em tempo real para clientes conectados ao [serviço de signaler do Azure](https://azure.microsoft.com/services/signalr-service/) usando associações de serviço de sinalização no Azure functions. O Azure Functions dá suporte a associações de entrada e saída para o serviço Signalr.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

As associações de serviço Signalr são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) , versão 1. *. O código-fonte do pacote está no repositório GitHub [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Anotações de Java

Para usar as anotações do serviço Signalr em funções Java, você precisa adicionar uma dependência ao artefato do *Azure-Functions-Java-library-signalr* (versão 1,0 ou superior) para o pom. xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Para usar as associações de serviço do Signalr em Java, verifique se você está usando a versão 2.4.419 ou superior do Azure Functions Core Tools (versão do host 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Usando o serviço de sinalização com Azure Functions

Para obter detalhes sobre como configurar e usar o serviço de sinalização e Azure Functions juntos, consulte [Azure Functions desenvolvimento e configuração com o serviço de signaler do Azure](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Associação de entrada de informações de conexão do signalr

Antes que um cliente possa se conectar ao serviço de Signaler do Azure, ele deve recuperar a URL do ponto de extremidade de serviço e um token de acesso válido. A associação de entrada *SignalRConnectionInfo* produz a URL do ponto de extremidade do serviço signalr e um token válido que são usados para se conectar ao serviço. Como o token é limitado por tempo e pode ser usado para autenticar um usuário específico em uma conexão, você não deve armazenar em cache o token nem compartilhá-lo entre clientes. Um gatilho HTTP usando essa associação pode ser usado por clientes para recuperar as informações de conexão.

Veja o exemplo de idioma específico:

* [2.xC#](#2x-c-input-examples)
* [2. x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

Para obter mais informações sobre como essa associação é usada para criar uma função "Negotiate" que pode ser consumida por um SDK de cliente do Signalr, consulte o [artigo Azure Functions desenvolvimento e configuração](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação conceitos do serviço signalr.

### <a name="2x-c-input-examples"></a>exemplos de entrada C# 2. x

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

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

A autenticação do serviço de aplicativo define `x-ms-client-principal-id` os `x-ms-client-principal-name` cabeçalhos HTTP chamados e que contêm a ID e o nome da entidade de segurança do cliente autenticado, respectivamente. Você pode definir a `UserId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão](./functions-bindings-expressions-patterns.md)de associação `{headers.x-ms-client-principal-id}` : `{headers.x-ms-client-principal-name}`ou. 

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

### <a name="2x-javascript-input-examples"></a>2. x exemplos de entrada JavaScript

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

Eis o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

A autenticação do serviço de aplicativo define `x-ms-client-principal-id` os `x-ms-client-principal-name` cabeçalhos HTTP chamados e que contêm a ID e o nome da entidade de segurança do cliente autenticado, respectivamente. Você pode definir a `userId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão](./functions-bindings-expressions-patterns.md)de associação `{headers.x-ms-client-principal-id}` : `{headers.x-ms-client-principal-name}`ou. 

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

Eis o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2. x exemplos de entrada Java

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

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de funções usando a [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md).

A autenticação do serviço de aplicativo define `x-ms-client-principal-id` os `x-ms-client-principal-name` cabeçalhos HTTP chamados e que contêm a ID e o nome da entidade de segurança do cliente autenticado, respectivamente. Você pode definir a `UserId` propriedade da associação com o valor de um dos cabeçalhos usando uma [expressão](./functions-bindings-expressions-patterns.md)de associação `{headers.x-ms-client-principal-id}` : `{headers.x-ms-client-principal-name}`ou.

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

## <a name="signalr-output-binding"></a>Associação de saída do signalr

Use a Associação de saída do signalr para enviar uma ou mais mensagens usando o serviço de Signaler do Azure. Você pode transmitir uma mensagem para todos os clientes conectados ou pode transmiti-la somente para clientes conectados que foram autenticados para um determinado usuário.

Você também pode usá-lo para gerenciar os grupos aos quais um usuário pertence.

Veja o exemplo de idioma específico:

* [2.xC#](#2x-c-send-message-output-examples)
* [2. x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# exemplos de saída de mensagem de envio

#### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que envia uma mensagem usando a associação de saída para todos os clientes conectados. O `Target` é o nome do método a ser invocado em cada cliente. A `Arguments` propriedade é uma matriz de zero ou mais objetos a serem passados para o método de cliente.

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

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem somente para conexões que foram autenticadas para um usuário, definindo a `UserId` propriedade da mensagem do signalr.

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

#### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo, definindo a `GroupName` propriedade da mensagem do signalr.

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

### <a name="2x-c-group-management-output-examples"></a>exemplos de saída C# de gerenciamento de grupo 2. x

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a `SignalRGroupAction` classe com a `SignalR` Associação de saída para gerenciar a associação de grupo de um usuário.

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
> Para obter os `ClaimsPrincipal` limites corretos, você deve ter definido as configurações de autenticação no Azure functions.

### <a name="2x-javascript-send-message-output-examples"></a>2. x exemplos de saída de mensagem de envio JavaScript

#### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

O exemplo a seguir mostra uma associação de saída do Signalr em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação para enviar uma mensagem com o serviço de signaler do Azure. Defina a associação de saída para uma matriz de uma ou mais mensagens do Signalr. Uma mensagem de sinalização consiste em `target` uma propriedade que especifica o nome do método a ser invocado em cada cliente e `arguments` uma propriedade que é uma matriz de objetos a serem passados para o método de cliente como argumentos.

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

Eis o código JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem somente para conexões que foram autenticadas para um usuário, definindo a `userId` propriedade da mensagem do signalr.

*Function. JSON* permanece o mesmo. Eis o código JavaScript:

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

#### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo, definindo a `groupName` propriedade da mensagem do signalr.

*Function. JSON* permanece o mesmo. Eis o código JavaScript:

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

### <a name="2x-javascript-group-management-output-examples"></a>2. x exemplos de saída de gerenciamento de grupo JavaScript

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a `SignalR` Associação de saída para gerenciar a associação de grupo de um usuário.

#### <a name="add-user-to-a-group"></a>Adicionar usuário a um grupo

O exemplo a seguir adiciona um usuário a um grupo.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

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

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2. x exemplos de saída de mensagem de envio Java

#### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que envia uma mensagem usando a associação de saída para todos os clientes conectados. O `target` é o nome do método a ser invocado em cada cliente. A `arguments` propriedade é uma matriz de zero ou mais objetos a serem passados para o método de cliente.

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

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem somente para conexões que foram autenticadas para um usuário, definindo a `userId` propriedade da mensagem do signalr.

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

#### <a name="send-to-a-group"></a>Enviar para um grupo

Você pode enviar uma mensagem somente para conexões que foram adicionadas a um grupo, definindo a `groupName` propriedade da mensagem do signalr.

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

### <a name="2x-java-group-management-output-examples"></a>2. x exemplos de saída de gerenciamento de grupo Java

O serviço signalr permite que os usuários sejam adicionados a grupos. As mensagens podem ser enviadas a um grupo. Você pode usar a `SignalRGroupAction` classe com a `SignalROutput` Associação de saída para gerenciar a associação de grupo de um usuário.

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

## <a name="configuration"></a>Configuração

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `SignalRConnectionInfo` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type**|| Tem de ser definido como `signalRConnectionInfo`.|
|**direction**|| Tem de ser definido como `in`.|
|**name**|| Nome da variável usada no código de função para objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do Hub do Signalr para o qual as informações de conexão são geradas.|
|**userId**|**UserId**| Opcional: O valor da declaração do identificador de usuário a ser definido no token de chave de acesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do serviço Signalr (o padrão é "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `SignalR` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type**|| Tem de ser definido como `signalR`.|
|**direction**|| Tem de ser definido como `out`.|
|**name**|| Nome da variável usada no código de função para objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do Hub do Signalr para o qual as informações de conexão são geradas.|
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do serviço Signalr (o padrão é "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
