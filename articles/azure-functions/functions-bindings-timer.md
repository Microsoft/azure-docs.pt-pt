---
title: Gatilho de temporizador para Azure Functions
description: Entenda como usar gatilhos de temporizador no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 962c28c8b081980c2715d4d78739662e86748bd1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814448"
---
# <a name="timer-trigger-for-azure-functions"></a>Gatilho de temporizador para Azure Functions 

Este artigo explica como trabalhar com gatilhos de temporizador no Azure Functions. Um gatilho de temporizador permite que você execute uma função em um agendamento. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

O gatilho de temporizador é fornecido no pacote NuGet [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , versão 2. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

O gatilho de temporizador é fornecido no pacote NuGet [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) , versão 3. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

Veja o exemplo de idioma específico:

* [C#](#c-example)
* [Script do c# (.csx)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C#exemplo

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que é executada cada vez que os minutos têm um valor divisível por cinco (por exemplo, se a função começar em 18:57:00, o próximo desempenho será em 19:00:00). O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

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

### <a name="c-script-example"></a>C#exemplo de script

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função grava um log que indica se essa invocação de função se deve a uma ocorrência de agendamento ausente. O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código de script do c#:

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

### <a name="f-example"></a>F#exemplo

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *Function. JSON* e uma [ F# função de script](functions-reference-fsharp.md) que usa a associação. A função grava um log que indica se essa invocação de função se deve a uma ocorrência de agendamento ausente. O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o F# código de script:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Exemplo de Java

A função de exemplo a seguir dispara e executa a cada cinco minutos. A `@TimerTrigger` anotação na função define o agendamento usando o mesmo formato de cadeia de caracteres de [expressões cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava um log que indica se essa invocação de função se deve a uma ocorrência de agendamento ausente. Um [objeto de timer](#usage) é passado para a função.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Eis o código JavaScript:

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

### <a name="python-example"></a>Exemplo de Python

O exemplo a seguir usa uma associação de gatilho de temporizador cuja configuração é descrita no arquivo *Function. JSON* . A [função Python](functions-reference-python.md) real que usa a associação é descrita no arquivo  *__init__. py* . O objeto passado para a função é do tipo [objeto Azure. Functions. TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). A lógica de função grava nos logs indicando se a invocação atual é devido a uma ocorrência de agendamento ausente. 

Eis a vinculação de dados a *Function* ficheiro:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Este é o código Python:

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

## <a name="attributes"></a>Atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

O construtor do atributo usa uma expressão CRON ou uma `TimeSpan`. Você só poderá `TimeSpan` usar se o aplicativo de funções estiver em execução em um plano do serviço de aplicativo. O exemplo a seguir mostra uma expressão CRON:

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

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `TimerTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como "timerTrigger". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "in". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa o objeto de timer no código de função. | 
|**schedule**|**Scheduler**|Uma [expressão cron](#ncrontab-expressions) ou um valor [TimeSpan](#timespan) . Um `TimeSpan` só pode ser usado para um aplicativo de funções que é executado em um plano do serviço de aplicativo. Você pode colocar a expressão de agendamento em uma configuração de aplicativo e definir essa propriedade como o nome da configuração **%** do aplicativo encapsulado em sinais, como neste exemplo: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Se `true`, a função será invocada quando o tempo de execução for iniciado. Por exemplo, o tempo de execução é iniciado quando o aplicativo de funções é ativado depois de ficar ocioso devido à inatividade. Quando o aplicativo de funções é reiniciado devido a alterações de função e quando o aplicativo de funções é dimensionado horizontalmente. Portanto, **runOnStartup** deve ser raramente, se já `true`estiver definido como, especialmente em produção. |
|**useMonitor**|**UseMonitor**|Defina como `true` ou `false` para indicar se o agendamento deve ser monitorado. Agendar monitoramento persistes agendar ocorrências para ajudar a garantir que a agenda seja mantida corretamente mesmo quando as instâncias do aplicativo de funções forem reiniciadas. Se não for definido explicitamente, o padrão `true` será para agendas que têm um intervalo de recorrência maior que 1 minuto. Para agendas que disparam mais de uma vez por minuto `false`, o padrão é.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> É recomendável que você defina `true` runOnStartup como em produção. Usar essa configuração faz com que o código seja executado em momentos altamente imprevisíveis. Em determinadas configurações de produção, essas execuções extras podem resultar em custos significativamente mais altos para aplicativos hospedados em planos de consumo. Por exemplo, com **runOnStartup** habilitado, o gatilho é invocado sempre que seu aplicativo de funções é dimensionado. Certifique-se de compreender totalmente o comportamento de produção de suas funções antes de habilitar o **runOnStartup** em produção.   

## <a name="usage"></a>Utilização

Quando uma função de gatilho de temporizador é invocada, um objeto de temporizador é passado para a função. O JSON a seguir é uma representação de exemplo do objeto de timer.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

A `IsPastDue` propriedade é `true` quando a invocação de função atual é posterior à agendada. Por exemplo, uma reinicialização do aplicativo de funções pode fazer com que uma invocação seja perdida.

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB 

Azure Functions usa a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar as expressões NCronTab. Um NCRONTAB exppression é semelhante a uma expressão CRON, exceto pelo fato de que ele inclui um sexto campo adicional no início a ser usado para a precisão de tempo em segundos:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando disparado  |
|---------|---------|---------|
|Um valor específico |<nobr>"0 5 * * * *"</nobr>|às hh: 05:00, em que HH é a cada hora (uma vez por hora)|
|Todos os valores`*`()|<nobr>"0 * 5 * * *"</nobr>|às 5: mm: 00 todos os dias, em que mm é cada minuto da hora (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>"5-7 * * * * *"</nobr>|em hh: mm: 05, hh: mm: 06 e hh: mm: 07, em que HH: mm é cada minuto de cada hora (3 vezes por minuto)|  
|Um conjunto de valores (`,` operador)|<nobr>"5, 8, 10 * * * * *"</nobr>|em hh: mm: 05, hh: mm: 08 e hh: mm: 10 em que HH: mm é cada minuto de cada hora (3 vezes por minuto)|
|Um valor de intervalo`/` (operador)|<nobr>"0 */5 * * * *"</nobr>|em hh: 05:00, hh: 10:00, hh: 15:00 e assim por diante por meio de hh: 55:00, em que HH é a cada hora (12 vezes por hora)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Exemplos de NCRONTAB

Aqui estão alguns exemplos de expressões NCRONTAB que você pode usar para o gatilho de temporizador no Azure Functions.

|Exemplo|Quando disparado  |
|---------|---------|
|`"0 */5 * * * *"`|uma vez a cada cinco minutos|
|`"0 0 * * * *"`|uma vez na parte superior de cada hora|
|`"0 0 */2 * * *"`|uma vez a cada duas horas|
|`"0 0 9-17 * * *"`|uma vez a cada hora, das 9h às 17h|
|`"0 30 9 * * *"`|às 9:30, todos os dias|
|`"0 30 9 * * 1-5"`|às 9:30, todos os dias úteis|
|`"0 30 9 * Jan Mon"`|às 9:30, todas as segundas-feiras em janeiro|


### <a name="ncrontab-time-zones"></a>NCRONTAB fuso horário

Os números em uma expressão CRON referem-se a uma hora e data, não a um período de tempo. Por exemplo, um 5 no `hour` campo refere-se a 5:00 am, e não a cada 5 horas.

O fuso horário padrão usado com as expressões CRON é UTC (tempo Universal Coordenado). Para que sua expressão CRON se baseie em outro fuso horário, crie uma configuração de aplicativo para seu `WEBSITE_TIME_ZONE`aplicativo de funções chamado. Defina o valor para o nome do fuso horário desejado, conforme mostrado no [índice de fuso horário da Microsoft](https://technet.microsoft.com/library/cc749073). 

Por exemplo, o *horário padrão do leste* é o UTC-05:00. Para que o gatilho do temporizador seja acionado às 10:00 AM EST todos os dias, use a seguinte expressão NCRONTAB que conta para o fuso horário UTC:

```
"0 0 15 * * *"
``` 

Ou crie uma configuração de aplicativo para seu aplicativo de `WEBSITE_TIME_ZONE` funções denominada e defina o valor para **hora padrão do leste**.  Em seguida, usa a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando você usa `WEBSITE_TIME_ZONE`o, o tempo é ajustado para alterações de tempo no fuso horário específico, como o horário de verão. 

## <a name="timespan"></a>TimeSpan

 Um `TimeSpan` só pode ser usado para um aplicativo de funções que é executado em um plano do serviço de aplicativo.

Ao contrário de uma expressão cron `TimeSpan` , um valor especifica o intervalo de tempo entre cada invocação de função. Quando uma função é concluída após ser executada por mais tempo do que o intervalo especificado, o temporizador imediatamente invoca a função novamente.

Expresso como uma cadeia de caracteres `TimeSpan` , o `hh:mm:ss` formato `hh` é quando é menor que 24. Quando os dois primeiros dígitos são 24 ou mais, o formato é `dd:hh:mm`. Eis alguns exemplos:

|Exemplo |Quando disparado  |
|---------|---------|
|"01:00:00" | a cada hora        |
|"00:01:00"|a cada minuto         |
|"24:00:00" | a cada 24 dias        |
|"1,00:00:00" | todos os dias        |

## <a name="scale-out"></a>Expandir

Se um aplicativo de funções for dimensionado para várias instâncias, apenas uma única instância de uma função disparada por temporizador será executada em todas as instâncias.

## <a name="function-apps-sharing-storage"></a>Armazenamento de compartilhamento de aplicativos de funções

Se você compartilhar uma conta de armazenamento em vários aplicativos de funções, certifique-se de que cada aplicativo `id` de funções tem um *host. JSON*diferente. Você pode omitir `id` a propriedade ou definir manualmente cada aplicativo de `id` funções para um valor diferente. O gatilho de temporizador usa um bloqueio de armazenamento para garantir que haverá apenas uma instância de temporizador quando um aplicativo de funções for dimensionado para várias instâncias. Se dois aplicativos de funções compartilharem `id` o mesmo e cada um usar um gatilho de temporizador, somente um temporizador será executado.

## <a name="retry-behavior"></a>Comportamento de repetição

Ao contrário do gatilho de fila, o gatilho de temporizador não tenta novamente depois que uma função falha. Quando uma função falha, ela não é chamada novamente até a próxima vez no agendamento.

## <a name="troubleshooting"></a>Resolução de problemas

Para obter informações sobre o que fazer quando o gatilho do temporizador não funciona conforme o esperado, consulte [investigando e relatando problemas com funções disparadas por temporizador não acionando](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ir para um início rápido que usa um gatilho de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
