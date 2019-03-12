---
title: Monitores de funções duráveis - Azure
description: Saiba como implementar um monitor de estado com a extensão de funções duráveis para as funções do Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: f3b9ef9d840630269c4c5621a4dab3c732bacdbf
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551658"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Cenário de monitor em funções duráveis - exemplo de observador de Meteorologia

O padrão de monitor se refere a um flexível *recorrentes* processo num fluxo de trabalho - por exemplo, a consulta até que forem cumpridas determinadas condições. Este artigo explica um exemplo que utiliza [funções duráveis](durable-functions-overview.md) para implementar a monitorização.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Este exemplo monitora condições de Meteorologia atual de uma localização e alertas de um utilizador por SMS quando o céu é claras. Poderia usar uma função acionada por temporizador regular para verificar a meteorologia e enviar alertas. No entanto, é um problema com essa abordagem **gerenciamento de duração**. Se apenas um alerta deve ser enviado, o monitor tem de desativar em si após limpar Meteorologia é detectada. O padrão de monitorização pode terminar a sua própria execução, entre outros benefícios:

* Monitores executados em intervalos, não agenda: um acionador de temporizador *é executado* cada hora; um monitor *aguarda* uma hora entre ações. Ações de um monitor irão não se sobrepõem, a menos que especificado, que pode ser importante para tarefas de longa execução.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode mudar com base em alguma condição.
* Monitores podem terminar quando alguma condição for cumprida, ou ser terminada por outro processo.
* Monitores podem ter parâmetros. O exemplo mostra como o mesmo processo de monitorização de Meteorologia pode ser aplicado a qualquer pedida de localização e o número de telefone.
* Monitores são dimensionáveis. Uma vez que cada monitor é uma instância de orquestração, vários monitores podem ser criados sem ter de criar novas funções ou definir mais de código.
* Monitores integrar facilmente fluxos de trabalho maiores. Um monitor pode ser uma secção de uma função de orquestração mais complexa, ou uma [orquestração secundárias](durable-functions-sub-orchestrations.md).

## <a name="configuring-twilio-integration"></a>Configurar a integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Configurar a integração Weather Underground

Este exemplo envolve usar a API de subterrânea Meteorologia para verificar condições de Meteorologia atual para uma localização.

A primeira coisa que precisa é uma conta Weather Underground. Pode criar uma gratuitamente na [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Assim que tiver uma conta, terá de adquirir uma chave de API. Pode fazê-lo ao visitar [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), em seguida, a seleção de definições de chave. O plano de Stratus Developer é gratuito e suficientes para executar este exemplo.

Depois de ter uma chave de API, adicione as seguintes **definição de aplicação** à sua aplicação de função.

| Nome da definição de aplicação | Descrição do valor |
| - | - |
| **WeatherUndergroundApiKey**  | A chave de API subterrânea de Meteorologia. |

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* `E3_Monitor`: Uma função de orquestrador que chama `E3_GetIsClear` periodicamente. Ele chama `E3_SendGoodWeatherAlert` se `E3_GetIsClear` retorna true.
* `E3_GetIsClear`: Uma função de atividade que verifica as condições de Meteorologia atual para uma localização.
* `E3_SendGoodWeatherAlert`: Uma função de atividade que envia uma mensagem SMS através do Twilio.

As secções seguintes explicam a configuração e o código que são utilizados para c# scripts e JavaScript. O código para o desenvolvimento do Visual Studio é mostrado no final do artigo.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A meteorologia monitorização orquestração (código de exemplo do portal Visual Studio Code e o Azure)

O **E3_Monitor** função usa o padrão *Function* para as funções do orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Eis o código que implementa a função:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Esta função de orquestrador executa as seguintes ações:

1. Obtém a **MonitorRequest** consistindo a *localização* para monitorizar e a *número de telefone* para que ele irá enviar uma notificação de SMS.
2. Determina o tempo de expiração do monitor. Este exemplo utiliza um valor hard-coded para fins de brevidade.
3. Chamadas **E3_GetIsClear** para determinar se existem clara céu na localização pedida.
4. Se a meteorologia estiver desmarcada, chama **E3_SendGoodWeatherAlert** para enviar uma notificação de SMS para o número de telefone pedida.
5. Cria um temporizador durável para retomar a orquestração no próximo intervalo de consulta. Este exemplo utiliza um valor hard-coded para fins de brevidade.
6. Continua a ser executado até que o [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (C#) ou `currentUtcDateTime` (JavaScript) passa a hora de expiração do monitor ou um alerta SMS é enviado.

Várias instâncias do orchestrator podem enviar para executar simultaneamente várias **MonitorRequests**. A localização para monitorizar e o número de telefone para enviar um alerta SMS podem ser especificados.

## <a name="strongly-typed-data-transfer-net-only"></a>Transferência de dados possui rigidez de tipos (apenas para o .NET)

O orchestrator requer várias partes de dados, então [POCO objetos compartilhados](../functions-reference-csharp.md#reusing-csx-code) são utilizados para a transferência de dados possui rigidez de tipos em c# e c# script: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

O exemplo de JavaScript utiliza objetos normais do JSON como parâmetros.

## <a name="helper-activity-functions"></a>Funções de atividade de programa auxiliar

Como com outros exemplos, as funções de atividade de programa auxiliar são funções regulares que utilizam o `activityTrigger` acionar a ligação. O **E3_GetIsClear** função obtém as condições de Meteorologia atual usando a API de subterrânea meteorologia e determina se o céu é claro. O *Function* é definida da seguinte forma:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

E Eis a implementação. Como os POCOs utilizados para transferência de dados, a lógica para lidar com a API de chamar e analisar a resposta JSON é abstraído numa classe compartilhada em c#. Pode encontrá-lo como parte da [código de exemplo do Visual Studio](#run-the-sample).

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

O **E3_SendGoodWeatherAlert** função usa a vinculação do Twilio para enviar uma mensagem SMS de notificar o utilizador final que é um bom momento para dar uma. Seus *Function* é simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

E aqui está o código que envia a mensagem SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Executar o exemplo

Utilizar as funções de acionada por HTTP incluídas no exemplo, pode começar a orquestração ao enviar o pedido de HTTP POST seguinte:

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

O **E3_Monitor** instância é iniciado e as condições de Meteorologia atual para a localização do pedido de consulta. Se a meteorologia estiver desmarcada, ele chama uma função de atividade para enviar um alerta, caso contrário, ele define um temporizador. Quando o temporizador expira, será retomada a orquestração.

Pode ver registos de atividades da orquestração ao observar a função no portal funções do Azure.

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

A orquestração será [terminar](durable-functions-instance-management.md) assim que o tempo limite for atingiu ou desmarque o céu é detetados. Também pode utilizar `TerminateAsync` (.NET) ou `terminate` (JavaScript) dentro de outra função ou invocar a **terminatePostUri** webhook de HTTP POST referenciado na resposta 202 acima, substituindo `{text}` com o motivo para terminação de:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis a orquestração como um único arquivo c# num projeto do Visual Studio:

> [!NOTE]
> Terá de instalar o `Microsoft.Azure.WebJobs.Extensions.Twilio` pacote Nuget para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo tenha demonstrado como utilizar funções duráveis para monitorizar o estado de uma origem externa usando [temporizadores duráveis](durable-functions-timers.md) e a lógica condicional. O exemplo seguinte mostra como usar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com interação humana.

> [!div class="nextstepaction"]
> [Executar o exemplo de interação humana](durable-functions-phone-verification.md)
