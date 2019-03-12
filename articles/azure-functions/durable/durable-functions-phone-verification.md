---
title: Interação humana e tempos limite de funções duráveis - Azure
description: Saiba como lidar com interação humana e tempos limite na extensão de funções duráveis para as funções do Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 136b819f6bbbc1b546b66f54e771dbec8c71202c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548154"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana em funções duráveis - exemplo de verificação do telefone

Este exemplo demonstra como criar uma [funções duráveis](durable-functions-overview.md) orquestração que envolve a interação humana. Sempre que uma pessoa real está envolvida num processo automatizado, o processo deve ser capaz de enviar notificações à pessoa e receba respostas de forma assíncrona. Ele também tem de permitir a possibilidade de que a pessoa não está disponível. (Esta última parte é onde os tempos limite de importância.)

Este exemplo implementa um sistema de verificação do telefone com base em SMS. Esses tipos de fluxos geralmente são utilizados ao verificar o número de telefone de um cliente ou para autenticação multifator (MFA). Esse é um exemplo de poderoso, porque a implementação completa é feita com algumas pequenas funções. Nenhum armazenamento de dados externos, como uma base de dados, é necessário.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Verificação do telefone é usada para verificar que os utilizadores finais do seu aplicativo não são os remetentes de spam e que são quem dizem que eles são. Autenticação multifator é um caso de utilização comuns para proteger contas de utilizador contra hackers. O desafio com a implementação de sua própria verificação do telefone é que ela requer uma **interação com monitoração de estado** com um ser humano. Um utilizador final, geralmente, é fornecido um código (por exemplo, um número de 4 dígitos) e tem de responder **num período de tempo razoável**.

Funções comuns do Azure têm um estado específico (como são muitos outros pontos finais da cloud em outras plataformas), para que esses tipos de interações envolvem o gerenciamento explicitamente externamente num banco de dados ou alguns outros persistente armazenamento de Estados. Além disso, a interação deve ser dividida em várias funções que podem ser coordenadas em conjunto. Por exemplo, precisa de, pelo menos, uma função para decidir sobre um código, persisti-los em algum lugar e enviar para o telefone do utilizador. Além disso, precisa de pelo menos uma outra função para receber uma resposta do usuário e alguma forma mapeá-lo novamente para a chamada de função original para fazer a validação do código. Um tempo limite também é um aspecto importante para garantir a segurança. Isso pode ser bastante complexo rapidamente.

A complexidade desse cenário é significativamente reduzida quando utiliza funções duráveis. Como verá neste exemplo, uma função de orquestrador pode gerir a interação com monitoração de estado sem envolver quaisquer armazenamentos de dados externos e facilmente. Uma vez que as funções do orchestrator são *durável*, estes fluxos interativos também são altamente confiáveis.

## <a name="configuring-twilio-integration"></a>Configurar a integração do Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

As secções seguintes explicam a configuração e o código que são utilizados para c# scripts e JavaScript. O código para o desenvolvimento do Visual Studio é mostrado no final do artigo.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de verificação do SMS (código de exemplo do portal Visual Studio Code e o Azure)

O **E4_SmsPhoneVerification** função usa o padrão *Function* para as funções do orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Eis o código que implementa a função:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Depois de iniciada, esta função de orquestrador faz o seguinte:

1. Obtém um número de telefone para o qual será *enviar* a notificação de SMS.
2. Chamadas **E4_SendSmsChallenge** para enviar uma mensagem SMS para o utilizador e a devolve novamente o código de desafio de 4 dígitos esperado.
3. Cria um temporizador durável esse acionadores 90 segundos da hora atual.
4. Em paralelo com o temporizador, aguarda uma **SmsChallengeResponse** eventos do usuário.

O utilizador recebe uma mensagem SMS com um código de quatro dígitos. Eles têm 90 segundos para enviar esse mesmo código de 4 dígitos para a instância de função do orchestrator para concluir o processo de verificação. Se submeter o código errado, eles recebem um três tentativas adicionais para obtê-la (dentro da mesma janela de 90 segundos).

> [!NOTE]
> Talvez não seja óbvio a princípio, mas este orchestrator função é inteiramente determinística. Isto acontece porque o `CurrentUtcDateTime` (.NET) e `currentUtcDateTime` propriedades (JavaScript) são utilizadas para calcular o tempo de expiração do temporizador, e essas propriedades retornam o mesmo valor em cada repetição neste momento no código do orchestrator. Isso é importante para garantir que o mesmo `winner` resulta de todas as chamadas repetidas à `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript).

> [!WARNING]
> É importante [Cancelar temporizadores](durable-functions-timers.md) se não precisa mais deles para expirar, como no exemplo acima quando uma resposta de desafio é aceite.

## <a name="send-the-sms-message"></a>Enviar a mensagem SMS

O **E4_SendSmsChallenge** função usa a vinculação do Twilio para enviar a mensagem SMS com o código de 4 dígitos para o utilizador final. O *Function* é definida da seguinte forma:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

E aqui está o código que gera o código de desafio de 4 dígitos e envia a mensagem SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (funciona apenas 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Isso **E4_SendSmsChallenge** função apenas é chamada uma vez, mesmo que o processo falhar ou obtém repetidos. Isso é bom porque não quer o utilizador final receber várias mensagens SMS. O `challengeCode` retornar o valor é mantido automaticamente, para que a função de orquestrador sempre saiba o que é o código correto.

## <a name="run-the-sample"></a>Executar o exemplo

Utilizar as funções de acionada por HTTP incluídas no exemplo, pode começar a orquestração ao enviar o pedido de HTTP POST seguinte:

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
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A função de orquestrador recebe o número de telefone fornecido e imediatamente envia uma mensagem SMS com um código de verificação de 4 dígitos gerado aleatoriamente &mdash; por exemplo, *2168*. A função, em seguida, aguarda 90 segundos por uma resposta.

Para responder com o código, pode usar [ `RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de outra função ou invocar o **sendEventUrl** webhook de HTTP POST referenciado na resposta 202 acima , substituindo `{eventName}` com o nome do evento, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se enviar isso antes do timer expira, a orquestração é concluída e o `output` campo é definido como `true`, indicando uma verificação com êxito.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se deixar que o timer expirar ou, se introduzir o código errado quatro vezes, pode consultar o estado e ver um `false` função de orquestração de saída, que indica que a verificação do telefone falhou.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis a orquestração como um único arquivo c# num projeto do Visual Studio:

> [!NOTE]
> Terá de instalar o `Microsoft.Azure.WebJobs.Extensions.Twilio` pacote Nuget para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo tenha demonstrado algumas das capacidades avançadas de funções durável, notavelmente `WaitForExternalEvent` e `CreateTimer`. Já viu como eles podem ser combinados com `Task.WaitAny` para implementar um sistema de tempo limite confiável, o que é muitas vezes útil para interagir com pessoas reais. Pode saber mais sobre como utilizar funções duráveis ao ler uma série de artigos que oferecem uma cobertura completa do tópicos específicos.

> [!div class="nextstepaction"]
> [Vá para o primeiro artigo da série](durable-functions-bindings.md)
