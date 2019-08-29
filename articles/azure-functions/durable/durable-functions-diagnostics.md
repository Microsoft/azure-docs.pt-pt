---
title: Diagnósticos no Durable Functions – Azure
description: Saiba como diagnosticar problemas com a extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2cc60ee2c73aa6858f68d6b13a895a0188bb5735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098140"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnósticos em Durable Functions no Azure

Há várias opções para diagnosticar problemas com [Durable Functions](durable-functions-overview.md). Algumas destas opções são iguais às das funções normais e outras são exclusivas do Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application insights](../../azure-monitor/app/app-insights-overview.md) é a maneira recomendada de fazer diagnósticos e monitoramento no Azure functions. O mesmo se aplica a Durable Functions. Para obter uma visão geral de como aproveitar Application Insights em seu aplicativo de funções, consulte [monitorar Azure Functions](../functions-monitoring.md).

A extensão durável Azure Functions também emite *eventos de rastreamento* que permitem rastrear a execução de ponta a ponta de uma orquestração. Eles podem ser encontrados e consultados usando a ferramenta de [análise de Application insights](../../azure-monitor/app/analytics.md) no portal do Azure.

### <a name="tracking-data"></a>Dados de rastreamento

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de rastreamento seja gravado na coleção de rastreamentos em Application insights. Esse evento contém uma carga **customDimensions** com vários campos.  Todos os nomes de campo são precedidos por `prop__`.

* **hubName**: O nome do hub de tarefas no qual suas orquestrações estão em execução.
* **appName**: O nome do aplicativo de funções. Isso é útil quando você tem vários aplicativos de função compartilhando a mesma instância de Application Insights.
* **slotName**: O [slot de implantação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) no qual o aplicativo de funções atual está em execução. Isso é útil quando você aproveita os slots de implantação para a versão de suas orquestrações.
* **functionName**: O nome da função de orquestrador ou de atividade.
* **functionType**: O tipo da função, como orquestrador ou **atividade**.
* **instanceId**: A ID exclusiva da instância de orquestração.
* **estado**: O estado de execução do ciclo de vida da instância. Valores válidos incluem:
  * **Agendado**: A função foi agendada para execução, mas ainda não começou a ser executada.
  * **Iniciado**em: A função começou a ser executada, mas ainda não foi aguardada ou concluída.
  * **Aguardado**: O orquestrador agendou algum trabalho e está aguardando sua conclusão.
  * **Ouvindo**: O orquestrador está ouvindo uma notificação de evento externo.
  * **Concluído**: A função foi concluída com êxito.
  * **Com falha**: A função falhou com um erro.
* **reason**: Dados adicionais associados ao evento de rastreamento. Por exemplo, se uma instância estiver aguardando uma notificação de evento externo, esse campo indicará o nome do evento que está aguardando. Se uma função tiver falhado, isso conterá os detalhes do erro.
* **isReplay**: Valor booliano que indica se o evento de rastreamento é para execução repetida.
* **extensionVersion**: A versão da extensão de tarefa durável. Esses são dados especialmente importantes ao relatar possíveis bugs na extensão. Instâncias de execução longa podem relatar várias versões se uma atualização ocorrer durante a execução.
* **sequenceNumber**: Número de sequência de execução de um evento. Combinado com o carimbo de data/hora ajuda a ordenar os eventos por tempo de execução. *Observe que esse número será redefinido como zero se o host for reiniciado enquanto a instância estiver em execução, portanto, é importante sempre classificar pelo carimbo de data/hora primeiro e, depois, sequenceNumber.*

O detalhamento dos dados de rastreamento emitidos para Application insights pode ser configurado `logger` na seção (funções 1. x `logging` ) ou (funções 2. `host.json` x) do arquivo.

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Funções 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Por padrão, todos os eventos de rastreamento sem reprodução são emitidos. O volume de dados pode ser reduzido com a `Host.Triggers.DurableTask` definição `"Warning"` de `"Error"` ou em que os eventos de controle de caso serão emitidos apenas para situações excepcionais.

