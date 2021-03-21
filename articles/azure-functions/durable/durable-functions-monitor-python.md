---
title: Monitores em Funções Duradouras (Python) - Azure
description: Saiba como implementar um monitor de estado utilizando a extensão de Funções Duradouras para Funções Azure (Python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434768"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Monitor cenário em Funções Duradouras - Amostra de monitorização do Problema GitHub

O padrão do monitor refere-se a um processo recorrente flexível num fluxo de trabalho - por exemplo, a votação até que determinadas condições sejam satisfeitas. Este artigo explica uma amostra que utiliza funções duradouras para implementar a monitorização.

## <a name="prerequisites"></a>Pré-requisitos

* [Complete o artigo de arranque rápido](quickstart-python-vscode.md)
* [Clone ou descarregue o projeto de amostras do GitHub](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

Esta amostra monitoriza a contagem de problemas num repo GitHub e alerta o utilizador se houver mais de 3 questões em aberto. Pode utilizar uma função ativada regularmente pelo temporizador para solicitar que a emissão aberta conte a intervalos regulares. No entanto, um dos problemas com esta abordagem é **a gestão vitalícia.** Se apenas um alerta for enviado, o monitor precisa de se desativar após a deteção de 3 ou mais problemas. O padrão de monitorização pode terminar a sua própria execução, entre outros benefícios:

* Os monitores funcionam em intervalos, não em horários: um gatilho do temporizador *funciona* a cada hora; um monitor *espera* uma hora entre as ações. As ações de um monitor não se sobreporão a menos que sejam especificadas, o que pode ser importante para tarefas de longa duração.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode mudar com base em alguma condição.
* Os monitores podem terminar quando alguma condição é satisfeita ou ser terminada por outro processo.
* Os monitores podem tomar parâmetros. A amostra mostra como o mesmo processo de monitorização de repo-monitor pode ser aplicado a qualquer repo e número de telefone público solicitado do GitHub.
* Os monitores são escaláveis. Como cada monitor é uma instância de orquestração, vários monitores podem ser criados sem ter que criar novas funções ou definir mais código.
* Os monitores integram-se facilmente em fluxos de trabalho maiores. Um monitor pode ser uma secção de uma função de orquestração mais complexa, ou uma [sub-orquestração.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Configuração

### <a name="configuring-twilio-integration"></a>Configuração da integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação da amostra:

* `E3_Monitor`: Uma [função orquestradora](durable-functions-bindings.md#orchestration-trigger) que chama `E3_TooManyOpenIssues` periodicamente. Chama `E3_SendAlert` se o valor de devolução `E3_TooManyOpenIssues` é `True` .
* `E3_TooManyOpenIssues`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que verifica se um repositório tem demasiadas questões em aberto. Para fins de despromodóia, consideramos que ter mais de 3 questões em aberto é demasiado.
* `E3_SendAlert`: Uma função de atividade que envia uma mensagem SMS via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>função orquestrador E3_Monitor


A função **E3_Monitor** utiliza o *function.js* padrão para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Aqui está o código que implementa a função:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Esta função orquestradora executa as seguintes ações:

1. Obtém o *repo* para monitorizar e o *número de telefone* para o qual enviará uma notificação sms.
2. Determina o tempo de validade do monitor. A amostra usa um valor codificado para a brevidade.
3. As chamadas **E3_TooManyOpenIssues** para determinar se há demasiadas questões em aberto na repo solicitada.
4. Se houver demasiados problemas, as chamadas **E3_SendAlert** enviar uma notificação sms para o número de telefone solicitado.
5. Cria um temporizador durável para retomar a orquestração no próximo intervalo de votação. A amostra usa um valor codificado para a brevidade.
6. Continua a funcionar até que o tempo utc atual passe o tempo de validade do monitor, ou um alerta SMS é enviado.

Vários casos de orquestradores podem ser executados simultaneamente, chamando a função de orquestrador várias vezes. O repo para monitorar e o número de telefone para enviar um alerta SMS pode ser especificado. Por fim, note que a função do orquestrador *não* está a funcionar enquanto espera pelo temporizador, para que não seja cobrado por isso.


### <a name="e3_toomanyopenissues-activity-function"></a>E3_TooManyOpenIssues função de atividade

Tal como acontece com outras amostras, as funções de atividade do ajudante são funções regulares que utilizam a ligação do `activityTrigger` gatilho. A função **E3_TooManyOpenIssues** recebe uma lista de questões atualmente em aberto no repo e determina se existem "demasiados" deles: mais de 3 de acordo com a nossa amostra.


O *function.jsem diante* é definido da seguinte forma:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

E aqui está a implementação.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>E3_SendAlert função de atividade

A função **E3_SendAlert** utiliza a ligação Twilio para enviar uma mensagem SMS notificando o utilizador final de que existem pelo menos 3 questões em aberto à espera de uma resolução.


A sua *function.jsé* simples:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

E aqui está o código que envia a mensagem SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>Executar o exemplo

Vai precisar de uma conta [GitHub.](https://github.com/) Com ele, crie um repositório público temporário ao que possa abrir questões.

Utilizando as funções desencadeadas por HTTP incluídas na amostra, pode iniciar a orquestração enviando o seguinte pedido HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Por exemplo, se o seu nome de utilizador GitHub é `foo` e o seu repositório `bar` é, então o seu valor `"repo"` deve ser `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

O **E3_Monitor** exemplo começa e consulta o repo fornecido para questões abertas. Se forem encontrados pelo menos 3 questões em aberto, chama uma função de atividade para enviar um alerta; caso contrário, define um temporizador. Quando o temporizador expirar, a orquestração será retomada.

Pode ver a atividade da orquestração olhando para os registos de funções no portal Azure Functions.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

A orquestração terminará assim que o seu tempo limite for atingido ou forem detetadas mais de 3 questões em aberto. Também pode utilizar a `terminate` API dentro de outra função ou invocar o **webhookpostspostsposts** referido na resposta de 202 acima. Para utilizar o webhook, `{text}` substitua-o pela razão da rescisão antecipada. O URL HTTP POST será mais ou menos o seguinte:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou como utilizar funções duráveis para monitorizar o estado de uma fonte externa utilizando [temporizadores duradouros](durable-functions-timers.md) e lógica condicional. A próxima amostra mostra como usar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com a interação humana.

> [!div class="nextstepaction"]
> [Executar a amostra de interação humana](durable-functions-phone-verification.md)
