---
title: Gatilho do temporizador para funções azure
description: Compreenda como utilizar gatilhos de temporizador nas Funções Azure.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056413"
---
# <a name="timer-trigger-for-azure-functions"></a>Gatilho do temporizador para funções azure 

Este artigo explica como trabalhar com gatilhos temporizadores nas Funções Azure. Um gatilho do temporizador permite-lhe executar uma função num horário. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

O gatilho do temporizador é fornecido no pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão 2.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

O gatilho do temporizador é fornecido no pacote [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versão 3.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que é executada cada vez que as atas têm um valor divisível por cinco (por exemplo, se a função começar às 18:57:00, a próxima performance será às 19:00:00). O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

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

O exemplo seguinte mostra uma ligação do gatilho do temporizador num ficheiro *function.json* e numa [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve um registo indicando se esta invocação de função se deve a uma ocorrência de horário perdido. O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

Aqui estão os dados vinculativos no ficheiro *função.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código de script C#:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho do temporizador num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve um registo indicando se esta invocação de função se deve a uma ocorrência de horário perdido. Um [objeto temporizador](#usage) é passado para a função.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="python"></a>[Pitão](#tab/python)

O exemplo seguinte utiliza uma ligação do gatilho do temporizador cuja configuração é descrita no ficheiro *fun.json.* A [função Python](functions-reference-python.md) real que utiliza a ligação é descrita no ficheiro * __init__.py.* O objeto passado para a função é de tipo [azure.functions.TimerRequest object](/python/api/azure-functions/azure.functions.timerrequest). A lógica da função escreve para os registos indicando se a invocação atual se deve a uma ocorrência de horário perdido. 

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="java"></a>[Java](#tab/java)

A função de exemplo seguinte dispara e executa a cada cinco minutos. A `@TimerTrigger` anotação na função define o horário utilizando o mesmo formato de cadeia que [as expressões CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

O construtor do atributo tem uma `TimeSpan`expressão CRON ou um . Só pode `TimeSpan` utilizar se a aplicação de funções estiver a funcionar num plano de Serviço de Aplicações. `TimeSpan`não é suportado para funções de Consumo ou Premium Elástico.

O exemplo que se segue mostra uma expressão CRON:

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

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `@TimerTrigger` anotação na função define o horário utilizando o mesmo formato de cadeia que [as expressões CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `TimerTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido para "TimerTrigger". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/d | Deve ser definido para "in". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d | O nome da variável que representa o objeto temporizador no código de função. | 
|**agenda**|**Expressão de horário**|Uma [expressão CRON](#ncrontab-expressions) ou um valor [TimeSpan.](#timespan) A `TimeSpan` pode ser usado apenas para uma aplicação de função que funciona em um Plano de Serviço de Aplicações. Pode colocar a expressão de horário numa definição de app e **%** definir esta propriedade para o nome de definição de aplicação envolto em sinais, como neste exemplo: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Se `true`, a função for invocada quando o tempo de funcionamento começar. Por exemplo, o tempo de execução começa quando a aplicação de funções acorda depois de ficar inativa devido à inatividade. quando a aplicação de função recomeçar devido a alterações de função, e quando a aplicação de função se esescala. Assim, **runOnStartup** raramente deve ser `true`definido para , especialmente na produção. |
|**useMonitor**|**Monitor de Utilização**|Definir `true` ou `false` indicar se o horário deve ser monitorizado. A monitorização do horário persiste em agendar ocorrências para ajudar a garantir que o horário seja mantido corretamente, mesmo quando as instâncias da aplicação de funções recomeçam. Se não for definido explicitamente, o padrão é `true` para horários que tenham um intervalo de recorrência superior ou igual a 1 minuto. Para horários que disparam mais de `false`uma vez por minuto, o padrão é .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Recomendamos não configurar `true` **runOnStartup** para em produção. A utilização desta definição faz com que o código execute em momentos altamente imprevisíveis. Em determinadas configurações de produção, estas execuções extra podem resultar em custos significativamente mais elevados para aplicações alojadas nos planos de Consumo. Por exemplo, com **o runOnStartup** ativado o gatilho é invocado sempre que a sua aplicação de função é dimensionada. Certifique-se de que compreende completamente o comportamento de produção das suas funções antes de ativar a **runOnStartup** em produção.   

## <a name="usage"></a>Utilização

Quando uma função de gatilho do temporizador é invocada, um objeto temporizador é passado para a função. O seguinte JSON é uma representação exemplo do objeto temporizador.

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

A `IsPastDue` propriedade `true` é quando a invocação da função atual é mais tarde do que o previsto. Por exemplo, um reinício de uma aplicação de função pode causar a falta de uma invocação.

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB 

A Azure Functions utiliza a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões NCRONTAB. Uma expressão NCRONTAB é semelhante a uma expressão CRON, exceto que inclui um sexto campo adicional no início da utilização para a precisão do tempo em segundos:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo pode ter um dos seguintes tipos de valores:

|Tipo  |Exemplo  |Quando desencadeado  |
|---------|---------|---------|
|Um valor específico |<nobr>"0 5 * * * *"</nobr>|em hh:05:00 onde hh é a cada hora (uma vez por hora)|
|Todos os`*`valores ()|<nobr>"0 * 5 * * *"</nobr>|às 5:mm:00 todos os dias, onde mm é cada minuto da hora (60 vezes por dia)|
|Uma gama`-` (operador)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05,hh:mm:06, e hh:mm:07 onde hh:mm é cada minuto de cada hora (3 vezes por minuto)|
|Um conjunto de`,` valores (operador)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05,hh:mm:08, e hh:mm:10 onde hh:mm é cada minuto de cada hora (3 vezes por minuto)|
|Um valor`/` de intervalo (operador)|<nobr>"0 */5 * * * *"</nobr>|hh:00:00, hh:05:00, hh:10:00, e assim por diante através de hh:55:00 onde hh é a cada hora (12 vezes por hora)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Exemplos ncrontab

Aqui estão alguns exemplos de expressões NCRONTAB que pode usar para o gatilho do temporizador nas Funções Azure.

|Exemplo|Quando desencadeado  |
|---------|---------|
|`"0 */5 * * * *"`|uma vez a cada cinco minutos|
|`"0 0 * * * *"`|uma vez no topo de cada hora|
|`"0 0 */2 * * *"`|uma vez a cada duas horas|
|`"0 0 9-17 * * *"`|uma vez a cada hora das 9:00 às 17:00|
|`"0 30 9 * * *"`|às 9:30 da manhã todos os dias|
|`"0 30 9 * * 1-5"`|às 9:30 da manhã todos os dias da semana|
|`"0 30 9 * Jan Mon"`|às 9:30 am todas as segundas-feiras em janeiro|


### <a name="ncrontab-time-zones"></a>Fusos horários NCRONTAB

Os números numa expressão CRON referem-se a uma data e data, não a um período de tempo. Por exemplo, um `hour` 5 no campo refere-se às 5:00 da manhã, não a cada 5 horas.

O fuso horário predefinido utilizado com as expressões CRON é o Tempo Universal Coordenado (UTC). Para ter a expressão CRON baseada noutro fuso horário, `WEBSITE_TIME_ZONE`crie uma definição de aplicação para a sua aplicação de função chamada . Detete o valor para o nome do fuso horário desejado, como mostrado no Índice de [Fuso Horário](https://technet.microsoft.com/library/cc749073)da Microsoft .

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`não é atualmente apoiado no plano de consumo linux.

Por exemplo, o *Horário Padrão Oriental* é UTC-05:00. Para que o seu temporizador dispare às 10:00 EST todos os dias, use a seguinte expressão NCRONTAB que explica o fuso horário UTC:

```
"0 0 15 * * *"
``` 

Ou crie uma definição `WEBSITE_TIME_ZONE` de aplicação para a sua aplicação de função nomeada e defina o valor para **o Tempo Padrão Oriental**.  Em seguida, utiliza a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando utilizar `WEBSITE_TIME_ZONE`, o tempo é ajustado para alterações de tempo no fuso horário específico, como o horário de verão. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` pode ser usado apenas para uma aplicação de função que funciona em um Plano de Serviço de Aplicações.

Ao contrário de `TimeSpan` uma expressão CRON, um valor especifica o intervalo de tempo entre cada invocação de função. Quando uma função completa após o tempo de execução mais longo do que o intervalo especificado, o temporizador invoca imediatamente a função novamente.

Expresso como uma `TimeSpan` corda, `hh:mm:ss` `hh` o formato é quando tem menos de 24 anos. Quando os dois primeiros dígitos são 24 ou mais, o formato é `dd:hh:mm`. Eis alguns exemplos:

|Exemplo |Quando desencadeado  |
|---------|---------|
|"01:00:00" | a cada hora        |
|"00:01:00"|cada minuto         |
|"24:00:00" | a cada 24 dias        |
|"1.00:00:00" | Todos os dias        |

## <a name="scale-out"></a>Aumentar

Se uma aplicação de função se dimensionar para várias instâncias, apenas uma instância de uma função acionada pelo temporizador é executada em todas as instâncias.

## <a name="function-apps-sharing-storage"></a>Aplicativos de função que partilham armazenamento

Se estiver a partilhar contas de armazenamento através de aplicações de funções que não estejam implementadas para o serviço de aplicações, poderá ter de atribuir explicitamente o ID do anfitrião a cada aplicação.

| Versão funções | Definição                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (e superior)  | `AzureFunctionsWebHost__hostid`variável ambiente |
| 1.x               | `id`em *host.json*                                  |

Pode omitir o valor de identificação ou definir manualmente a configuração de identificação de cada aplicação de função para um valor diferente.

O gatilho do temporizador utiliza um bloqueio de armazenamento para garantir que só existe uma instância temporizador quando uma aplicação de função se baseia em várias instâncias. Se duas aplicações de função partilharem a mesma configuração de identificação e cada uma usar um gatilho temporizador, apenas um temporizador corre.

## <a name="retry-behavior"></a>Comportamento de retry

Ao contrário do gatilho da fila, o gatilho do temporizador não se retenta depois de uma função falhar. Quando uma função falha, não é chamada novamente até à próxima vez na agenda.

## <a name="troubleshooting"></a>Resolução de problemas

Para obter informações sobre o que fazer quando o gatilho do temporizador não funciona como esperado, consulte questões de [investigação e reporte com funções acionadas pelo temporizador que não disparam](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vá a um arranque rápido que usa um gatilho temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
