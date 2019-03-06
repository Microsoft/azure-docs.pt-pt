---
title: Diagnósticos no funções duráveis - Azure
description: Saiba como diagnosticar problemas com a extensão de funções duráveis para as funções do Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ac9abaaea7f33627332a9bc7563745b5efdf3d12
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436253"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnósticos no funções duráveis no Azure

Existem várias opções para diagnosticar problemas com [funções duráveis](durable-functions-overview.md). Algumas destas opções são iguais às das funções normais e outras são exclusivas do Durable Functions.

## <a name="application-insights"></a>Application Insights

[O Application Insights](../../azure-monitor/app/app-insights-overview.md) é a forma recomendada para fazer o diagnóstico e monitorização nas funções do Azure. O mesmo se aplica às funções durável. Para uma descrição geral de como tirar partido do Application Insights na sua aplicação de funções, consulte [as funções do Azure de Monitor](../functions-monitoring.md).

A extensão de durável de funções do Azure também emite *acompanhar eventos* que permitem-lhe rastrear a execução de ponto-a-ponto de uma orquestração. Estes podem ser encontrados e consultados usando o [Application Insights Analytics](../../azure-monitor/app/analytics.md) ferramenta no portal do Azure.

### <a name="tracking-data"></a>Dados de controlo

Cada evento de ciclo de vida de uma instância da orquestração faz com que um evento de controlo de escrita a **rastreios** coleção no Application Insights. Este evento contém um **customDimensions** payload com vários campos.  Os nomes de campos são anexados ao `prop__`.

* **hubName**: O nome do hub de tarefa executam suas orquestrações.
* **appName**: O nome da aplicação de função. Isto é útil quando tem várias aplicações de funções que partilha a mesma instância do Application Insights.
* **slotName**: O [bloco de implementação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) no qual a aplicação de função atual está em execução. Isto é útil quando utilizar blocos de implementação para a versão suas orquestrações.
* **functionName**: O nome da função de orquestrador ou atividade.
* **functionType**: O tipo de função, como **Orchestrator** ou **atividade**.
* **instanceId**: O ID exclusivo da instância de orquestração.
* **state**: O estado de execução do ciclo de vida da instância. Valores válidos incluem:
  * **Agendado**: A função foi agendada para execução, mas ainda não começou ainda em execução.
  * **Iniciado**: A função foi iniciada em execução, mas não tem ainda aguardada ou concluída.
  * **Aguardada**: O orchestrator tem agendada algum trabalho e está a aguardar conclusão da mesma.
  * **A escutar**: O orchestrator está à escuta de uma notificação de evento externo.
  * **Concluído**: A função foi concluída com êxito.
  * **Falha ao**: A função falhou com um erro.
* **reason**: Dados adicionais associados com o evento de controlo. Por exemplo, se uma instância está a aguardar uma notificação de evento externo, este campo indica o nome do evento que está a aguardar. Se uma função falhou, isso irá conter os detalhes do erro.
* **isReplay**: Valor booleano que indica se o evento de controlo é para execução repetida.
* **extensionVersion**: A versão da extensão de tarefas durável. São dados especialmente importantes quando possíveis erros na extensão de geração de relatórios. Instâncias de execução longa podem comunicar várias versões, se uma atualização ocorre durante a execução.
* **sequenceNumber**: Número de sequência de execução de um evento. Combinado com a ajuda de timestamp a ordenar os eventos por tempo de execução. *Observe que esse número será repor para zero se o anfitrião reinicia enquanto a instância está em execução, pelo que é importante sempre ordenar por timestamp pela primeira vez, em seguida, sequenceNumber.*

A verbosidade dos dados emitidos para o Application Insights de rastreamento pode ser configurada no `logger` (funções 1.x) ou `logging` (funções 2.x) seção o `host.json` ficheiro.

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

Por predefinição, são emitidos eventos de rastreio de todos os não-repetição. O volume de dados pode ser reduzido através da definição `Host.Triggers.DurableTask` para `"Warning"` ou `"Error"` caso em que eventos de controlo será apenas será emitido para situações excecionais.

