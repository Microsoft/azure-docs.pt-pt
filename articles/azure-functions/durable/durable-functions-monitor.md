---
title: Monitores em Funções Duradouras - Azure
description: Saiba como implementar um monitor de estado utilizando a extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: e70c50098ece516312e1e92984185624c276301b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028425"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Cenário de monitor em Funções Duradouras - Amostra de observador meteorológico

O padrão do monitor refere-se a um processo *recorrente* flexível num fluxo de trabalho - por exemplo, a votação até que determinadas condições sejam satisfeitas. Este artigo explica uma amostra que utiliza [funções duradouras](durable-functions-overview.md) para implementar a monitorização.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

Esta amostra monitoriza as condições meteorológicas atuais de um local e alerta um utilizador por SMS quando o céu está limpo. Pode utilizar uma função regular de acionamento do temporizador para verificar o tempo e enviar alertas. No entanto, um dos problemas com esta abordagem é **a gestão vitalícia.** Se apenas um alerta for enviado, o monitor deve desativar-se depois de ser detetado tempo limpo. O padrão de monitorização pode terminar a sua própria execução, entre outros benefícios:

* Os monitores funcionam em intervalos, não em horários: um gatilho do temporizador *funciona* a cada hora; um monitor *espera* uma hora entre as ações. As ações de um monitor não se sobreporão a menos que sejam especificadas, o que pode ser importante para tarefas de longa duração.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode mudar com base em alguma condição.
* Os monitores podem terminar quando alguma condição é satisfeita ou ser terminada por outro processo.
* Os monitores podem tomar parâmetros. A amostra mostra como o mesmo processo de monitorização das condições meteorológicas pode ser aplicado a qualquer localização e número de telefone solicitados.
* Os monitores são escaláveis. Como cada monitor é uma instância de orquestração, vários monitores podem ser criados sem ter que criar novas funções ou definir mais código.
* Os monitores integram-se facilmente em fluxos de trabalho maiores. Um monitor pode ser uma secção de uma função de orquestração mais complexa, ou uma [sub-orquestração.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Configuração

### <a name="configuring-twilio-integration"></a>Configuração da integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Configuração da integração do Weather Underground

Esta amostra envolve o uso da API do Weather Underground para verificar as condições meteorológicas atuais para uma localização.

A primeira coisa que precisas é de uma conta no Weather Underground. Pode criar um de [https://www.wunderground.com/signup](https://www.wunderground.com/signup) graça. Uma vez que tenha uma conta, terá de adquirir uma chave API. Pode fazê-lo visitando [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) e selecionando definições de chave. O plano stratus developer é gratuito e suficiente para executar esta amostra.

Assim que tiver uma chave API, adicione a seguinte **definição de aplicação** à sua aplicação de função.

| Nome de definição de aplicativo | Descrição do valor |
| - | - |
| **WeatherUndergroundApiKey**  | A sua chave de API subterrânea meteorológica. |

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E3_Monitor`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que chama `E3_GetIsClear` periodicamente. Liga `E3_SendGoodWeatherAlert` se `E3_GetIsClear` voltar a ser verdade.
* `E3_GetIsClear`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que verifica as condições meteorológicas atuais para um local.
* `E3_SendGoodWeatherAlert`: Uma função de atividade que envia uma mensagem SMS via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>função orquestrador E3_Monitor

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

O orquestrador requer um local para monitorizar e um número de telefone para enviar uma mensagem para quando o se tornar claro no local. Estes dados são transmitidos ao orquestrador como um objeto fortemente `MonitorRequest` dactilografado.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função **E3_Monitor** utiliza o *function.js* padrão para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Aqui está o código que implementa a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

# <a name="python"></a>[Python](#tab/python)
Temos um tutorial diferente para o padrão de monitorização em Python, por favor, veja-o [aqui.](durable-functions-monitor-python.md)

---

Esta função orquestradora executa as seguintes ações:

1. Obtém o **MonitorRequest** constituído pela *localização* para monitorizar e o *número de telefone* para o qual enviará uma notificação sms.
2. Determina o tempo de validade do monitor. A amostra usa um valor codificado para a brevidade.
3. As chamadas **E3_GetIsClear** para determinar se há céu limpo no local solicitado.
4. Se o tempo estiver limpo, ligue **E3_SendGoodWeatherAlert** para enviar uma notificação SMS para o número de telefone solicitado.
5. Cria um temporizador durável para retomar a orquestração no próximo intervalo de votação. A amostra usa um valor codificado para a brevidade.
6. Continua a funcionar até que o tempo utc atual passe o tempo de validade do monitor, ou um alerta SMS é enviado.

Vários casos de orquestradores podem ser executados simultaneamente, chamando a função de orquestrador várias vezes. A localização para monitorizar e o número de telefone para enviar um alerta SMS pode ser especificado. Por fim, note que a função do orquestrador *não* está a funcionar enquanto espera pelo temporizador, para que não seja cobrado por isso.
### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear função de atividade

Tal como acontece com outras amostras, as funções de atividade do ajudante são funções regulares que utilizam a ligação do `activityTrigger` gatilho. A função **E3_GetIsClear** obtém as condições meteorológicas atuais usando a API do Weather Underground e determina se o céu está limpo.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O *function.jsem diante* é definido da seguinte forma:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

E aqui está a implementação.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

# <a name="python"></a>[Python](#tab/python)
Temos um tutorial diferente para o padrão de monitorização em Python, por favor, veja-o [aqui.](durable-functions-monitor-python.md)

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert função de atividade

A função **E3_SendGoodWeatherAlert** utiliza a ligação Twilio para enviar uma mensagem SMS notificando o utilizador final de que é uma boa altura para uma caminhada.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Terá de instalar a `Microsoft.Azure.WebJobs.Extensions.Twilio` embalagem Nuget para executar o código de amostra.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A sua *function.jsé* simples:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

E aqui está o código que envia a mensagem SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

# <a name="python"></a>[Python](#tab/python)
Temos um tutorial diferente para o padrão de monitorização em Python, por favor, veja-o [aqui.](durable-functions-monitor-python.md)

---

## <a name="run-the-sample"></a>Executar o exemplo

Utilizando as funções desencadeadas por HTTP incluídas na amostra, pode iniciar a orquestração enviando o seguinte pedido HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

O **E3_Monitor** caso começa e consulta as condições meteorológicas atuais para a localização solicitada. Se o tempo estiver claro, chama uma função de atividade para enviar um alerta; caso contrário, define um temporizador. Quando o temporizador expirar, a orquestração será retomada.

Pode ver a atividade da orquestração olhando para os registos de funções no portal Azure Functions.

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

A orquestração termina assim que o seu tempo limite for atingido ou forem detetados céus claros. Também pode utilizar a `terminate` API dentro de outra função ou invocar o **webhookpostspostsposts** referido na resposta de 202 acima. Para utilizar o webhook, `{text}` substitua-o pela razão da rescisão antecipada. O URL HTTP POST será mais ou menos o seguinte:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou como utilizar funções duráveis para monitorizar o estado de uma fonte externa utilizando [temporizadores duradouros](durable-functions-timers.md) e lógica condicional. A próxima amostra mostra como usar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com a interação humana.

> [!div class="nextstepaction"]
> [Executar a amostra de interação humana](durable-functions-phone-verification.md)
