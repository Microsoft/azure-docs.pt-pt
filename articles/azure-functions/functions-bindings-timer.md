---
title: Acionador de temporizador das funções do Azure
description: Compreenda como utilizar acionadores de temporizadores nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 0779ca2083691949821999322a3d732aed7b2694
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760772"
---
# <a name="timer-trigger-for-azure-functions"></a>Acionador de temporizador das funções do Azure 

Este artigo explica como trabalhar com acionadores de temporizadores nas funções do Azure. Um acionador de temporizador permite-lhe executar uma função com base numa agenda. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

O acionador de temporizador é fornecido na [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet, versão 2.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

O acionador de temporizador é fornecido na [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet, versão 3.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

Veja o exemplo de idioma específico:

* [C#](#c-example)
* [Script do c# (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>Exemplo do c#

A exemplo a seguir mostra um [ C# função](functions-dotnet-class-library.md) que é executado sempre que os minutos de ter um valor divisível por cinco (por exemplo, se a função começa às 18:00 57:, o desempenho seguinte será 19: 00:00). O [ `TimerInfo` ](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

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

### <a name="c-script-example"></a>Exemplo de script do c#

O exemplo seguinte mostra um acionador de temporizador de enlace num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função escreve um registo que indica se esta invocação de função é devido a uma ocorrência de agendamento em falta. O [ `TimerInfo` ](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

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

O exemplo seguinte mostra um acionador de temporizador de enlace num *Function* ficheiro e uma [ F# função do script](functions-reference-fsharp.md) que utiliza o enlace. A função escreve um registo que indica se esta invocação de função é devido a uma ocorrência de agendamento em falta. O [ `TimerInfo` ](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

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

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo seguinte mostra um acionador de temporizador de enlace num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função escreve um registo que indica se esta invocação de função é devido a uma ocorrência de agendamento em falta. R [objeto timer](#usage) é passado para a função.

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

### <a name="java-example"></a>Exemplo de Java

A seguinte função de exemplo aciona e executa a cada cinco minutos. O `@TimerTrigger` anotação na função define a agenda com o mesmo formato de cadeia de caracteres que [expressões CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

## <a name="attributes"></a>Atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Construtor do atributo utiliza uma expressão CRON ou um `TimeSpan`. Pode usar `TimeSpan` apenas se a aplicação de função está em execução num plano do serviço de aplicações. O exemplo seguinte mostra uma expressão CRON:

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
|**tipo** | n/d | Tem de ser definido para "timerTrigger". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "in". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa o objeto de timer no código de função. | 
|**schedule**|**ScheduleExpression**|R [expressão CRON](#cron-expressions) ou uma [TimeSpan](#timespan) valor. A `TimeSpan` pode ser utilizado apenas para uma aplicação de função que é executado num plano do serviço de aplicações. Pode colocar a expressão de agendamento numa definição de aplicação e definir esta propriedade para a aplicação encapsulado em do nome da definição **%** sinais, tal como neste exemplo: "% ScheduleAppSetting %". |
|**runOnStartup**|**RunOnStartup**|Se `true`, a função é invocada quando o tempo de execução é iniciado. Por exemplo, o tempo de execução é iniciado quando a aplicação de funções reativado depois de ficar ociosa devido a inatividade. Quando a aplicação de funções reinicia devido a alterações de função e, quando a aplicação de funções aumenta horizontalmente. Então **runOnStartup** deve raramente se alguma vez ser definido como `true`, especialmente em produção. |
|**useMonitor**|**UseMonitor**|Defina como `true` ou `false` para indicar se a agenda deve ser monitorizada. Agenda de monitorização mantém as ocorrências de agenda para ajudar a garantir que a agenda é mantida corretamente, mesmo quando reiniciar instâncias de aplicações de função. Se não estiver definido explicitamente, a predefinição é `true` para agendas que têm um intervalo de periodicidade superior a 1 minuto. Para agendamentos que acionam mais de uma vez por minuto, a predefinição é `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Recomendamos a definição **runOnStartup** para `true` na produção. Com esta definição faz com código executar em momentos imprevisíveis altamente. Em determinadas configurações de produção, essas execuções Extras podem resultar em custos significativamente mais para aplicações alojadas nos planos de consumo. Por exemplo, com **runOnStartup** ativado o acionador na invocado sempre que a sua aplicação function app é dimensionada. Certifique-se de que compreende totalmente o comportamento de produção das suas funções antes de ativar **runOnStartup** na produção.   

## <a name="usage"></a>Utilização

Quando uma função de Acionador de temporizador é invocada, um objeto de timer é passado para a função. O JSON seguinte é uma representação de exemplo do objeto timer.

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

O `IsPastDue` propriedade é `true` quando a invocação de função atual for posterior que o previsto. Por exemplo, um reinício de aplicação de função poderá causar uma invocação despercebido.

## <a name="cron-expressions"></a>Expressões CRON 

O Azure utiliza as funções do [NCronTab](https://github.com/atifaziz/NCrontab) biblioteca interpretar expressões CRON. Uma expressão CRON inclui seis campos:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando acionado  |
|---------|---------|---------|
|Um valor específico |<nobr>"0 5 * * * *"</nobr>|em hh:05:00 em que hh significa a cada hora (uma vez por hora)|
|Todos os valores (`*`)|<nobr>"0 * 5 * * *"</nobr>|em 5:mm: 00 todos os dias, onde mm significa a cada minuto da hora (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05, hh:mm:06 e hh:mm:07 em que HH: mm é a cada minuto da hora em hora (3 vezes um minuto)|  
|Um conjunto de valores (`,` operador)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05, hh:mm:08 e hh:mm:10 em que HH: mm é a cada minuto da hora em hora (3 vezes um minuto)|
|Um valor de intervalo (`/` operador)|<nobr>"0 */5 * * * *"</nobr>|na hh:05:00, hh:10:00, hh:15:00, e assim por diante, por meio de hh:55:00 em que hh significa a cada hora (12 vezes por hora)|

Para especificar meses ou dias pode utilizar valores numéricos, nomes ou abreviações de nomes:

* Dias, os valores numéricos são 0 a 6 em que 0 é iniciada com Domingo.
* Os nomes estão em inglês. Por exemplo: `Monday`, `January`.
* Nomes diferenciam maiúsculas de minúsculas.
* Podem ser abreviados para nomes. Três letras é o comprimento de abreviatura recomendada.  Por exemplo: `Mon`, `Jan`. 

### <a name="cron-examples"></a>Exemplos CRON

Aqui estão alguns exemplos de expressões CRON que pode utilizar para o acionador de temporizador nas funções do Azure.

|Exemplo|Quando acionado  |
|---------|---------|
|`"0 */5 * * * *"`|uma vez a cada cinco minutos|
|`"0 0 * * * *"`|uma vez na parte superior de cada hora|
|`"0 0 */2 * * *"`|uma vez a cada duas horas|
|`"0 0 9-17 * * *"`|uma vez por hora das 9:00 para 5 PM|
|`"0 30 9 * * *"`|em 9H30 todos os dias|
|`"0 30 9 * * 1-5"`|em 9H30 cada dia da semana|
|`"0 30 9 * Jan Mon"`|em 9H30 sempre de segunda em Janeiro|
>[!NOTE]   
>Pode encontrar exemplos de expressão CRON online, mas muitos deles omita o `{second}` campo. Se copiar a partir de um deles, adicione a falta `{second}` campo. Normalmente, é aconselhável um zero nesse campo, não um asterisco.

### <a name="cron-time-zones"></a>CRON fusos horários

Os números numa expressão CRON referem-se para uma hora e data, não é um intervalo de tempo. Por exemplo, um 5 no `hour` campo refere-se para 5:00, nem todas as cinco horas.

O fuso horário predefinido utilizado com as expressões CRON é a hora Universal Coordenada (UTC). Para que a sua expressão CRON com base no fuso horário outro, crie uma definição de aplicação para a sua aplicação de função com o nome `WEBSITE_TIME_ZONE`. Defina o valor para o nome do fuso horário desejado como mostra a [índice de fuso horário do Microsoft](https://technet.microsoft.com/library/cc749073). 

Por exemplo, *hora padrão do Leste* é UTC 05:00. Para ter o temporizador de Acionador a acionar na 10 11:00 EST todos os dias, utilize a seguinte expressão CRON que leve fuso horário UTC:

```json
"schedule": "0 0 15 * * *"
``` 

Ou crie uma definição de aplicação para a sua aplicação de função com o nome `WEBSITE_TIME_ZONE` e defina o valor **hora padrão do Leste**.  Em seguida, utiliza a seguinte expressão CRON: 

```json
"schedule": "0 0 10 * * *"
``` 

Quando utiliza `WEBSITE_TIME_ZONE`, a hora é ajustada para alterações de hora no fuso horário específico, por exemplo, o horário de Verão. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` pode ser utilizado apenas para uma aplicação de função que é executado num plano do serviço de aplicações.

Ao contrário de uma expressão CRON, um `TimeSpan` valor Especifica o intervalo de tempo entre cada invocação de função. Quando uma função é concluída depois de executar mais do que o intervalo especificado, o temporizador imediatamente invoca a função novamente.

Expresso como uma cadeia de caracteres, o `TimeSpan` é o formato `hh:mm:ss` quando `hh` é inferior a 24. Quando os dois primeiros dígitos 24 ou superior, o formato é `dd:hh:mm`. Eis alguns exemplos:

|Exemplo |Quando acionado  |
|---------|---------|
|"01:00:00" | a cada hora        |
|"00:01:00"|cada minuto         |
|"24:00:00" | everyday        |

## <a name="scale-out"></a>Expandir

Se uma aplicação de funções aumenta horizontalmente para várias instâncias, apenas uma única instância de uma função acionada por temporizador é executada em todas as instâncias.

## <a name="function-apps-sharing-storage"></a>Armazenamento de partilha de aplicações de funções

Se partilhar uma conta de armazenamento entre várias aplicações de funções, certifique-se de que cada aplicação de função tem outra `id` no *Host. JSON*. Pode omitir os `id` propriedade ou definir manualmente cada aplicação de função `id` para um valor diferente. O acionador de temporizador utiliza um bloqueio de armazenamento para se certificar de que haverá apenas uma instância de temporizador quando uma aplicação de funções aumenta horizontalmente para várias instâncias. Se duas aplicações de função partilham o mesmo `id` e cada utiliza um acionador de temporizador, apenas um temporizador será executado.

## <a name="retry-behavior"></a>Comportamento de tentativas

Ao contrário do acionador de fila, o acionador de temporizador não repita após a falha de uma função. Quando uma função falhar, ele não é chamado novamente até a próxima vez na agenda.

## <a name="troubleshooting"></a>Resolução de problemas

Para obter informações sobre o que fazer quando o acionador de temporizador não funciona conforme esperado, consulte [Investigating e os relatórios de problemas com timer acionado funções não disparando](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ir para um início rápido que utiliza um acionador de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
