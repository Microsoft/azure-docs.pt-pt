---
title: Webhooks - serviços de voz
titlesuffix: Azure Cognitive Services
description: Os Webhooks são HTTP chamadas de retorno ideal para otimizar a sua solução ao lidar com longa processos como imports, adaptação, testes de precisão ou transcrições de ficheiros de longa execução em execução.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: a100049ddfc9d4859e303546c1b10e814cf96ebb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606212"
---
# <a name="webhooks-for-speech-services"></a>Webhooks para serviços de voz

Os Webhooks são como retornos de chamada HTTP que permitem ao aplicativo aceitar dados dos serviços de voz quando ela se tornar disponível. Através de webhooks, pode otimizar a utilização de nossas APIs REST, eliminando a necessidade de consultar continuamente por uma resposta. As próximas seções, aprenderá como utilizar webhooks com os serviços de voz.

## <a name="supported-operations"></a>Operações suportadas

Os serviços de voz suportam webhooks para todas as operações de longa execução. Cada uma das operações indicadas abaixo pode acionar uma chamada de retorno HTTP após a conclusão.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Em seguida, vamos criar um webhook.

## <a name="create-a-webhook"></a>Criar um webhook

Vamos criar um webhook para uma transcrição offline. O cenário: um utilizador tiver um ficheiro de áudio de longa execução que eles gostariam de transcrição de forma assíncrona com a API de transcrição do Batch.

Webhooks que podem ser criados ao fazer um pedido POST para https://\<região\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks.

Parâmetros de configuração para o pedido são fornecidos como JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Todos os pedidos POST para a API de transcrição do lote requerem um `name`. O `description` e `properties` parâmetros são opcionais.

O `Active` propriedade é utilizada para mudar a chamada de retorno para o URL e desativar sem ter de eliminar e recriar o registo do webhook. Se só precisa de retorno de chamada uma vez depois do processo foi concluída, em seguida, elimine o webhook e comutador a `Active` propriedade como false.

O tipo de evento `TranscriptionCompletion` é fornecido na matriz de eventos. Ele será chamada de retorno para o ponto final quando uma transcrição entra num Estado terminal (`Succeeded` ou `Failed`). Retorno de chamada para o URL registado, o pedido irá conter uma `X-MicrosoftSpeechServices-Event` cabeçalho que contém um dos tipos de eventos registrados. Há uma solicitação por tipo de eventos registrados.

Existe um tipo de evento que não é possível subscrever. É o `Ping` tipo de evento. É enviado um pedido com este tipo para o URL quando concluída a criação de um webhook ao utilizar o URL de ping (ver abaixo).  

Na configuração, o `url` propriedade é necessária. Pedidos POST são enviados para este URL. O `secret` é utilizado para criar um hash de SHA256 da carga, com o segredo como uma chave HMAC. O hash está definido como o `X-MicrosoftSpeechServices-Signature` cabeçalho de retorno de chamada para o URL registado. Este cabeçalho é codificado em Base64.

Este exemplo ilustra como validar um payload utilizando C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
Neste trecho de código, o `secret` é descodificado e validado. Também observará que o tipo de evento do webhook foi mudado. Atualmente, existe um evento por transcrição concluída. As repetições de código cinco vezes para cada evento (com um segundo de atraso) antes de desistir.

### <a name="other-webhook-operations"></a>Outras operações de webhook

Para obter webhooks tudo registados: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Para obter um webhook específico: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Para remover um webhook específico: ELIMINAR https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> No exemplo acima, a região é 'westus'. Isso deve ser substituído por região em que criou seu recurso de serviços de voz no portal do Azure.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping corpo: vazio

Envia um pedido POST para o URL registado. O pedido contém um `X-MicrosoftSpeechServices-Event` cabeçalho com um ping de valor. Se o webhook foi registado com um segredo, ele conterá uma `X-MicrosoftSpeechServices-Signature` cabeçalho com um hash de SHA256 do payload com o segredo como chave do HMAC de. O hash é codificado em Base64.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test corpo: vazio

Envia um pedido POST para o URL registado se uma entidade para o tipo de evento subscrito (transcrição) está presente no sistema e está no estado apropriado. O payload será gerado a partir da última entidade que poderia ter chamado o hook de web. Se nenhuma entidade estiver presente, a mensagem irá responder com 204. Se um pedido de teste pode ser feito, irá responder com 200. O corpo do pedido é da mesma forma que no pedido GET para uma entidade específica, que o hook de web tiver subscrito para (por exemplo, transcrição). O pedido terá o `X-MicrosoftSpeechServices-Event` e `X-MicrosoftSpeechServices-Signature` cabeçalhos conforme descrito anteriormente.

### <a name="run-a-test"></a>Executar um teste

Um breve teste pode ser feito com o Web site https://bin.webhookrelay.com. A partir daí, pode obter chamada fazer uma cópia de URLs para passar como parâmetro para o HTTP POST para criar um webhook descrito anteriormente no documento.

Clique em "Criar Bucket" e siga na tela instruções para obter um gancho. Em seguida, utilize as informações fornecidas nesta página para registar o hook com o serviço de voz. O payload de uma mensagem de retransmissão – em resposta à realização de uma transcrição – será semelhante ao seguinte:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
A mensagem contém o URL de gravação e os modelos usados de transcrever essa gravação.

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
