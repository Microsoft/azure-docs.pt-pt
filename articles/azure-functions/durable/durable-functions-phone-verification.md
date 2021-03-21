---
title: Interação humana e timeouts em Funções Duradouras - Azure
description: Aprenda a lidar com a interação humana e os intervalos de tempo na extensão de Funções Duradouras para Funções Azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: dd7f8416b2f4520ec8e94c8608f753f7412afc4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627377"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana em Funções Duradouras - Amostra de verificação de telefone

Esta amostra demonstra como construir uma orquestração [de Funções Duradouras](durable-functions-overview.md) que envolva interação humana. Sempre que uma pessoa real esteja envolvida num processo automatizado, o processo deve ser capaz de enviar notificações à pessoa e receber respostas assíncroneas. Deve também permitir a possibilidade de a pessoa estar indisponível. (Esta última parte é onde os intervalos se tornam importantes.)

Esta amostra implementa um sistema de verificação de telefone baseado em SMS. Estes tipos de fluxos são frequentemente utilizados quando se verifica o número de telefone de um cliente ou para a autenticação de vários fatores (MFA). É um exemplo poderoso porque toda a implementação é feita usando algumas pequenas funções. Não é necessária nenhuma loja de dados externo, como uma base de dados.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

A verificação telefónica é usada para verificar se os utilizadores finais da sua aplicação não são spammers e que são quem dizem ser. A autenticação multi-factor é um caso de uso comum para proteger as contas dos utilizadores de hackers. O desafio de implementar a sua própria verificação de telefone é que requer uma **interação imponente** com um ser humano. Um utilizador final é normalmente fornecido algum código (por exemplo, um número de 4 dígitos) e deve responder **num período de tempo razoável**.

As Funções Ordinárias de Azure são apátridas (como são muitos outros pontos finais em nuvem noutras plataformas), pelo que estes tipos de interações envolvem a gestão explícita do Estado externamente numa base de dados ou em alguma outra loja persistente. Além disso, a interação deve ser dividida em múltiplas funções que podem ser coordenadas em conjunto. Por exemplo, precisa de pelo menos uma função para decidir sobre um código, persistindo-o em algum lugar, e enviando-o para o telefone do utilizador. Além disso, precisa de pelo menos uma outra função para receber uma resposta do utilizador e de alguma forma mapeá-la de volta para a chamada de função original para fazer a validação do código. Um intervalo é também um aspeto importante para garantir a segurança. Pode ser bastante complexo rapidamente.

A complexidade deste cenário é muito reduzida quando se utiliza Funções Duradouras. Como você verá nesta amostra, uma função orquestradora pode gerir a interação imponente facilmente e sem envolver quaisquer lojas de dados externas. Como as funções de orquestradores são *duráveis,* estes fluxos interativos também são altamente fiáveis.

## <a name="configuring-twilio-integration"></a>Configuração da integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo percorre as seguintes funções na aplicação da amostra:

* `E4_SmsPhoneVerification`: [Função orquestradora](durable-functions-bindings.md#orchestration-trigger) que executa o processo de verificação do telefone, incluindo a gestão de intervalos e retréis.
* `E4_SendSmsChallenge`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que envia um código por mensagem de texto.

> [!NOTE]
> A `HttpStart` função na aplicação da [amostra e no quickstart](#prerequisites) atua como cliente da [Orquestração,](durable-functions-bindings.md#orchestration-client) o que despoleta a função de orquestrador.

### <a name="e4_smsphoneverification-orchestrator-function"></a>função orquestrador E4_SmsPhoneVerification

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Pode não ser óbvio no início, mas este orquestrador não viola o [constrangimento da orquestração determinística.](durable-functions-code-constraints.md) É determinístico porque a `CurrentUtcDateTime` propriedade é usada para calcular o tempo de validade do temporizador, e devolve o mesmo valor em cada repetição neste ponto no código orquestrador. Este comportamento é importante para garantir que o mesmo `winner` resulta de cada chamada repetida para `Task.WhenAny` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função **E4_SmsPhoneVerification** utiliza o *function.js* padrão para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Aqui está o código que implementa a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Pode não ser óbvio no início, mas este orquestrador não viola o [constrangimento da orquestração determinística.](durable-functions-code-constraints.md) É determinístico porque a `currentUtcDateTime` propriedade é usada para calcular o tempo de validade do temporizador, e devolve o mesmo valor em cada repetição neste ponto no código orquestrador. Este comportamento é importante para garantir que o mesmo `winner` resulta de cada chamada repetida para `context.df.Task.any` .

# <a name="python"></a>[Python](#tab/python)

A função **E4_SmsPhoneVerification** utiliza o *function.js* padrão para funções orquestradoras.

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/function.json)]

