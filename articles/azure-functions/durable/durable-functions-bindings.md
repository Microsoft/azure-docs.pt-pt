---
title: Associações para o Durable Functions-Azure
description: Como usar gatilhos e associações para a extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbee98d64d37b2cdfc515eb733324902e238a768
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383098"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associações para Durable Functions (Azure Functions)

A extensão [Durable Functions](durable-functions-overview.md) introduz duas novas associações de gatilho que controlam a execução do orquestrador e funções de atividade. Ele também apresenta uma associação de saída que atua como um cliente para o tempo de execução de Durable Functions.

## <a name="orchestration-triggers"></a>Gatilhos de orquestração

O gatilho de orquestração permite que você crie funções de orquestrador duráveis. Esse gatilho dá suporte à inicialização de novas instâncias de função de orquestrador e à retomada das instâncias de função de orquestrador existentes que estão "aguardando" uma tarefa.

Quando você usa as ferramentas do Visual Studio para Azure Functions, o gatilho Orchestration é configurado usando o atributo .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .

Quando você escreve funções de orquestrador em linguagens de script (por exemplo, C# JavaScript ou scripts), o gatilho de orquestração é definido pelo seguinte objeto JSON na `bindings` matriz do arquivo *Function. JSON* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`é o nome da orquestração. Esse é o valor que os clientes devem usar quando desejarem iniciar novas instâncias dessa função de orquestrador. Esta propriedade é opcional. Se não for especificado, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão para o aplicativo de funções. Essas filas são detalhes de implementação interna da extensão, que é o motivo pelo qual elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas observações sobre o gatilho de orquestração:

* **Thread único** -um único thread de Dispatcher é usado para toda a execução da função de orquestrador em uma única instância de host. Por esse motivo, é importante garantir que o código da função de orquestrador seja eficiente e não execute nenhuma e/s. Também é importante garantir que esse thread não faça nenhum trabalho assíncrono, exceto ao aguardar em tipos de tarefa específicos de Durable Functions.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas em gatilhos de orquestração.
* **Visibilidade de mensagem** – as mensagens de gatilho de orquestração são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, contanto que o aplicativo de funções esteja em execução e íntegro.
* **Valores de retorno** – os valores de retorno são serializados para JSON e persistidos para a tabela de histórico de orquestração no armazenamento de tabelas do Azure. Esses valores de retorno podem ser consultados pela Associação de cliente de orquestração, descrita posteriormente.

> [!WARNING]
> Funções de orquestrador nunca devem usar nenhuma associação de entrada ou saída diferente da Associação de gatilho de orquestração. Fazer isso tem o potencial de causar problemas com a extensão de tarefa durável, pois essas associações podem não obedecer às regras de thread único e e/s. Se você quiser usar outras associações, adicione-as a uma função de atividade chamada de sua função de orquestrador.

> [!WARNING]
> As funções de orquestrador do JavaScript nunca `async`devem ser declaradas.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de orquestração dá suporte a entradas e saídas. Aqui estão algumas coisas que você precisa saber sobre o tratamento de entrada e saída:

* **entradas** – as funções de orquestração do .NET dão suporte apenas a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) como um tipo de parâmetro. Não há suporte para a desserialização de entradas diretamente na assinatura de função. O código deve usar o método [getinput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET `getInput` ) ou (JavaScript) para buscar entradas de função de orquestrador. Essas entradas devem ser tipos serializáveis em JSON.
* **saídas** – os gatilhos de orquestração dão suporte a valores de saída, bem como entradas. O valor de retorno da função é usado para atribuir o valor de saída e deve ser serializável por JSON. Se uma função do .NET `Task` retornar `void`ou, `null` um valor será salvo como a saída.

### <a name="trigger-sample"></a>Exemplo de gatilho