Para ativar a emitir os eventos de repetição de orquestração verboso, o `LogReplayEvents` pode ser definido como `true` no `host.json` do ficheiro em `durableTask` conforme mostrado:

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
> Por predefinição, a telemetria do Application Insights é amostragem pelo tempo de execução das funções do Azure para evitar a emissão de dados com demasiada frequência. Isso pode fazer com que as informações de controlo ser perdidas quando muitos eventos de ciclo de vida ocorrem num curto período de tempo. O [artigo de monitorização de funções do Azure](../functions-monitoring.md#configure-sampling) explica como configurar esse comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta seguinte mostra os dados de histórico de controlo de uma única instância do [Hello sequência](durable-functions-sequence.md) orquestração de função. Ele é escrito com o [linguagem de consulta de informações do aplicativo (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Ele filtra a execução de repetição para que apenas os *lógico* é mostrado o caminho de execução. Eventos são ordenados por ordenar por `timestamp` e `sequenceNumber` conforme mostrado na consulta abaixo:

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

O resultado é uma lista de eventos de controlo que mostra o caminho de execução da orquestração, incluindo quaisquer funções de atividade, ordenadas pelo tempo de execução por ordem ascendente.

![Consulta do Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Consulta de resumida de instância

A consulta seguinte apresenta o estado de todas as instâncias de orquestração que foram executados num intervalo de tempo especificado.

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

O resultado é uma lista de IDs de instância e o respetivo estado atual do tempo de execução.

![Consulta do Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registo

É importante manter o orquestrador de comportamento de repetição em mente ao escrever registos diretamente a partir de uma função de orquestrador. Por exemplo, considere a seguinte função do orchestrator:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Os dados de registo resultante será algo parecido com o seguinte:

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
> Lembre-se de que embora os registos de afirmação chamar F1, F2 e F3, essas funções são apenas *, na verdade,* chamado na primeira vez que eles são encontrados. As chamadas subseqüentes que ocorrem durante a reprodução são ignoradas e as saídas são reproduzidas na lógica do orchestrator.

Se quiser fazer logon na execução de não repetição, pode escrever uma expressão condicional para o registo apenas se for `IsReplaying` é `false`. Considere o exemplo acima, mas desta vez com verificações de repetição.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Com esta alteração, a saída de registo é o seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Estado personalizado

Estado de orquestração personalizado permite-lhe definir um valor de estado personalizado para sua função de orquestrador. Este estado é fornecido por meio da consulta de estado HTTP API ou o `DurableOrchestrationClient.GetStatusAsync` API. O estado de orquestração personalizado permite o monitoramento mais avançado para as funções do orchestrator. Por exemplo, pode incluir o código de função de orquestrador `DurableOrchestrationContext.SetCustomStatus` chamadas para atualizar o progresso de uma operação de longa execução. Um cliente, como uma página da web ou outro sistema externo, em seguida, poderia consultar periodicamente a consulta de estado HTTP APIs para informações mais detalhadas de progresso. Um exemplo ao utilizar `DurableOrchestrationContext.SetCustomStatus` é apresentada abaixo:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

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

Durante a execução de orquestração, clientes externos podem obter este estado personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Os clientes obterá a seguinte resposta:

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
> O payload de estado personalizado está limitado a 16 KB de texto JSON de UTF-16, uma vez que ele precisa ser capaz de se ajustar numa coluna de armazenamento de tabelas do Azure. Pode utilizar o armazenamento externo se precisar de payload maior.

## <a name="debugging"></a>Depurar

Funções do Azure suporta a depuração de código de função diretamente e esse mesmo suporte são seguidos para funções duráveis, quer em execução no Azure ou localmente. No entanto, existem alguns comportamentos estar atento ao depurar:

* **Repetição**: As funções do Orchestrator regularmente reproduzir quando novas entradas são recebidas. Isso significa que uma única *lógico* execução de uma função de orquestrador pode resultar em acessando o mesmo ponto de interrupção várias vezes, especialmente se estiver definido no início do código de função.
* **Await**: Sempre que um `await` é encontrado, ele produz o controlo novamente para o dispatcher do Framework de tarefa durável. Se esta for a primeira vez um determinado `await` tiver sido encontrado, a tarefa associada é *nunca* retomado. Porque nunca retoma a tarefa, etapa *ao longo do* await (F10 no Visual Studio) não é realmente possível. Etapa sobre só funciona quando uma tarefa está a ser reproduzida.
* **Tempos limite de mensagens**: Funções duráveis internamente usa mensagens na fila para acionar a execução de funções do orchestrator e funções de atividade. Num ambiente de várias VMS, separando a depuração por longos períodos de tempo pode causar a outra VM escolher a mensagem, resultando em execução duplicada. Esse comportamento existe para as funções de Acionador de fila regulares, bem, mas é importante ressaltar neste contexto, uma vez que as filas são um detalhe de implementação.

> [!TIP]
> Ao definir pontos de interrupção, se quiser interromper apenas em execução de não repetição, pode definir um ponto de interrupção condicional que se apenas de quebras `IsReplaying` é `false`.

## <a name="storage"></a>Armazenamento

Por predefinição, as funções duráveis armazena o estado no armazenamento do Azure. Isso significa que pode inspecionar o estado das suas orquestrações com ferramentas, como [Explorador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de ecrã de Explorador de armazenamento do Azure](./media/durable-functions-diagnostics/storage-explorer.png)

Isto é útil para depuração, pois ver exatamente qual estado pode estar numa orquestração. Mensagens de filas também podem ser examinadas para saber qual trabalho está pendente (ou com dificuldades em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execuções no armazenamento de tabelas, evite colocar qualquer dependência nesta tabela. Eles podem mudar à medida que a extensão de funções duráveis evolui.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aprenda a usar temporizadores duráveis](durable-functions-timers.md)
