---
title: Enlaces de funções duráveis - Azure
description: Como utilizar acionadores e enlaces para a extensão de funções duráveis para as funções do Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 678e370977cadae642207f91a02136404fb6c34e
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609324"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Enlaces de funções duráveis (funções do Azure)

O [funções duráveis](durable-functions-overview.md) extensão introduz duas novas associações de Acionador que controlam a execução das funções do orchestrator e atividade. Ele também introduz um enlace de saída que age como um cliente para o tempo de execução de funções duráveis.

## <a name="orchestration-triggers"></a>Acionadores de orquestração

O acionador de orquestração permite-lhe criar funções de orchestrator durável. Este acionador suporta iniciar novas instâncias de função do orchestrator e retomar a instâncias de função de orquestrador existentes que "aguardam" uma tarefa.

Ao utilizar as ferramentas do Visual Studio das funções do Azure, o acionador de orquestração é configurado com o [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) atributo .NET.

Quando escreve as funções do orchestrator em linguagens de scripts (por exemplo, JavaScript ou C# criação de scripts), o acionador de orquestração é definido pelo seguinte objeto JSON no `bindings` matriz do *Function* ficheiro:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` é o nome da orquestração. Este é o valor que os clientes têm de utilizar quando pretendem iniciar novas instâncias desta função de orquestrador. Esta propriedade é opcional. Se não for especificado, é utilizado o nome da função.

Internamente, este enlace de Acionador consulta uma série de filas na conta de armazenamento predefinida para a aplicação de funções. Essas filas são os detalhes de implementação interna da extensão, razão pela qual não estão explicitamente configurados nas propriedades de ligação.

### <a name="trigger-behavior"></a>Comportamento de Acionador

Seguem-se algumas notas sobre o acionador de orquestração:

* **Single-threading** -um thread dispatcher única é utilizado para a execução de função de orquestrador todos os numa instância de anfitrião único. Por esse motivo, é importante certificar-se de que o código de função do orchestrator é eficiente e não executa qualquer e/s. Também é importante certificar-se de que esse thread não faz qualquer trabalho assíncrono, exceto quando a aguardar em tipos de tarefas específicas de funções durável.
* **Processamento de mensagens de veneno** -não são suportadas mensagens não processáveis em acionadores de orquestração.
* **Visibilidade da mensagem** -as mensagens de Acionador de orquestração são removidos da fila e mantidas invisíveis durante um período configurável. A visibilidade destas mensagens é renovada automaticamente, desde que a aplicação de função está em execução e em bom estado.
* **Valores de retorno** -retornar valores são serializados para JSON e persistidas para a tabela de histórico de orquestração no armazenamento de tabelas do Azure. Este valores de retorno podem ser consultado pelo cliente de orquestração de ligação, descrito mais à frente.

> [!WARNING]
> As funções do Orchestrator nunca devem usar qualquer entrada ou saída enlaces que não seja a orquestração acionar a ligação. Se o fizer, tem o potencial de causar problemas com a extensão de tarefas durável, pois essas ligações podem não obedecem a single-threading e regras de e/s.

> [!WARNING]
> As funções do JavaScript orchestrator nunca devem ser declaradas `async`.

### <a name="trigger-usage-net"></a>Utilização do acionador (.NET)

O acionador de orquestração de enlace suporta entradas e saídas. Seguem-se alguns aspetos a saber sobre a entrada e saída de manipulação:

* **entradas** -as funções de orquestração de .NET suportam apenas [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) como um tipo de parâmetro. Não é suportada a desserialização de entradas diretamente na assinatura da função. Código tem de utilizar o [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) ou `getInput` método (JavaScript) para obtenção de entradas de função de orquestrador. Estas entradas tem de ser tipos serializáveis por JSON.
* **produz** -os acionadores de orquestração suportam valores de saída, bem como as entradas. O valor de retorno da função é utilizado para atribuir o valor de saída e tem de ser serializável JSON. Se uma função de .NET retorna `Task` ou `void`, um `null` valor será guardado como a saída.

### <a name="trigger-sample"></a>Exemplo de Acionador

Segue-se um exemplo de função de orquestrador de "Hello World" mais simples possível aparência:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O `context` objeto em JavaScript não representa o DurableOrchestrationContext, mas o [contexto de função como um todo.](../functions-reference-node.md#context-object). Pode acessar métodos de orquestração através da `context` do objeto `df` propriedade.

> [!NOTE]
> Devem utilizar JavaScript orquestradores `return`. O `durable-functions` biblioteca se encarrega de chamar o `context.done` método.

A maioria das funções do orchestrator chamam funções de atividade, então, aqui está um exemplo de "Hello World" que demonstra como chamar uma função de atividade:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Acionadores de atividade

O acionador de atividade permite-lhe criar funções que são chamados por funções do orchestrator.

Se estiver a utilizar o Visual Studio, o acionador de atividade é configurado com o [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) atributo .NET.

Se estiver a utilizar o VS Code ou o portal do Azure para desenvolvimento, o acionador de atividade é definido pelo seguinte objeto JSON no `bindings` matriz de *Function*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. Este é o valor que as funções do orchestrator usam para invocar esta função de atividade. Esta propriedade é opcional. Se não for especificado, é utilizado o nome da função.

Internamente, este enlace de Acionador consulta uma fila na conta de armazenamento padrão para a aplicação de funções. Esta fila é um detalhe de implementação interna da extensão, razão pela qual não é explicitamente configurado nas propriedades de ligação.

### <a name="trigger-behavior"></a>Comportamento de Acionador

Seguem-se algumas notas sobre o acionador de atividade:

* **Threading** – ao contrário do acionador de orquestração, acionadores de atividade não tem quaisquer restrições em torno de threading ou e/s. Eles podem ser tratados como funções regulares.
* **Processamento de mensagens de veneno** -não são suportadas mensagens não processáveis em acionadores de atividade.
* **Visibilidade da mensagem** -as mensagens de Acionador de atividade são removidos da fila e mantidas invisíveis durante um período configurável. A visibilidade destas mensagens é renovada automaticamente, desde que a aplicação de função está em execução e em bom estado.
* **Valores de retorno** -retornar valores são serializados para JSON e persistidas para a tabela de histórico de orquestração no armazenamento de tabelas do Azure.

> [!WARNING]
> O back-end de armazenamento para as funções de atividade é um detalhe de implementação e código do usuário não deve interagir com estas entidades de armazenamento diretamente.

### <a name="trigger-usage-net"></a>Utilização do acionador (.NET)

O acionador de atividade enlace suporta entradas e saídas, tal como o acionador de orquestração. Seguem-se alguns aspetos a saber sobre a entrada e saída de manipulação:

* **entradas** -as funções de atividade .NET utilizarem de forma nativa [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como um tipo de parâmetro. Em alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que é JSON serializável. Quando utiliza `DurableActivityContext`, pode chamar [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) buscar e anular a serialização a função de atividade de entrada.
* **produz** -as funções de atividade suportam valores de saída, bem como as entradas. O valor de retorno da função é utilizado para atribuir o valor de saída e tem de ser serializável JSON. Se uma função de .NET retorna `Task` ou `void`, um `null` valor será guardado como a saída.
* **Metadados** -as funções de atividade do .NET podem vincular a um `string instanceId` parâmetro para obter o ID de instância da orquestração principal.

### <a name="trigger-sample"></a>Exemplo de Acionador

Segue-se um exemplo de uma função de atividade de "Hello World" simples possível aparência:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

O tipo de parâmetro padrão para o .NET `ActivityTriggerAttribute` vinculação é `DurableActivityContext`. No entanto, os acionadores de atividade .NET também tipos de suporte de ligação diretamente para o JSON serializeable (incluindo tipos primitivos), para que a mesma função pode ser simplificada como segue:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Enlaces de JavaScript podem também ser passados como parâmetros adicionais, para que a mesma função pode ser simplificada da seguinte forma:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Passar vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. A recomendação neste caso, é passar uma matriz de objetos ou usar [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objetos no .NET.

O exemplo a seguir está a utilizar novas funcionalidades do [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) adicionado com [c# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="orchestration-client"></a>Cliente de orquestração

A ligação do cliente de orquestração permite-lhe escrever funções que interagem com as funções do orchestrator. Por exemplo, pode agir em instâncias de orquestração das seguintes formas:

* Iniciá-los.
* Consulte o respetivo estado.
* Finalizá-los.
* Envie eventos para eles, enquanto estiver em execução.
* Remover o histórico de instância.

Se estiver a utilizar o Visual Studio, pode ligar para o cliente de orquestração, utilizando o [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) atributo .NET.

Se estiver usando linguagens de script (por exemplo, *. csx* ou *. js* arquivos) para o desenvolvimento, o acionador de orquestração é definido pelo seguinte objeto JSON no `bindings` matriz de  *Function*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` -Utilizado em cenários em que várias aplicações de funções partilham a mesma conta de armazenamento, mas tem de ser isolados um do outro. Se não for especificado, o valor predefinido de `host.json` é utilizado. Este valor tem de corresponder ao valor utilizado pelas funções de orchestrator de destino.
* `connectionName` -O nome de uma definição de aplicação que contém uma cadeia de ligação de conta de armazenamento. A conta de armazenamento representada por esta cadeia de ligação tem de ser o mesmo usado pelas funções de orchestrator de destino. Se não for especificado, é utilizada a cadeia de ligação de conta de armazenamento padrão para a aplicação de funções.

> [!NOTE]
> Na maioria dos casos, recomendamos que omite essas propriedades e contar com o comportamento padrão.

### <a name="client-usage"></a>Utilização do cliente

Nas funções de .NET, geralmente liga para `DurableOrchestrationClient`, que lhe dá acesso total a todos os clientes APIs suportadas pelas funções durável. No JavaScript, as mesmas APIs expostas pela `DurableOrchestrationClient` objeto devolvido do `getClient`. APIs no objeto cliente incluem:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (atualmente apenas .NET)

Em alternativa, podem ligar as funções de .NET `IAsyncCollector<T>` em que `T` é [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) ou `JObject`.

Consulte a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) documentação da API para obter mais informações sobre estas operações.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, terá de definir a variável de ambiente `WEBSITE_HOSTNAME` para `localhost:<port>`, por ex. `localhost:7071` Para utilizar os métodos em `DurableOrchestrationClient`. Para obter mais informações sobre este requisito, consulte a [problema do GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Exemplo de cliente (desenvolvimento do Visual Studio)

Aqui está uma função de acionada por fila de exemplo que inicia uma orquestração de "HelloWorld".

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

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (não o Visual Studio)

Se não estiver a utilizar o Visual Studio para desenvolvimento, pode criar as seguintes *Function* ficheiro. Este exemplo mostra como configurar uma função de acionada por fila de mensagens em fila que utilize a ligação do cliente de orquestração durável:

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

Seguem-se exemplos de idioma específico que inicie novas instâncias de função de orquestrador.

#### <a name="c-sample"></a>Exemplo de c#

O exemplo a seguir mostra como utilizar o cliente de orquestração durável de ligação para iniciar uma nova instância de função a partir de uma função de script do c#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Exemplo de JavaScript

O exemplo a seguir mostra como utilizar a ligação para iniciar uma nova instância de função a partir de uma função de JavaScript do cliente de orquestração durável:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Podem encontrar mais detalhes sobre como iniciar instâncias na [gerenciamento de instância](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>definições de Host. JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os comportamentos de ponto de verificação e repetição](durable-functions-checkpointing-and-replay.md)