---
title: Interação humana e tempos limite no Durable Functions-Azure
description: Saiba como lidar com a interação humana e os tempos limite na extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4d8955517450ce3b4efdf30e2790e4be678dfc7b
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735184"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana no exemplo de verificação de telefone Durable Functions

Este exemplo demonstra como criar uma orquestração de [Durable Functions](durable-functions-overview.md) que envolve interação humana. Sempre que uma pessoa real estiver envolvida em um processo automatizado, o processo deverá ser capaz de enviar notificações para a pessoa e receber respostas de forma assíncrona. Ele também deve permitir a possibilidade de que a pessoa não esteja disponível. (Essa última parte é onde os tempos limite se tornam importantes.)

Este exemplo implementa um sistema de verificação de telefone baseado em SMS. Esses tipos de fluxos geralmente são usados ao verificar o número de telefone de um cliente ou a MFA (autenticação multifator). Esse é um exemplo poderoso, pois toda a implementação é feita usando algumas funções pequenas. Não é necessário nenhum armazenamento de dados externo, como um banco de dado.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

A verificação por telefone é usada para verificar se os usuários finais do seu aplicativo não são spammers e se eles dizem que eles são. A autenticação multifator é um caso de uso comum para proteger contas de usuário contra hackers. O desafio com a implementação de sua própria verificação de telefone é que ele requer uma **interação com estado** com um homem. Um usuário final normalmente recebe algum código (por exemplo, um número de 4 dígitos) e deve responder **em um período razoável**.

Os Azure Functions comuns são sem monitoração de estado (como muitos outros pontos de extremidade de nuvem em outras plataformas), portanto, esses tipos de interações envolvem o gerenciamento explícito de estado externamente em um banco de dados ou em algum outro repositório persistente. Além disso, a interação deve ser dividida em várias funções que podem ser coordenadas juntas. Por exemplo, você precisa de pelo menos uma função para decidir sobre um código, mantê-lo em algum lugar e enviá-lo para o telefone do usuário. Além disso, você precisa de pelo menos uma outra função para receber uma resposta do usuário e, de alguma forma, mapeá-la de volta para a chamada de função original a fim de fazer a validação do código. Um tempo limite também é um aspecto importante para garantir a segurança. Isso pode ser bastante complexo rapidamente.

A complexidade desse cenário é bastante reduzida quando você usa Durable Functions. Como você verá neste exemplo, uma função de orquestrador pode gerenciar a interação com estado com facilidade e sem envolver armazenamentos de dados externos. Como as funções de orquestrador são *duráveis*, esses fluxos interativos também são altamente confiáveis.

## <a name="configuring-twilio-integration"></a>Configurando a integração do twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo percorre as seguintes funções no aplicativo de exemplo:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

As seções a seguir explicam a configuração e o código que C# são usados para scripts e JavaScript. O código para desenvolvimento do Visual Studio é mostrado no final do artigo.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de verificação de SMS (Visual Studio Code e portal do Azure código de exemplo)

A função **E4_SmsPhoneVerification** usa o *Function. JSON* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Este é o código que implementa a função:

### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Depois de iniciada, essa função de orquestrador faz o seguinte:

1. Obtém um número de telefone para o qual ele *enviará* a notificação de SMS.
2. Chama **E4_SendSmsChallenge** para enviar uma mensagem SMS ao usuário e retorna de volta o código de desafio de 4 dígitos esperado.
3. Cria um temporizador durável que dispara 90 segundos a partir da hora atual.
4. Em paralelo com o temporizador, o aguarda um evento **SmsChallengeResponse** do usuário.

O usuário recebe uma mensagem SMS com um código de quatro dígitos. Eles têm de 90 segundos para enviar o mesmo código de 4 dígitos de volta para a instância de função do orquestrador para concluir o processo de verificação. Se eles enviarem o código errado, eles receberão três outras tentativas de obtê-lo (dentro da mesma janela de 90 segundos).

> [!NOTE]
> Ele pode não ser óbvio a princípio, mas essa função de orquestrador é totalmente determinística. Isso ocorre porque as `CurrentUtcDateTime` Propriedades (.net) `currentUtcDateTime` e (JavaScript) são usadas para calcular o tempo de expiração do temporizador e essas propriedades retornam o mesmo valor em cada reprodução neste ponto do código do orquestrador. Isso é importante para garantir que os mesmos `winner` resultados de cada chamada repetida `Task.WhenAny` para (.net) `context.df.Task.any` ou (JavaScript).

> [!WARNING]
> É importante cancelar os [temporizadores](durable-functions-timers.md) se você não precisar mais deles expirar, como no exemplo acima, quando uma resposta de desafio for aceita.

## <a name="send-the-sms-message"></a>Enviar a mensagem SMS

A função **E4_SendSmsChallenge** usa a associação twilio para enviar a mensagem SMS com o código de 4 dígitos para o usuário final. O *Function. JSON* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

E aqui está o código que gera o código de desafio de 4 dígitos e envia a mensagem SMS:

### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Essa função **E4_SendSmsChallenge** só é chamada uma vez, mesmo se o processo falha ou é reproduzido. Isso é bom porque você não quer que o usuário final receba várias mensagens SMS. O `challengeCode` valor de retorno é persistido automaticamente, portanto, a função de orquestrador sempre sabe qual é o código correto.

## <a name="run-the-sample"></a>Executar o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST:

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

A função de orquestrador recebe o número de telefone fornecido e envia imediatamente a ele uma mensagem SMS com um código &mdash; de verificação de 4 dígitos gerado aleatoriamente, por exemplo, *2168*. A função aguardará 90 segundos por uma resposta.

Para responder com o código, você pode usar [ `RaiseEventAsync` (.net) ou `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de outra função ou invocar o webhook **sendEventUrl** http post referenciado na resposta 202 acima `{eventName}` , substituindo pelo nome do evento, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se você enviar isso antes que o temporizador expire, a orquestração será concluída e `output` o campo é definido `true`como, indicando uma verificação bem-sucedida.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se você permitir que o temporizador expire, ou se você inserir o código errado quatro vezes, poderá consultar o status e ver uma `false` saída da função de orquestração, indicando que a verificação do telefone falhou.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Aqui está a orquestração como um único C# arquivo em um projeto do Visual Studio:

> [!NOTE]
> Será necessário instalar o `Microsoft.Azure.WebJobs.Extensions.Twilio` pacote NuGet para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Passos Seguintes

Este exemplo demonstrou alguns dos recursos avançados do Durable functions, notavelmente `WaitForExternalEvent` e. `CreateTimer` Você viu como elas podem ser combinadas com `Task.WaitAny` o para implementar um sistema de tempo limite confiável, que é geralmente útil para interagir com pessoas reais. Você pode aprender mais sobre como usar Durable Functions lendo uma série de artigos que oferecem uma cobertura detalhada de tópicos específicos.

> [!div class="nextstepaction"]
> [Ir para o primeiro artigo da série](durable-functions-bindings.md)
