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
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 73fd388ad8d35543138c06b413cd40d7052806a7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735649"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Enlaces do Serviço SignalR para as Funções do Azure

Este artigo explica como autenticar e enviar mensagens em tempo real para clientes ligados ao [serviço Azure SignalR](https://azure.microsoft.com/services/signalr-service/) utilizando os enlaces de serviço SignalR nas funções do Azure. Funções do Azure suporta de entrada e saída enlaces para o serviço SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces de serviço SignalR são fornecidos na [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) pacote NuGet, 1.0.0-preview1-* de versão. Código-fonte para o pacote está no [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) repositório do GitHub.

> [!NOTE]
> Serviço Azure SignalR está disponível em geral. No entanto, os enlaces de serviço SignalR para as funções do Azure estão atualmente em pré-visualização.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Enlace de entrada de informações de ligação de SignalR

Antes de um cliente pode ligar ao serviço Azure SignalR, ele tem de obter o URL de ponto final de serviço e um token de acesso válido. O *SignalRConnectionInfo* enlace de entrada produz o URL de ponto final de serviço SignalR e um token válido, que são utilizadas para ligar ao serviço. Como o token funciona por tempo limitado e pode ser utilizado para autenticar um utilizador específico para uma ligação, não deve colocar em cache o token ou partilhá-lo entre os clientes. Um acionador HTTP com este enlace pode ser utilizado pelos clientes para obter as informações de ligação.

Veja o exemplo de idioma específico:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>exemplo de entrada 2.x c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que adquire informações de ligação do SignalR usando o enlace de entrada e retorna-o através de HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
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
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>exemplo de entrada de JavaScript 2.x

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
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
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
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Enlace de saída do SignalR

Utilize o *SignalR* enlace para enviar mensagens de um ou mais utilizando o serviço Azure SignalR de saída. Pode difundir uma mensagem para todos os clientes conectados, ou pode difundi-lo apenas para clientes ligados que tenham sido autenticados para um determinado utilizador.

Veja o exemplo de idioma específico:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>exemplo de saída 2.x c#

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
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>2.x exemplo de saída de JavaScript

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
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Enviar para um utilizador

Pode enviar uma mensagem apenas para ligações que tenham sido autenticadas para um utilizador ao definir o `userId` propriedade da mensagem SignalR.

*Function* permanece o mesmo. Eis o código JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
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