Aqui está o código que implementa a função:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/\_\_init\_\_.py)]

> [!NOTE]
> Pode não ser óbvio no início, mas este orquestrador não viola o [constrangimento da orquestração determinística.](durable-functions-code-constraints.md) É determinístico porque a `currentUtcDateTime` propriedade é usada para calcular o tempo de validade do temporizador, e devolve o mesmo valor em cada repetição neste ponto no código orquestrador. Este comportamento é importante para garantir que o mesmo `winner` resulta de cada chamada repetida para `context.df.Task.any` .

---

Uma vez iniciado, esta função orquestradora faz o seguinte:

1. Obtém um número de telefone para o qual *enviará* a notificação por SMS.
2. As chamadas **E4_SendSmsChallenge** para enviar uma mensagem SMS ao utilizador e devolve o código de desafio esperado de 4 dígitos.
3. Cria um temporizador durável que dispara 90 segundos a partir da hora atual.
4. Paralelamente ao temporizador, aguarda-se um evento **SmsChallengeResponse** do utilizador.

O utilizador recebe uma mensagem SMS com um código de quatro dígitos. Têm 90 segundos para enviar o mesmo código de quatro dígitos de volta para a instância de função do orquestrador para completar o processo de verificação. Se apresentarem o código errado, obtêm mais três tentativas para o acertar (dentro da mesma janela de 90 segundos).

> [!WARNING]
> É importante [cancelar os temporizadores](durable-functions-timers.md) se já não precisar que expirem, como no exemplo acima quando uma resposta de desafio é aceite.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge função de atividade

A função **E4_SendSmsChallenge** utiliza a ligação Twilio para enviar a mensagem SMS com o código de quatro dígitos para o utilizador final.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Terá de instalar a `Microsoft.Azure.WebJobs.Extensions.Twilio` embalagem Nuget para executar o código de amostra.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O *function.jsem diante* é definido da seguinte forma:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

E aqui está o código que gera o código de desafio de quatro dígitos e envia a mensagem SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

# <a name="python"></a>[Python](#tab/python)

O *function.jsem diante* é definido da seguinte forma:

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/function.json)]

E aqui está o código que gera o código de desafio de quatro dígitos e envia a mensagem SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>Executar o exemplo

Utilizando as funções desencadeadas por HTTP incluídas na amostra, pode iniciar a orquestração enviando o seguinte pedido HTTP POST:

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

A função orquestrador recebe o número de telefone fornecido e envia-lhe imediatamente uma mensagem SMS com um código de verificação de 4 dígitos gerado &mdash; aleatoriamente, por exemplo, *2168*. Em seguida, a função aguarda 90 segundos para obter uma resposta.

Para responder com o código, pode utilizar [ `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de outra função ou invocar o **webhook sendEventUrl** HTTP POST referenciado na resposta de 202 acima, substituindo `{eventName}` pelo nome do evento, `SmsChallengeResponse`

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se o enviar antes do temporizador expirar, a orquestração termina e o `output` campo está definido para , `true` indicando uma verificação bem sucedida.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se deixar o temporizador expirar, ou se introduzir o código errado quatro vezes, pode consultar o estado e ver uma saída da `false` função de orquestração, indicando que a verificação do telefone falhou.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Passos seguintes

Esta amostra demonstrou algumas das capacidades avançadas de Funções Duradouras, nomeadamente `WaitForExternalEvent` e `CreateTimer` APIs. Já viste como estes podem ser combinados `Task.WaitAny` para implementar um sistema de tempo limite fiável, que é muitas vezes útil para interagir com pessoas reais. Você pode aprender mais sobre como usar Funções Duráveis lendo uma série de artigos que oferecem uma cobertura aprofundada de tópicos específicos.

> [!div class="nextstepaction"]
> [Vá ao primeiro artigo da série](durable-functions-bindings.md)
