---
title: Ligação de entrada do serviço de sinalização de funções Azure
description: Aprenda a devolver um URL de ponto final do serviço SignalR e aceda ao acesso em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77530266"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Ligação de entrada do serviço SignalR para funções azure

Antes de um cliente poder ligar-se ao Serviço De Sinalização Azure, deve recuperar o URL final do ponto de serviço e um sinal de acesso válido. A ligação de entrada *SignalRConnectionInfo* produz o URL de ponto final do SignalR Service e um símbolo válido que é usado para ligar ao serviço. Como o símbolo é limitado no tempo e pode ser usado para autenticar um utilizador específico a uma ligação, não deve cache o símbolo ou partilhá-lo entre clientes. Um gatilho HTTP utilizando esta ligação pode ser usado pelos clientes para recuperar a informação de ligação.

Para obter mais informações sobre como esta ligação é usada para criar uma função de "negociar" que pode ser consumida por um cliente SignalR SDK, consulte o artigo de desenvolvimento e configuração de [Funções Azure](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação de conceitos do SignalR Service.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que adquire informações de ligação SignalR utilizando a ligação de entrada e a devolve em HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação de informação de ligação SignalR num ficheiro *function.json* e uma [função De Script C#](functions-reference-csharp.md) que utiliza a ligação para devolver as informações de ligação.

Aqui estão os dados vinculativos no ficheiro *fun.json:*

Função exemplo.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código c# script:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de informação de ligação SignalR num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação para devolver as informações de ligação.

Aqui estão os dados vinculativos no ficheiro *fun.json:*

Função exemplo.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo seguinte mostra uma ligação de informação de ligação SignalR num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que utiliza a ligação para devolver as informações de ligação.

Aqui estão os dados vinculativos no ficheiro *fun.json:*

Função exemplo.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código Python:

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

# <a name="java"></a>[Java](#tab/java)

O exemplo seguinte mostra uma [função Java](functions-reference-java.md) que adquire informações de ligação SignalR utilizando a ligação de entrada e a devolve em HTTP.

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

## <a name="authenticated-tokens"></a>Fichas autenticadas

Se a função for desencadeada por um cliente autenticado, pode adicionar uma reclamação de ID do utilizador ao token gerado. Pode facilmente adicionar a autenticação a uma aplicação de função utilizando a [Autenticação do Serviço app](../app-service/overview-authentication-authorization.md).

App Service Autenticação define cabeçalhos HTTP nomeados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm o ID e o nome principal do cliente autenticado do utilizador, respectivamente.

# <a name="c"></a>[C #](#tab/csharp)

Pode definir `UserId` a propriedade da ligação ao valor de `{headers.x-ms-client-principal-id}` um `{headers.x-ms-client-principal-name}`dos cabeçalhos utilizando uma [expressão vinculativa:](./functions-bindings-expressions-patterns.md)ou .

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Pode definir `userId` a propriedade da ligação ao valor de `{headers.x-ms-client-principal-id}` um `{headers.x-ms-client-principal-name}`dos cabeçalhos utilizando uma [expressão vinculativa:](./functions-bindings-expressions-patterns.md)ou .

Função exemplo.json:

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

Aqui está o código c# script:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Pode definir `userId` a propriedade da ligação ao valor de `{headers.x-ms-client-principal-id}` um `{headers.x-ms-client-principal-name}`dos cabeçalhos utilizando uma [expressão vinculativa:](./functions-bindings-expressions-patterns.md)ou .

Função exemplo.json:

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

Aqui está o código JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Pode definir `userId` a propriedade da ligação ao valor de `{headers.x-ms-client-principal-id}` um `{headers.x-ms-client-principal-name}`dos cabeçalhos utilizando uma [expressão vinculativa:](./functions-bindings-expressions-patterns.md)ou .

Função exemplo.json:

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

Aqui está o código Python:

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

# <a name="java"></a>[Java](#tab/java)

Pode definir `userId` a propriedade da ligação ao valor de `{headers.x-ms-client-principal-id}` um `{headers.x-ms-client-principal-name}`dos cabeçalhos utilizando uma [expressão vinculativa:](./functions-bindings-expressions-patterns.md)ou .

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

## <a name="next-steps"></a>Passos seguintes

- [Enviar mensagens de serviço SignalR (encadernação de saída)](./functions-bindings-signalr-service-output.md) 
