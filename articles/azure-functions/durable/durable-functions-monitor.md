---
title: Monitores no Durable Functions-Azure
description: Saiba como implementar um monitor de status usando a extensão Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 992e3f7aa53fdd006d29c06113cd30b07a406f3b
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734343"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Cenário de monitor no exemplo do observador de Durable Functions de clima

O padrão de monitor refere-se a um processo *recorrente* flexível em um fluxo de trabalho, por exemplo, sondando até que determinadas condições sejam atendidas. Este artigo explica um exemplo que usa [Durable Functions](durable-functions-overview.md) para implementar o monitoramento.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Este exemplo monitora as condições do clima atual de um local e alerta um usuário por SMS quando o Skies está claro. Você pode usar uma função disparada por temporizador regular para verificar o clima e enviar alertas. No entanto, um problema com essa abordagem é o **Gerenciamento da vida útil**. Se apenas um alerta for enviado, o monitor precisará se desabilitá-lo depois que o clima claro for detectado. O padrão de monitoramento pode terminar sua própria execução, entre outros benefícios:

* Monitores executados em intervalos, não em agendas: um gatilho de temporizador *é executado* a cada hora; um monitor *aguarda* uma hora entre as ações. As ações de um monitor não se sobrepõem a menos que especificado, o que pode ser importante para tarefas de longa execução.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode ser alterado com base em alguma condição.
* Os monitores podem terminar quando alguma condição é atendida ou terminada por outro processo.
* Os monitores podem usar parâmetros. O exemplo mostra como o mesmo processo de monitoramento meteorológico pode ser aplicado a qualquer local e número de telefone solicitados.
* Os monitores são escalonáveis. Como cada monitor é uma instância de orquestração, vários monitores podem ser criados sem a necessidade de criar novas funções ou definir mais códigos.
* Os monitores integram-se facilmente a fluxos de trabalho maiores. Um monitor pode ser uma seção de uma função de orquestração mais complexa ou uma [suborquestração](durable-functions-sub-orchestrations.md).

## <a name="configuring-twilio-integration"></a>Configurando a integração do twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Configurando a integração do Weather Underground

Este exemplo envolve o uso da API Weather Underground para verificar as condições do clima atual de um local.

A primeira coisa que você precisa é de uma conta de clima Underground. Você pode criar um gratuitamente em [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Quando você tiver uma conta, será necessário adquirir uma chave de API. Você pode fazer isso visitando [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)e, em seguida, selecionando configurações de chave. O plano de desenvolvedor do Stratus é gratuito e suficiente para executar este exemplo.

Quando você tiver uma chave de API, adicione a seguinte **configuração de aplicativo** ao seu aplicativo de funções.

| Nome da configuração do aplicativo | Descrição do valor |
| - | - |
| **WeatherUndergroundApiKey**  | Sua chave de API de clima Underground. |

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E3_Monitor`: Uma função de orquestrador que `E3_GetIsClear` chama periodicamente. Ele chama `E3_SendGoodWeatherAlert` se `E3_GetIsClear` retorna true.
* `E3_GetIsClear`: Uma função de atividade que verifica as condições do clima atual de um local.
* `E3_SendGoodWeatherAlert`: Uma função de atividade que envia uma mensagem SMS por meio de twilio.

As seções a seguir explicam a configuração e o código que C# são usados para scripts e JavaScript. O código para desenvolvimento do Visual Studio é mostrado no final do artigo.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de monitoramento meteorológico (Visual Studio Code e portal do Azure código de exemplo)

A função **E3_Monitor** usa o *Function. JSON* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Este é o código que implementa a função:

### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Essa função de orquestrador executa as seguintes ações:

1. Obtém o **MonitorRequest** que consiste no *local* a ser monitorado e o *número de telefone* para o qual ele enviará uma notificação de SMS.
2. Determina o tempo de expiração do monitor. O exemplo usa um valor embutido em código para fins de brevidade.
3. Chama **E3_GetIsClear** para determinar se há uma Clear Skies no local solicitado.
4. Se o clima estiver claro, o chamará **E3_SendGoodWeatherAlert** para enviar uma notificação de SMS para o número de telefone solicitado.
5. Cria um temporizador durável para retomar a orquestração no próximo intervalo de sondagem. O exemplo usa um valor embutido em código para fins de brevidade.
6. Continua em execução até [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) que oC#CurrentUtcDateTime ( `currentUtcDateTime` ) ou (JavaScript) passe o tempo de expiração do monitor ou um alerta SMS seja enviado.

Várias instâncias de orquestrador podem ser executadas simultaneamente enviando vários **MonitorRequests**. O local a ser monitorado e o número de telefone para o qual enviar um alerta do SMS pode ser especificado.

## <a name="strongly-typed-data-transfer-net-only"></a>Transferência de dados fortemente tipados (somente .NET)

O orquestrador requer várias partes de dados; portanto, os [objetos poco compartilhados](../functions-reference-csharp.md#reusing-csx-code) são usados para transferência de dados com C# rigidez C# de tipos e script:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

O exemplo de JavaScript usa objetos JSON regulares como parâmetros.

## <a name="helper-activity-functions"></a>Funções de atividade do auxiliar

Assim como ocorre com outros exemplos, as funções de atividade auxiliar são funções regulares `activityTrigger` que usam a associação de gatilho. A função **E3_GetIsClear** Obtém as condições do clima atual usando a API do clima Underground e determina se o céu está claro. O *Function. JSON* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

E aqui está a implementação. Como o POCOs usado para transferência de dados, a lógica para manipular a chamada à API e analisar a resposta JSON é dissociada em uma C#classe compartilhada no. Você pode encontrá-lo como parte do [código de exemplo do Visual Studio](#run-the-sample).

### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

A função **E3_SendGoodWeatherAlert** usa a associação twilio para enviar uma mensagem SMS notificando o usuário final de que é um bom momento para uma passagem. Seu *Function. JSON* é simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

E aqui está o código que envia a mensagem SMS:

### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Executar o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

A instância **E3_Monitor** inicia e consulta as condições do clima atual para o local solicitado. Se o clima estiver claro, ele chamará uma função de atividade para enviar um alerta; caso contrário, ele definirá um temporizador. Quando o temporizador expirar, a orquestração será retomada.

Você pode ver a atividade da orquestração examinando os logs de função no portal de Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

A orquestração será [encerrada](durable-functions-instance-management.md) quando seu tempo limite for atingido ou se a figura estiver clara. Você também pode usar `TerminateAsync` (.net) ou `terminate` (JavaScript) dentro de outra função ou invocar o webhook **terminatePostUri** http post referenciado na resposta 202 acima `{text}` , substituindo pelo motivo do encerramento:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Aqui está a orquestração como um único C# arquivo em um projeto do Visual Studio:

> [!NOTE]
> Será necessário instalar o `Microsoft.Azure.WebJobs.Extensions.Twilio` pacote NuGet para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo demonstrou como usar Durable Functions para monitorar o status de uma fonte externa usando [temporizadores duráveis](durable-functions-timers.md) e a lógica condicional. O exemplo a seguir mostra como usar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com a interação humana.

> [!div class="nextstepaction"]
> [Executar o exemplo de interação humana](durable-functions-phone-verification.md)
