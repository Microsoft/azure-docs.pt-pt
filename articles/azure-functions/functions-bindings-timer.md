---
title: Acionador de temporizador para as funções do Azure
description: Compreenda como utilizar os acionadores de temporizador nas funções do Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: 6f74dd4d9cb78c1316c87bd5a261e751b9b34923
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Acionador de temporizador para as funções do Azure 

Este artigo explica como trabalhar com os acionadores de temporizador nas funções do Azure. Um acionador de temporizador permite-lhe executar uma função com base numa agenda. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Pacotes

O acionador de temporizador é fornecido no [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet. Código de origem para o pacote está a ser o [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

Veja o exemplo de específicas do idioma:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Exemplo do c#

O seguinte exemplo mostra um [c# função](functions-dotnet-class-library.md) que é executado a cada cinco minutos:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Exemplo de script do c#

O exemplo seguinte mostra um acionador de temporizador enlace num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função escreve um registo que indica se esta invocação de função é devido a uma ocorrência de agenda em falta.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Eis o código de script do c#:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>O F # exemplo

O exemplo seguinte mostra um acionador de temporizador enlace num *function.json* ficheiro e uma [função de script F #](functions-reference-fsharp.md) que utiliza o enlace. A função escreve um registo que indica se esta invocação de função é devido a uma ocorrência de agenda em falta.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Eis o código de script F #:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo seguinte mostra um acionador de temporizador enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função escreve um registo que indica se esta invocação de função é devido a uma ocorrência de agenda em falta.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Eis o código de script de JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Atributos

No [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

O construtor do atributo aceita uma expressão de CRON, conforme mostrado no exemplo seguinte:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Pode especificar um `TimeSpan` em vez de uma expressão de CRON se a sua aplicação de função é executada num plano de serviço de aplicações (não um plano de consumo).

Para obter um exemplo completado, consulte [c# exemplo](#c-example).

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `TimerTrigger` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Tem de ser definida para "timerTrigger". Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure.|
|**direction** | n/d | Tem de ser definida para "em". Esta propriedade é definida automaticamente quando criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa o objeto temporizador no código da função. | 
|**schedule**|**ScheduleExpression**|No plano de consumo, pode definir agendas com uma expressão de CRON. Se estiver a utilizar um plano do App Service, também pode utilizar um `TimeSpan` cadeia. As secções seguintes explicam as expressões de CRON. Pode colocar a expressão de agenda uma definição de aplicação e definir esta propriedade para um valor moldado numa **%** inicia, tal como neste exemplo: "% NameOfAppSettingWithCRONExpression %". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>Formato CRON 

A [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) para o temporizador de funções do Azure acionador inclui estes seis campos: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>Muitas das expressões CRON encontrará online omitir o `{second}` campo. Se copiar a partir de um deles, adicione o falta `{second}` campo.

### <a name="cron-time-zones"></a>CRON fusos horários

O fuso horário predefinido utilizado com as expressões de CRON está Hora Universal Coordenada (UTC). Para que a expressão de CRON baseado no fuso horário outro, crie uma nova definição de aplicação para a sua aplicação de função com o nome `WEBSITE_TIME_ZONE`. Defina o valor para o nome do fuso horário pretendido, conforme mostrado no [Microsoft fuso horário índice](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Por exemplo, *hora padrão do Leste* é UTC-05:00. Ter o temporizador acionar fire em 10:00 EST diariamente, utilize a seguinte expressão CRON que as contas do fuso horário UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Em alternativa, pode adicionar uma nova definição de aplicação para a sua aplicação de função com o nome `WEBSITE_TIME_ZONE` e defina o valor **hora padrão do Leste**.  Em seguida, a seguinte expressão de CRON poderiam ser utilizada para 10:00 AM EST: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Exemplos CRON

Seguem-se alguns exemplos de expressões de CRON que pode utilizar para o acionador de temporizador de funções do Azure. 

Para acionar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para acionar uma vez na parte superior de cada hora:

```json
"schedule": "0 0 * * * *",
```

Para acionar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
```

Para acionar uma vez a cada hora de 09: 00 para as 17: 00:

```json
"schedule": "0 0 9-17 * * *",
```

Para acionar às 9:30 AM todos os dias:

```json
"schedule": "0 30 9 * * *",
```

Para acionar às 9:30 AM cada dia da semana:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Utilização

Quando uma função de Acionador de temporizador é invocada, o [objeto temporizador](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) é transmitida para a função. O seguinte JSON é uma representação de exemplo do objeto temporizador. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Expandir

O acionador de temporizador suporta várias instâncias Escalamento horizontal. Uma única instância de uma função de temporizador específico é executada em todas as instâncias.

## <a name="function-apps-sharing-storage"></a>Armazenamento de partilha de aplicações de função

Se partilhar uma conta de armazenamento por várias aplicações de função, certifique-se de que cada aplicação de função tem um outro `id` no *host.json*. Pode omitir o `id` propriedade ou definir manualmente cada aplicação de função `id` para um valor diferente. O acionador de temporizador utiliza um bloqueio de armazenamento para se certificar de que vai haver apenas uma instância de temporizador quando uma aplicação de função aumenta horizontalmente de forma a várias instâncias. Se duas aplicações de função partilham o mesmo `id` e cada utiliza um acionador de temporizador, irá executar apenas um temporizador.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ir para um guia de introdução que utiliza um acionador de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
