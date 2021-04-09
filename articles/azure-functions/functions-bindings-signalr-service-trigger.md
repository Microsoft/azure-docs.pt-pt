---
title: Azure Functions SignalR Service binding
description: Aprenda a enviar mensagens de Serviço SignalR a partir de Funções Azure.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763523"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Serviço SignalR aciona a ligação para funções Azure

Utilize a ligação do gatilho *SignalR* para responder às mensagens enviadas pelo Serviço Azure SignalR. Quando a função é desencadeada, as mensagens transmitidas para a função são analisadas como um objeto json.

No modo sem servidor signalR Service, o Serviço SignalR utiliza a função [Upstream](../azure-signalr/concept-upstream.md) para enviar mensagens do cliente para a App de Função. E a App function usa a ligação do dispositivo SignalR para lidar com estas mensagens. A arquitetura geral é mostrada abaixo: :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="SignalR Trigger Architecture":::

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemplo

O exemplo a seguir mostra uma função que recebe uma mensagem utilizando a ligação do gatilho e regista a mensagem.

# <a name="c"></a>[C#](#tab/csharp)

A ligação do gatilho signalR service para C# tem dois modelos de programação. Modelo baseado em classe e modelo tradicional. O modelo baseado em classe pode fornecer uma experiência de programação consistente do lado do servidor SignalR. E o modelo tradicional proporciona mais flexibilidade e semelhante com outras ligações de funções.

### <a name="with-class-based-model"></a>Com modelo baseado em classe

Consulte [o modelo baseado em classe](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) para mais detalhes.

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Com modelo tradicional

O modelo tradicional obedece à convenção de Azure Function desenvolvida por C#. Se não está familiarizado com isso, pode aprender com [documentos.](./functions-dotnet-class-library.md)

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Use o atributo `[SignalRParameter]` para simplificar `ParameterNames`

Como é um pouco complicado de `ParameterNames` usar, `SignalRParameter` é fornecido para alcançar o mesmo propósito.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aqui estão os dados vinculativos do *function.jsem* arquivo:

Exemplo function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Aqui está o código do script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui estão os dados vinculativos do *function.jsem* arquivo:

Exemplo function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Aqui estão os dados vinculativos do *function.jsem* arquivo:

Exemplo function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Aqui está o código Python:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Configuração

### <a name="signalrtrigger"></a>SignalRTrigger

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `SignalRTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo**| n/a | Deve ser definido para `SignalRTrigger` .|
|**direção**| n/a | Deve ser definido para `in` .|
|**nome**| n/a | Nome variável usado no código de função para objeto de contexto de invocação do gatilho. |
|**nome hub**|**Nome Hub**| Este valor deve ser definido como o nome do hub SignalR para a função a ser ativada.|
|**categoria**|**Categoria**| Este valor deve ser definido como a categoria de mensagens para a função a ser ativada. A categoria pode ser um dos seguintes valores: <ul><li>**conexões**: Incluindo *eventos ligados* e *desligados*</li><li>**mensagens**: Incluindo todos os outros eventos, exceto os da categoria *de ligações*</li></ul> |
|**evento**|**Evento**| Este valor deve ser definido como o caso de mensagens para a função a ser ativada. Para a categoria *de mensagens,* o evento é o *alvo* na [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) que os clientes enviam. Para a categoria *de ligações, apenas* é utilizado *ligado* e *desligado.* |
|**parâmetroSNames**|**Parametrâneos**| (Opcional) Uma lista de nomes que se liga aos parâmetros. |
|**conexãoStringSetting**|**ConexãoStringSetting**| O nome da definição da aplicação que contém a cadeia de ligação SignalR Service (predefinição de "AzureSignalRConnectionString") |

## <a name="payload"></a>Carga útil

O tipo de entrada do gatilho é declarado como `InvocationContext` um ou outro tipo personalizado. Se `InvocationContext` escolher, terá acesso total ao conteúdo do pedido. Para um tipo personalizado, o tempo de execução tenta analisar o corpo de pedido JSON para definir as propriedades do objeto.

### <a name="invocationcontext"></a>InvocaçãoContexto

InvocationContext contém todo o conteúdo da mensagem enviada do Serviço SignalR.

|Propriedade em InvocationContext | Description|
|------------------------------|------------|
|Argumentos| Disponível para a categoria *mensagens.* Contém *argumentos* na [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Erro| Disponível para evento *desligado.* Pode ser esvaziar se a ligação estiver fechada sem erro, ou se contiver as mensagens de erro.|
|Hub| O nome central a que a mensagem pertence.|
|Categoria| A categoria da mensagem.|
|Evento| O evento da mensagem.|
|ConnectionId| A identificação de ligação do cliente que envia a mensagem.|
|IDUtilizador| A identidade do utilizador do cliente que envia a mensagem.|
|Cabeçalhos| Os cabeçalhos do pedido.|
|Consulta| A consulta do pedido quando os clientes se ligam ao serviço.|
|Afirmações| As reclamações do cliente.|

## <a name="using-parameternames"></a>Ao utilizar `ParameterNames`

A propriedade `ParameterNames` permite ligar argumentos de `SignalRTrigger` mensagens de invocação aos parâmetros das funções. O nome que definiu pode ser usado como parte de expressões de [encadernação noutras](../azure-functions/functions-bindings-expressions-patterns.md) ligações ou como parâmetros no seu código. Isso dá-lhe uma maneira mais conveniente de aceder a argumentos `InvocationContext` de.

Digamos que tem um cliente JavaScript SignalR a tentar invocar o método `broadcast` em Azure Function com dois `message1` argumentos, `message2` .

```javascript
await connection.invoke("broadcast", message1, message2);
```

Depois de `parameterNames` definir, o nome que definiu corresponderá respectivamente aos argumentos enviados do lado do cliente. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

Em seguida, o `arg1` will conterá o conteúdo de `message1` , e `arg2` conterá o conteúdo de `message2` .


### <a name="remarks"></a>Observações

Para a ligação do parâmetro, a ordem importa. Se estiver `ParameterNames` a utilizar, a encomenda corresponde `ParameterNames` à ordem dos argumentos invocados no cliente. Se estiver a utilizar o atributo `[SignalRParameter]` em C#, a ordem dos argumentos nos métodos Azure Function corresponde à ordem dos argumentos dos clientes.

`ParameterNames` e atributo `[SignalRParameter]` **não pode** ser usado ao mesmo tempo, ou você receberá uma exceção.

## <a name="signalr-service-integration"></a>Integração do Serviço SignalR

O Serviço SignalR precisa de um URL para aceder à App de função quando estiver a utilizar a ligação do dispositivo SignalR. O URL deve ser configurado em **Definições a montante** do lado do serviço SignalR. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="Portal a montante":::

Ao utilizar o gatilho do Serviço SignalR, o URL pode ser simples e formatado como mostrado abaixo:

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

O `Function_App_URL` pode ser encontrado na página geral da App da Função e o `API_KEY` gerado pela Função Azure. Pode obter a `API_KEY` lâmina de `signalr_extension` **teclas** de aplicação da App.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Chave API":::

Se pretender utilizar mais do que uma App de função juntamente com um Serviço SignalR, a montante também pode suportar regras complexas de encaminhamento. Encontre mais detalhes nas [definições de Upstream](../azure-signalr/concept-upstream.md).

## <a name="step-by-step-sample"></a>Amostra passo a passo

Pode seguir a amostra no GitHub para implementar uma sala de chat na App function com o serviço SignalR a ligar e a funcionalidade a montante: [Amostra de sala de chat bidirecional](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>Passos seguintes

* [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Amostra de ligação do gatilho do sinalr](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
