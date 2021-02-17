---
title: Gatilho temporizador para funções Azure
description: Compreenda como utilizar gatilhos temporizadores em Funções Azure.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: c6b3bd61386cbde0e8de63055eee9218e372dfcd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547847"
---
# <a name="timer-trigger-for-azure-functions"></a>Gatilho temporizador para funções Azure

Este artigo explica como trabalhar com gatilhos temporizadores em Funções Azure. Um gatilho de temporizador permite-lhe executar uma função num horário.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para obter informações sobre como executar manualmente uma função acionada pelo temporizador, consulte [executar manualmente uma função não acionada por HTTP](./functions-manually-run-non-http.md).

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

O gatilho do temporizador é fornecido no pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão 3.x. O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

O gatilho do temporizador é fornecido no pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão 2.x. O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) executada cada vez que as atas têm um valor divisível por cinco (por exemplo, se a função começar às 18:57:00, a próxima performance será às 19:00:00). O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho do temporizador numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve um registo que indica se esta invocação da função se deve a uma falha de horário. O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código do guião C:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

A função de exemplo a seguir dispara e executa a cada cinco minutos. A `@TimerTrigger` anotação na função define o calendário utilizando o mesmo formato de corda que as [expressões cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho do temporizador numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve um registo que indica se esta invocação da função se deve a uma falha de horário. Um [objeto temporizador](#usage) é passado para a função.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como configurar o *ficheiro defunction.js* e *run.ps1* para um gatilho do temporizador no [PowerShell](./functions-reference-powershell.md).

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Um exemplo do [objeto temporizador](#usage) é passado como o primeiro argumento para a função.

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir utiliza uma ligação do gatilho do temporizador cuja configuração é descrita no *function.jsficheiro.* A [função Python](functions-reference-python.md) real que utiliza a ligação é descrita no ficheiro *__init__.py.* O objeto transmitido para a função é do tipo [azure.funções.TimerRequest .](/python/api/azure-functions/azure.functions.timerrequest) A lógica da função escreve para os registos indicando se a invocação atual se deve a uma falha de horário.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Aqui está o código Python:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

O construtor do atributo requer uma expressão cron ou um `TimeSpan` . Só pode ser utilizado `TimeSpan` se a aplicação de função estiver a funcionar num plano de Serviço de Aplicações. `TimeSpan` não é suportado para funções de consumo ou premium elástico.

O exemplo a seguir mostra uma expressão CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A `@TimerTrigger` anotação na função define o calendário utilizando o mesmo formato de corda que as [expressões cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `TimerTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido como "TimerTrigger". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para "dentro". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome da variável que representa o objeto temporizador no código de função. | 
|**agendar**|**ProgramaçãoExpressão**|Uma [expressão cron](#ncrontab-expressions) ou um valor [TimeSpan.](#timespan) A `TimeSpan` pode ser usada apenas para uma aplicação de função que funciona num Plano de Serviço de Aplicações. Pode colocar a expressão do agendamento numa configuração de aplicação e definir esta propriedade para o nome de definição de aplicação embrulhado **%** em sinais, como neste exemplo: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Se `true` , a função é invocada quando o tempo de funcionamento começa. Por exemplo, o tempo de funcionamento começa quando a aplicação de função acorda depois de ficar inativa devido à inatividade. quando a aplicação de função reinicia devido a alterações de função, e quando a aplicação de função se escala. Assim, **o runOnStartup** raramente deve ser definido `true` para , especialmente na produção. |
|**useMonitor**|**UseMonitor**|Definir `true` para ou `false` para indicar se o horário deve ser monitorizado. A monitorização da programação persiste em horários de ocorrências para ajudar a garantir que o horário é mantido corretamente mesmo quando as instâncias de aplicações de função reiniciam. Se não for definido explicitamente, o padrão é `true` para horários que tenham um intervalo de recorrência superior ou igual a 1 minuto. Para horários que desencadeiam mais de uma vez por minuto, o padrão é `false` .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Recomendamos contra a definição **do runOnStartup** `true` na produção. A utilização desta definição faz com que o código seja executado em tempos altamente imprevisíveis. Em certas definições de produção, estas execuções extra podem resultar em custos significativamente mais elevados para as aplicações hospedadas nos planos de Consumo. Por exemplo, com **o runOnStartup** ativado, o gatilho é invocado sempre que a sua aplicação de função é dimensionada. Certifique-se de que compreende perfeitamente o comportamento de produção das suas funções antes de permitir o **runOnStartup** na produção.

## <a name="usage"></a>Utilização

Quando uma função de gatilho do temporizador é invocada, um objeto temporizador é passado para a função. O seguinte JSON é uma representação exemplo do objeto temporizador.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

A `isPastDue` propriedade é quando a `true` invocação da função atual é mais tarde do que o programado. Por exemplo, um reinício de uma aplicação de função pode fazer com que uma invocação seja perdida.

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB

A Azure Functions utiliza a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões NCRONTAB. Uma expressão NCRONTAB é semelhante a uma expressão CRON, exceto que inclui um sexto campo adicional no início para usar para precisão de tempo em segundos:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo pode ter um dos seguintes tipos de valores:

|Tipo  |Exemplo  |Quando acionado  |
|---------|---------|---------|
|Um valor específico |<nobr>`0 5 * * * *`</nobr>| Uma vez a cada hora do dia ao minuto 5 de cada hora |
|Todos os valores `*` ()|<nobr>`0 * 5 * * *`</nobr>| A cada minuto da hora, começando na hora 5 |
|Uma gama `-` (operador)|<nobr>`5-7 * * * * *`</nobr>| Três vezes por minuto - em segundos 5 a 7 durante cada minuto de cada hora de cada dia |
|Um conjunto de valores `,` (operador)|<nobr>`5,8,10 * * * * *`</nobr>| Três vezes por minuto - aos segundos 5, 8 e 10 durante cada minuto de cada hora de cada dia |
|Um valor de intervalo `/` (operador)|<nobr>`0 */5 * * * *`</nobr>| 12 vezes por hora - no segundo 0 de cada 5 minutos de cada hora de cada dia |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Exemplos NCRONTAB

Aqui estão alguns exemplos de expressões NCRONTAB que pode usar para o gatilho do temporizador em Funções Azure.

| Exemplo            | Quando acionado                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | uma vez a cada cinco minutos            |
| `0 0 * * * *`      | uma vez no topo de cada hora      |
| `0 0 */2 * * *`    | uma vez a cada duas horas               |
| `0 0 9-17 * * *`   | uma vez a cada hora das 9:00 às 17:00  |
| `0 30 9 * * *`     | às 9:30 todos os dias               |
| `0 30 9 * * 1-5`   | às 9:30 am todos os dias da semana           |
| `0 30 9 * Jan Mon` | às 9:30 todas as segundas-feiras de janeiro |

> [!NOTE]
> A expressão NCRONTAB requer um formato **de seis campos.** A sexta posição de campo é um valor para segundos que é colocado no início da expressão. Cinco expressões de cron de campo não são suportadas em Azure.

### <a name="ncrontab-time-zones"></a>Fusos horários NCRONTAB

Os números numa expressão CRON referem-se a uma hora e data, e não a um período de tempo. Por exemplo, um 5 no `hour` campo refere-se às 5:00, não a cada 5 horas.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` pode ser usada apenas para uma aplicação de função que funciona num Plano de Serviço de Aplicações.

Ao contrário de uma expressão CRON, um `TimeSpan` valor especifica o intervalo de tempo entre cada invocação da função. Quando uma função termina após o período de funcionamento mais longo do que o intervalo especificado, o temporizador volta a invocar a função.

Expresso como uma corda, o `TimeSpan` formato é quando é inferior a `hh:mm:ss` `hh` 24. Quando os dois primeiros dígitos são 24 ou superiores, o formato é `dd:hh:mm` . Eis alguns exemplos:

| Exemplo      | Quando acionado |
|--------------|----------------|
| "01:00:00"   | a cada hora     |
| "00:01:00"   | a cada minuto   |
| "24:00:00"   | a cada 24 dias  |
| "1.00:00:00" | Todos os dias      |

## <a name="scale-out"></a>Aumentar

Se uma aplicação de função se reduzir para várias instâncias, apenas uma instância de uma função desencadeada pelo temporizador é executada em todas as instâncias. Não voltará a disparar se houver uma invocação pendente ainda em execução.

## <a name="function-apps-sharing-storage"></a>Aplicativos de função partilhando armazenamento

Se estiver a partilhar contas de armazenamento através de aplicações de funções que não estão implantadas no serviço de aplicações, poderá ter de atribuir explicitamente o ID do anfitrião a cada aplicação.

| Versão de funções | Definição                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (e superior)  | `AzureFunctionsWebHost__hostid` variável de ambiente |
| 1.x               | `id` em *host.jsem*                                  |

Pode omitir o valor de identificação ou definir manualmente a configuração de identificação de cada aplicação de função para um valor diferente.

O gatilho do temporizador utiliza um bloqueio de armazenamento para garantir que existe apenas uma instância temporizador quando uma aplicação de função se escala para várias instâncias. Se duas aplicações de função partilham a mesma configuração de identificação e cada uma utiliza um gatilho do temporizador, apenas um temporizador funciona.

## <a name="retry-behavior"></a>Relemgar o comportamento

Ao contrário do gatilho da fila, o gatilho do temporizador não se ressando depois de uma função falhar. Quando uma função falha, não é chamada novamente até a próxima vez na programação.

## <a name="troubleshooting"></a>Resolução de problemas

Para obter informações sobre o que fazer quando o gatilho do temporizador não funcionar como esperado, consulte [questões de investigação e reporte com funções desacionadas de temporizador que não disparem](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vá a um arranque rápido que usa um gatilho do temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
