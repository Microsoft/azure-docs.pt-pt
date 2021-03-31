---
title: Ligação de entrada de serviço signalr funções Azure
description: Aprenda a devolver um URL de serviço signalR e aceda a token em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763324"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Ligação de entrada de serviço SignalR para funções Azure

Antes de um cliente poder ligar-se ao Serviço Azure SignalR, deve recuperar o URL do ponto final de serviço e um token de acesso válido. A ligação de entrada *SignalRConnectionInfo* produz o URL do ponto final do Serviço SignalR e um token válido que é utilizado para ligar ao serviço. Como o token é limitado no tempo e pode ser usado para autenticar um utilizador específico numa ligação, não deve cache o token ou partilhá-lo entre os clientes. Um gatilho HTTP que utilize esta ligação pode ser utilizado pelos clientes para recuperar as informações de ligação.

Para obter mais informações sobre como esta ligação é usada para criar uma função de "negociação" que pode ser consumida por um cliente SignalR SDK, consulte o [artigo de desenvolvimento e configuração de Funções Azure](../azure-signalr/signalr-concept-serverless-development-config.md) na documentação de conceitos do Serviço SignalR.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adquire informações de ligação SignalR utilizando a ligação de entrada e a devolve através de HTTP.

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

O exemplo a seguir mostra uma ligação de informação signalR que liga numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação para devolver as informações de ligação.

Aqui estão os dados vinculativos do *function.jsem* arquivo:

Exemplo function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código do script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de informação signalR que liga uma *function.jsno* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação para devolver as informações de ligação.

Aqui estão os dados vinculativos do *function.jsem* arquivo:

Exemplo function.js:

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

O exemplo a seguir mostra uma ligação de informação signalR que liga uma *function.jsno* ficheiro e uma [função Python](functions-reference-python.md) que utiliza a ligação para devolver as informações de ligação.

Aqui estão os dados vinculativos do *function.jsem* arquivo:

Exemplo function.js:

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

O exemplo a seguir mostra uma [função Java](functions-reference-java.md) que adquire informações de ligação SignalR utilizando a ligação de entrada e a devolve através de HTTP.

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

Se a função for desencadeada por um cliente autenticado, pode adicionar uma reclamação de ID do utilizador ao token gerado. Pode adicionar facilmente a autenticação a uma aplicação de função utilizando [a Autenticação do Serviço de Aplicações.](../app-service/overview-authentication-authorization.md)

Conjuntos de autenticação de serviço de aplicações HTTP nomeados `x-ms-client-principal-id` e que contêm o `x-ms-client-principal-name` iD principal e nome do cliente autenticado, respectivamente.

# <a name="c"></a>[C#](#tab/csharp)

Pode definir a `UserId` propriedade da ligação para o valor de um dos cabeçalhos utilizando uma [expressão de encadernação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

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

Pode definir a `userId` propriedade da ligação para o valor de um dos cabeçalhos utilizando uma [expressão de encadernação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

Exemplo function.js:

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

Aqui está o código do script C#:

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

Pode definir a `userId` propriedade da ligação para o valor de um dos cabeçalhos utilizando uma [expressão de encadernação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

Exemplo function.js:

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

Pode definir a `userId` propriedade da ligação para o valor de um dos cabeçalhos utilizando uma [expressão de encadernação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

Exemplo function.js:

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
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Pode definir a `userId` propriedade da ligação para o valor de um dos cabeçalhos utilizando uma [expressão de encadernação](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}` .

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

- [Manuseie as mensagens do Serviço SignalR (ligação do gatilho)](./functions-bindings-signalr-service-trigger.md)
- [Enviar mensagens de serviço SignalR (ligação de saída)](./functions-bindings-signalr-service-output.md) 
