---
title: Interação humana e intervalos em Funções Duráveis - Azure
description: Aprenda a lidar com a interação humana e os intervalos na extensão das Funções Duráveis para funções azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0c16ef092c30a94cd04b55c91d3643ac29b82be0
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562110"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana em Funções Duráveis - Amostra de verificação do telefone

Esta amostra demonstra como construir uma orquestração de [Funções Duráveis](durable-functions-overview.md) que envolve a interação humana. Sempre que uma pessoa real esteja envolvida num processo automatizado, o processo deve poder enviar notificações à pessoa e receber respostas assincronicamente. Deve também permitir a possibilidade de a pessoa não estar disponível. (Esta última parte é onde os intervalos tornam-se importantes.)

Esta amostra implementa um sistema de verificação de telefone baseado em SMS. Estes tipos de fluxos são frequentemente utilizados quando se verifica o número de telefone de um cliente ou para a autenticação de vários fatores (MFA). É um exemplo poderoso porque toda a implementação é feita usando algumas pequenas funções. Não é necessária nenhuma loja de dados externa, como uma base de dados.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

A verificação do telefone é usada para verificar se os utilizadores finais da sua aplicação não são spammers e que são quem dizem ser. A autenticação de vários fatores é um caso comum para proteger as contas dos utilizadores de hackers. O desafio com a implementação da verificação do seu próprio telefone é que requer uma **interação audativa** com um ser humano. Um utilizador final é normalmente fornecido algum código (por exemplo, um número de 4 dígitos) e deve responder **num período de tempo razoável**.

As Funções Azure ordinárias são apátridas (tal como muitos outros pontos finais em nuvem noutras plataformas), por isso este tipo de interações envolvem gerir explicitamente o Estado externamente numa base de dados ou em alguma outra loja persistente. Além disso, a interação deve ser dividida em múltiplas funções que podem ser coordenadas em conjunto. Por exemplo, é necessário pelo menos uma função para decidir um código, persistir em algum lugar e enviá-lo para o telefone do utilizador. Além disso, precisa de pelo menos uma outra função para receber uma resposta do utilizador e, de alguma forma, mapeá-la de volta para a chamada de função original para fazer a validação do código. Um prazo é também um aspeto importante para garantir a segurança. Pode ficar bastante complexo rapidamente.

A complexidade deste cenário é muito reduzida quando se utilizam Funções Duráveis. Como pode ver nesta amostra, uma função orquestradora pode gerir a interação imponente facilmente e sem envolver quaisquer lojas de dados externas. Como as funções orquestradoras são *duráveis,* estes fluxos interativos também são altamente fiáveis.

## <a name="configuring-twilio-integration"></a>Configurar a integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo percorre as seguintes funções na aplicação da amostra:

* `E4_SmsPhoneVerification`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que executa o processo de verificação do telefone, incluindo a gestão de intervalos e repetições.
* `E4_SendSmsChallenge`: Uma [função de orquestrador](durable-functions-bindings.md#activity-trigger) que envia um código por mensagem de texto.

### <a name="e4_smsphoneverification-orchestrator-function"></a>função orquestradora E4_SmsPhoneVerification

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Pode não ser óbvio no início, mas esta função orquestradora é completamente determinista. É determinista porque a propriedade `CurrentUtcDateTime` é usada para calcular o tempo de validade do temporizador, e devolve o mesmo valor em cada repetição neste ponto do código orquestrador. Este comportamento é importante para garantir que o mesmo `winner` resulta de cada chamada repetida para `Task.WhenAny`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função **E4_SmsPhoneVerification** utiliza a *função padrão.json* para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Aqui está o código que implementa a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Pode não ser óbvio no início, mas esta função orquestradora é completamente determinista. É determinista porque a propriedade `currentUtcDateTime` é usada para calcular o tempo de validade do temporizador, e devolve o mesmo valor em cada repetição neste ponto do código orquestrador. Este comportamento é importante para garantir que o mesmo `winner` resulta de cada chamada repetida para `context.df.Task.any`.

---

Uma vez iniciado, esta função orquestradora faz o seguinte:

1. Obtém um número de telefone para o qual *enviará* a notificação sMS.
2. As chamadas **E4_SendSmsChallenge** enviar uma mensagem SMS ao utilizador e devolve o código de desafio de 4 dígitos esperado.
3. Cria um temporizador durável que dispara 90 segundos a partir do tempo atual.
4. Em paralelo com o temporizador, aguarda pelo utilizador um evento **SmsChallengeResponse.**

O utilizador recebe uma mensagem SMS com um código de quatro dígitos. Têm 90 segundos para enviar o mesmo código de quatro dígitos de volta para a instância de função orquestradora para completar o processo de verificação. Se submeterem o código errado, recebem mais três tentativas para acertar (dentro da mesma janela de 90 segundos).

> [!WARNING]
> É importante cancelar os [tempos](durable-functions-timers.md) se já não precisar que expirem, como no exemplo acima, quando uma resposta ao desafio é aceite.

## <a name="e4_sendsmschallenge-activity-function"></a>função de atividade E4_SendSmsChallenge

A função **E4_SendSmsChallenge** utiliza a ligação Twilio para enviar a mensagem SMS com o código de quatro dígitos para o utilizador final.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Terá de instalar o pacote `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget para executar o código da amostra.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A *função.json* é definida da seguinte forma:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

E aqui está o código que gera o código de desafio de quatro dígitos e envia a mensagem SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>Executar o exemplo

Utilizando as funções desencadeadas pelo HTTP incluídas na amostra, pode iniciar a orquestração enviando o seguinte pedido HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A função de orquestrador recebe o número de telefone fornecido e envia-lhe imediatamente uma mensagem SMS com um código de verificação de 4 dígitos gerado aleatoriamente &mdash;, por exemplo, *2168*. A função espera então 90 segundos para uma resposta.

Para responder com o código, pode utilizar [`RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de outra função ou invocar o **webhook do Envio** Http POST referenciado na resposta de 202 acima, substituindo `{eventName}` pelo nome do evento, `SmsChallengeResponse`:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se enviar isto antes de expirar o temporizador, a orquestração completa e o campo `output` está definido para `true`, indicando uma verificação bem sucedida.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se deixar expirar o temporizador, ou se introduzir o código errado quatro vezes, pode consultar o estado e ver uma `false` saída da função de orquestração, indicando que a verificação do telefone falhou.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou algumas das capacidades avançadas das Funções Duráveis, nomeadamente `WaitForExternalEvent` e `CreateTimer` APIs. Você viu como estes podem ser combinados com `Task.WaitAny` implementar um sistema de tempo livre fiável, que é muitas vezes útil para interagir com pessoas reais. Você pode aprender mais sobre como usar Funções Duráveis lendo uma série de artigos que oferecem uma cobertura aprofundada de tópicos específicos.

> [!div class="nextstepaction"]
> [Ir ao primeiro artigo da série](durable-functions-bindings.md)