Veja a seguir um exemplo de como a função de orquestrador "Olá, Mundo" mais simples poderia ser:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O `context` objeto em JavaScript não representa o DurableOrchestrationContext, mas o [contexto de função como um todo.](../functions-reference-node.md#context-object) Você pode acessar os métodos de orquestração `context` por meio `df` da Propriedade do objeto.

> [!NOTE]
> Os orquestradores de JavaScript `return`devem usar o. A `durable-functions` biblioteca cuida da chamada do `context.done` método.

A maioria das funções de orquestrador chamam funções de atividade, portanto, aqui está um exemplo de "Olá, Mundo" que demonstra como chamar uma função de atividade:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Gatilhos de atividade

O gatilho de atividade permite que você crie funções que são chamadas por funções de orquestrador.

Se você estiver usando o Visual Studio, o gatilho de atividade será configurado usando o atributo .NET [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .

Se você estiver usando vs Code ou o portal do Azure para desenvolvimento, o gatilho de atividade será definido pelo seguinte objeto JSON na `bindings` matriz de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`é o nome da atividade. Esse é o valor que as funções de orquestrador usam para invocar essa função de atividade. Esta propriedade é opcional. Se não for especificado, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma fila na conta de armazenamento padrão para o aplicativo de funções. Essa fila é um detalhe de implementação interna da extensão, que é o motivo pelo qual ela não é configurada explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas observações sobre o gatilho de atividade:

* **Threading** – diferentemente do gatilho de orquestração, os gatilhos de atividade não têm restrições em relação ao Threading ou e/s. Eles podem ser tratados como funções regulares.
* **Manipulação de mensagens suspeitas** -não há suporte para mensagens suspeitas em gatilhos de atividade.
* **Visibilidade de mensagem** -as mensagens de gatilho de atividade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, contanto que o aplicativo de funções esteja em execução e íntegro.
* **Valores de retorno** – os valores de retorno são serializados para JSON e persistidos para a tabela de histórico de orquestração no armazenamento de tabelas do Azure.

> [!WARNING]
> O back-end de armazenamento para funções de atividade é um detalhe de implementação e o código do usuário não deve interagir com essas entidades de armazenamento diretamente.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de atividade dá suporte a entradas e saídas, assim como o gatilho de orquestração. Aqui estão algumas coisas que você precisa saber sobre o tratamento de entrada e saída:

* **entradas** – as funções de atividade do .NET usam nativamente o [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um tipo de parâmetro. Como alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que seja serializável por JSON. Ao usar `DurableActivityContext`o, você pode chamar [getinput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) para buscar e desserializar a entrada da função de atividade.
* **Outputs** -as funções de atividade dão suporte a valores de saída, bem como entradas. O valor de retorno da função é usado para atribuir o valor de saída e deve ser serializável por JSON. Se uma função do .NET `Task` retornar `void`ou, `null` um valor será salvo como a saída.
* **metadados** -as funções de atividade .net podem ser `string instanceId` associadas a um parâmetro para obter a ID da instância da orquestração pai.

### <a name="trigger-sample"></a>Exemplo de gatilho

Veja a seguir um exemplo de como uma função de atividade "Olá, Mundo" simples pode ser:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

O tipo de parâmetro padrão para a `ActivityTriggerAttribute` associação .NET `DurableActivityContext`é. No entanto, os gatilhos de atividade do .NET também dão suporte à associação diretamente a tipos serializados em JSON (incluindo tipos primitivos), portanto, a mesma função pode ser simplificada da seguinte maneira:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

As associações de JavaScript também podem ser passadas como parâmetros adicionais, portanto, a mesma função pode ser simplificada da seguinte maneira:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Passando vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. Nesse caso, a recomendação é passar uma matriz de objetos ou usar objetos [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) no .net.

O exemplo a seguir está usando novos recursos do [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) adicionados com [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

### <a name="using-input-and-output-bindings"></a>Usando associações de entrada e saída

Você pode usar associações de entrada e saída regulares, além da Associação de gatilho de atividade. Por exemplo, você pode pegar a entrada para sua associação de atividade e enviar uma mensagem para um EventHub usando a associação de saída do EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Cliente de orquestração

A associação de cliente de orquestração permite que você grave funções que interagem com funções de orquestrador. Por exemplo, você pode agir em instâncias de orquestração das seguintes maneiras:

* Inicie-os.
* Consultar seu status.
* Encerre-os.
* Enviar eventos para eles enquanto eles estiverem em execução.
* Limpar o histórico de instância.

Se você estiver usando o Visual Studio, poderá associar ao cliente Orchestration usando o atributo [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net.

Se você estiver usando linguagens de script (por exemplo, arquivos. *CSX* ou *. js* ) para desenvolvimento, o gatilho de orquestração será definido pelo seguinte `bindings` objeto JSON na matriz de *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`-Usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão `host.json` de será usado. Esse valor deve corresponder ao valor usado pelas funções de orquestrador de destino.
* `connectionName`-O nome de uma configuração de aplicativo que contém uma cadeia de conexão da conta de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de orquestrador de destino. Se não for especificado, a cadeia de conexão de conta de armazenamento padrão para o aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, é recomendável omitir essas propriedades e contar com o comportamento padrão.

### <a name="client-usage"></a>Uso do cliente

Em funções do .net, você normalmente se `DurableOrchestrationClient`associa a, que fornece acesso completo a todas as APIs de cliente com suporte do Durable functions. No JavaScript, as mesmas APIs são expostas `DurableOrchestrationClient` pelo objeto retornado `getClient`de. As APIs no objeto de cliente incluem:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (atualmente somente no .NET)

Como alternativa, as funções do .net podem `IAsyncCollector<T>` se `T` associar a Where `JObject`é [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) ou.

Consulte a documentação da API do [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) para obter detalhes adicionais sobre essas operações.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, será necessário definir a variável `WEBSITE_HOSTNAME` de ambiente como, por `localhost:<port>`exemplo, `localhost:7071`para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, consulte o [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Exemplo de cliente (desenvolvimento do Visual Studio)

Aqui está um exemplo de função disparada por fila que inicia uma orquestração "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (não Visual Studio)

Se você não estiver usando o Visual Studio para desenvolvimento, poderá criar o seguinte arquivo *Function. JSON* . Este exemplo mostra como configurar uma função disparada por fila que usa a associação de cliente de orquestração durável:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Veja a seguir exemplos específicos de idioma que iniciam novas instâncias de função de orquestrador.

#### <a name="c-sample"></a>Exemplo de c#

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de C# função de uma função de script:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Exemplo de JavaScript

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre como iniciar instâncias podem ser encontrados no [Gerenciamento de instâncias](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>definições de Host. JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os comportamentos de ponto de verificação e reprodução](durable-functions-checkpointing-and-replay.md)
