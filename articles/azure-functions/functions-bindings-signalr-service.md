---
title: Enlaces de serviço do SignalR de funções do Azure
description: Compreenda como utilizar os enlaces de serviço SignalR com as funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: bd59a9584f6993d768a9aeb790470a1d978c78ae
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542458"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Enlaces do Serviço SignalR para as Funções do Azure

Este artigo explica como autenticar e enviar mensagens em tempo real para clientes ligados ao [serviço Azure SignalR](https://azure.microsoft.com/services/signalr-service/) utilizando os enlaces de serviço SignalR nas funções do Azure. Funções do Azure suporta de entrada e saída enlaces para o serviço SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces de serviço SignalR são fornecidos na [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) pacote NuGet, 1. * de versão. Código-fonte para o pacote está no [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Anotações de Java

Para utilizar as anotações de serviço SignalR nas funções do Java, precisa adicionar uma dependência para o *azure-funções-java-library-signalr* artefacto (versão 1.0 ou superior) para sua pom. XML.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Para utilizar os enlaces de serviço SignalR em Java, certifique-se de que está a utilizar a versão 2.4.419 ou superior das ferramentas de núcleo de funções do Azure (versão de anfitrião 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Utilizar serviço SignalR com as funções do Azure

Para obter detalhes sobre como configurar e utilizar serviço SignalR e as funções do Azure em conjunto, consulte [desenvolvimento das funções do Azure e a configuração com o serviço Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Enlace de entrada de informações de ligação de SignalR

Antes de um cliente pode ligar ao serviço Azure SignalR, ele tem de obter o URL de ponto final de serviço e um token de acesso válido. O *SignalRConnectionInfo* enlace de entrada produz o URL de ponto final de serviço SignalR e um token válido, que são utilizadas para ligar ao serviço. Como o token funciona por tempo limitado e pode ser utilizado para autenticar um utilizador específico para uma ligação, não deve colocar em cache o token ou partilhá-lo entre os clientes. Um acionador HTTP com este enlace pode ser utilizado pelos clientes para obter as informações de ligação.

Veja o exemplo de idioma específico:

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

Para obter mais informações sobre como este enlace é usado para criar uma função "negotiate" que pode ser utilizada por um SDK de cliente do SignalR, consulte a [artigo de desenvolvimento e a configuração de funções do Azure](../azure-signalr/signalr-concept-serverless-development-config.md) os conceitos do serviço SignalR documentação.

### <a name="2x-c-input-examples"></a>2.x C# exemplos de entrada

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que adquire informações de ligação do SignalR usando o enlace de entrada e retorna-o através de HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Autenticado de tokens

Se a função é acionada por um cliente autenticado, pode adicionar uma afirmação de ID de utilizador para o token gerada. Pode adicionar facilmente autenticação para uma aplicação de função com [autenticação do serviço de aplicações] (... /App-Service/Overview-Authentication-Authorization.MD).

Autenticação do serviço de aplicações define os cabeçalhos HTTP com o nome `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm os ID de principal de cliente e o nome, o utilizador autenticado, respetivamente. Pode definir o `UserId` propriedade da ligação para o valor do cabeçalho usando um [expressão de ligação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>Exemplos de entrada de JavaScript 2.x

O exemplo seguinte mostra um SignalR ligação informações enlace de entrada num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que usa a vinculação para retornar as informações de ligação.

Eis a vinculação de dados *Function* ficheiro:

Function de exemplo:

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

#### <a name="authenticated-tokens"></a>Autenticado de tokens

Se a função é acionada por um cliente autenticado, pode adicionar uma afirmação de ID de utilizador para o token gerada. Pode adicionar facilmente autenticação para uma aplicação de função com [autenticação do serviço de aplicações] (... /App-Service/Overview-Authentication-Authorization.MD).

Autenticação do serviço de aplicações define os cabeçalhos HTTP com o nome `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm os ID de principal de cliente e o nome, o utilizador autenticado, respetivamente. Pode definir o `userId` propriedade da ligação para o valor do cabeçalho usando um [expressão de ligação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

Function de exemplo:

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

### <a name="2x-java-input-examples"></a>Exemplos de entrada de Java 2.x

A exemplo a seguir mostra um [função de Java](functions-reference-java.md) que adquire informações de ligação do SignalR usando o enlace de entrada e retorna-o através de HTTP.

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

#### <a name="authenticated-tokens"></a>Autenticado de tokens

Se a função é acionada por um cliente autenticado, pode adicionar uma afirmação de ID de utilizador para o token gerada. Pode adicionar facilmente autenticação para uma aplicação de função com [autenticação do serviço de aplicações] (... /App-Service/Overview-Authentication-Authorization.MD).

Autenticação do serviço de aplicações define os cabeçalhos HTTP com o nome `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm os ID de principal de cliente e o nome, o utilizador autenticado, respetivamente. Pode definir o `UserId` propriedade da ligação para o valor do cabeçalho usando um [expressão de ligação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>Enlace de saída do SignalR

Utilize o *SignalR* enlace para enviar mensagens de um ou mais utilizando o serviço Azure SignalR de saída. Pode difundir uma mensagem para todos os clientes conectados, ou pode difundi-lo apenas para clientes ligados que tenham sido autenticados para um determinado utilizador.

Também pode utilizar para gerir os grupos a que pertence um utilizador.

Veja o exemplo de idioma específico:

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# enviar exemplos de saída da mensagem

#### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que envia uma mensagem usando o enlace de saída para todos os clientes conectados. O `Target` é o nome do método a ser invocada em cada cliente. O `Arguments` propriedade é uma matriz de zero ou mais objetos a serem passados para o método de cliente.

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

#### <a name="send-to-a-user"></a>Enviar para um utilizador

Pode enviar uma mensagem apenas para ligações que tenham sido autenticadas para um utilizador ao definir o `UserId` propriedade da mensagem SignalR.

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

Pode enviar uma mensagem apenas para ligações que foram adicionadas a um grupo definindo o `GroupName` propriedade da mensagem SignalR.

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
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2.x C# exemplos de saída de gestão de grupo

Serviço SignalR permite aos utilizadores adicionados a grupos. Em seguida, podem ser enviadas mensagens a um grupo. Pode utilizar o `SignalRGroupAction` classe com o `SignalR` enlace para gerir a associação de grupo de um utilizador de saída.

#### <a name="add-user-to-a-group"></a>Adicionar o utilizador a um grupo

O exemplo seguinte adiciona um utilizador a um grupo.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Remover utilizador de um grupo

O exemplo seguinte remove um utilizador a partir de um grupo.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>Exemplos de saída de mensagem de envio de JavaScript 2.x

#### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

O exemplo seguinte mostra uma saída de SignalR ligando uma *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace para enviar uma mensagem com o serviço Azure SignalR. Defina o enlace de saída para uma matriz de uma ou mais mensagens de SignalR. Uma mensagem de SignalR é composta por um `target` propriedade que especifica o nome do método a invocar em cada cliente, e um `arguments` propriedade que é uma matriz de objetos para passar para o método de cliente como argumentos.

Eis a vinculação de dados *Function* ficheiro:

Function de exemplo:

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

#### <a name="send-to-a-user"></a>Enviar para um utilizador

Pode enviar uma mensagem apenas para ligações que tenham sido autenticadas para um utilizador ao definir o `userId` propriedade da mensagem SignalR.

*Function* permanece o mesmo. Eis o código JavaScript:

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

Pode enviar uma mensagem apenas para ligações que foram adicionadas a um grupo definindo o `groupName` propriedade da mensagem SignalR.

*Function* permanece o mesmo. Eis o código JavaScript:

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

### <a name="2x-javascript-group-management-output-examples"></a>Exemplos de saída 2.x gestão de grupos de JavaScript

Serviço SignalR permite aos utilizadores adicionados a grupos. Em seguida, podem ser enviadas mensagens a um grupo. Pode utilizar o `SignalR` enlace para gerir a associação de grupo de um utilizador de saída.

#### <a name="add-user-to-a-group"></a>Adicionar o utilizador a um grupo

O exemplo seguinte adiciona um utilizador a um grupo.

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

#### <a name="remove-user-from-a-group"></a>Remover utilizador de um grupo

O exemplo seguinte remove um utilizador a partir de um grupo.

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

### <a name="2x-java-send-message-output-examples"></a>Exemplos de saída do 2.x Java enviar mensagem

#### <a name="broadcast-to-all-clients"></a>Difusão para todos os clientes

A exemplo a seguir mostra um [função de Java](functions-reference-java.md) que envia uma mensagem usando o enlace de saída para todos os clientes conectados. O `target` é o nome do método a ser invocada em cada cliente. O `arguments` propriedade é uma matriz de zero ou mais objetos a serem passados para o método de cliente.

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

#### <a name="send-to-a-user"></a>Enviar para um utilizador

Pode enviar uma mensagem apenas para ligações que tenham sido autenticadas para um utilizador ao definir o `userId` propriedade da mensagem SignalR.

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

Pode enviar uma mensagem apenas para ligações que foram adicionadas a um grupo definindo o `groupName` propriedade da mensagem SignalR.

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

### <a name="2x-java-group-management-output-examples"></a>Exemplos de saída 2.x gestão de grupos de Java

Serviço SignalR permite aos utilizadores adicionados a grupos. Em seguida, podem ser enviadas mensagens a um grupo. Pode utilizar o `SignalRGroupAction` classe com o `SignalROutput` enlace para gerir a associação de grupo de um utilizador de saída.

#### <a name="add-user-to-a-group"></a>Adicionar o utilizador a um grupo

O exemplo seguinte adiciona um utilizador a um grupo.

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

#### <a name="remove-user-from-a-group"></a>Remover utilizador de um grupo

O exemplo seguinte remove um utilizador a partir de um grupo.

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
|**tipo**|| Tem de ser definido como `signalRConnectionInfo`.|
|**direção**|| Tem de ser definido como `in`.|
|**name**|| Nome da variável no código de função para o objeto de informações de ligação. |
|**hubName**|**HubName**| Este valor tem de ser definido para o nome do hub do SignalR para que as informações de ligação são geradas.|
|**userId**|**UserId**| Opcional: O valor do identificador de utilizador de afirmação definir o token de chave de acesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da definição de aplicação que contém a cadeia de ligação de serviço SignalR (a predefinição é "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `SignalR` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**|| Tem de ser definido como `signalR`.|
|**direção**|| Tem de ser definido como `out`.|
|**name**|| Nome da variável no código de função para o objeto de informações de ligação. |
|**hubName**|**HubName**| Este valor tem de ser definido para o nome do hub do SignalR para que as informações de ligação são geradas.|
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da definição de aplicação que contém a cadeia de ligação de serviço SignalR (a predefinição é "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Desenvolvimento das funções do Azure e de configuração com o serviço Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md)