Para habilitar a emissão dos eventos de reprodução de orquestração detalhada, `LogReplayEvents` o pode ser definido `true` como no `host.json` arquivo em `durableTask` , conforme mostrado:

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Funções 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Por padrão, a telemetria Application Insights é amostrada pelo tempo de execução do Azure Functions para evitar emitir dados com muita frequência. Isso pode fazer com que as informações de rastreamento sejam perdidas quando muitos eventos de ciclo de vida ocorrem em um curto período de tempo. O [artigo Azure Functions monitoramento](../functions-monitoring.md#configure-sampling) explica como configurar esse comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta a seguir mostra dados de controle de histórico para uma única instância da orquestração de função de [sequência](durable-functions-sequence.md) de saudação. Ele é escrito usando a [linguagem de consulta de Application insights (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Ele filtra a execução de reprodução para que apenas o caminho de execução *lógica* seja mostrado. Os eventos podem ser ordenados classificando `sequenceNumber` por `timestamp` e conforme mostrado na consulta abaixo:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

O resultado é uma lista de eventos de rastreamento que mostra o caminho de execução da orquestração, incluindo qualquer função de atividade ordenada pelo tempo de execução em ordem crescente.

![Consulta de Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consulta de resumo da instância

A consulta a seguir exibe o status de todas as instâncias de orquestração que foram executadas em um intervalo de tempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

O resultado é uma lista de IDs de instância e seu status de tempo de execução atual.

![Consulta de Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registo

É importante manter o comportamento de reprodução do orquestrador em mente ao gravar logs diretamente de uma função de orquestrador. Por exemplo, considere a seguinte função de orquestrador:

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Os dados de log resultantes serão semelhantes ao seguinte:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Lembre-se de que, enquanto os logs alegam chamar F1, F2 e F3, essas funções são *, na verdade* , chamadas apenas na primeira vez em que são encontradas. As chamadas subsequentes que acontecem durante a repetição são ignoradas e as saídas são reproduzidas para a lógica do orquestrador.

Se você quiser fazer logon somente na execução sem repetição, poderá gravar uma expressão condicional para registrar somente se `IsReplaying` for. `false` Considere o exemplo acima, mas desta vez com as verificações de repetição.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Com essa alteração, a saída do log é a seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Status personalizado

O status de orquestração personalizado permite definir um valor de status personalizado para a função de orquestrador. Esse status é fornecido por meio da API de consulta de status `DurableOrchestrationClient.GetStatusAsync` http ou da API. O status personalizado de orquestração permite um monitoramento mais rico para funções de orquestrador. Por exemplo, o código da função de orquestrador `DurableOrchestrationContext.SetCustomStatus` pode incluir chamadas para atualizar o progresso de uma operação de execução longa. Um cliente, como uma página da Web ou outro sistema externo, pode consultar periodicamente as APIs de consulta de status HTTP para obter informações mais detalhadas sobre o progresso. Um exemplo de `DurableOrchestrationContext.SetCustomStatus` uso é fornecido abaixo:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Enquanto a orquestração está em execução, os clientes externos podem buscar esse status personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Os clientes receberão a seguinte resposta:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> A carga de status personalizada é limitada a 16 KB de texto JSON UTF-16 porque ele precisa ser capaz de se ajustar em uma coluna de armazenamento de tabelas do Azure. Você pode usar o armazenamento externo se precisar de uma carga maior.

## <a name="debugging"></a>A depurar

O Azure Functions dá suporte diretamente ao código de função de depuração e esse mesmo suporte é enviado para Durable Functions, seja em execução no Azure ou localmente. No entanto, há alguns comportamentos que devem ser considerados durante a depuração:

* **Reprodução**: As funções do Orchestrator são reproduzidas regularmente quando novas entradas são recebidas. Isso significa que uma única execução *lógica* de uma função de orquestrador pode resultar em atingir o mesmo ponto de interrupção várias vezes, especialmente se ele estiver definido no início do código de função.
* **Aguardar**: Sempre que `await` um é encontrado, ele gera o controle de volta para o Dispatcher do Framework de tarefa durável. Se esta for a primeira vez que uma `await` determinada foi encontrada, a tarefa associada *nunca* será retomada. Como a tarefa nunca é retomada , a depuração do Await (F10 no Visual Studio) não é realmente possível. A depuração só funciona quando uma tarefa está sendo repetida.
* **Tempos limite de mensagens**: Durable Functions usa internamente mensagens de fila para acionar a execução de funções de orquestrador e funções de atividade. Em um ambiente de várias VMS, dividir a depuração por longos períodos de tempo pode fazer com que outra VM pegue a mensagem, resultando em execução duplicada. Esse comportamento existe para funções regulares de gatilho de fila também, mas é importante destacar nesse contexto, uma vez que as filas são um detalhe de implementação.

> [!TIP]
> Ao definir pontos de interrupção, se você quiser interromper apenas a execução sem repetição, poderá definir um ponto de interrupção condicional que só se `IsReplaying` encontra. `false`

## <a name="storage"></a>Armazenamento

Por padrão, o Durable Functions armazena o estado no armazenamento do Azure. Isso significa que você pode inspecionar o estado de suas orquestrações usando ferramentas como [Gerenciador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de tela Gerenciador de Armazenamento do Azure](./media/durable-functions-diagnostics/storage-explorer.png)

Isso é útil para depuração porque você vê exatamente em que estado uma orquestração pode estar. As mensagens nas filas também podem ser examinadas para saber qual trabalho está pendente (ou preso em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de tabelas, evite usar qualquer dependência nessa tabela. Ele pode mudar à medida que a extensão de Durable Functions evolui.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como usar temporizadores duráveis](durable-functions-timers.md)
