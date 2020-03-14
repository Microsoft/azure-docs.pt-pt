---
title: Referência da API do discurso ao texto (REST) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a API REST speech-to-text. Neste artigo, irá aprender sobre as opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: erhopf
ms.openlocfilehash: 873898ce321100edbaa800d2436d0413c06ce175
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220448"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de voz em texto

Como alternativa ao [SDK](speech-sdk.md)da Fala, o serviço de Fala permite converter o discurso-a-texto usando uma API REST. Cada ponto de final de acesso está associado uma região. Seu aplicativo requer uma chave de subscrição para o ponto final que pretende utilizar.

Antes de utilizar a API REST speech-to-text, entenda:

* Os pedidos que utilizam a API REST e transmitem áudio diretamente só podem conter até 60 segundos de áudio.
* A API de REST de voz em texto devolve apenas os resultados finais. Resultados parciais não são fornecidos.

Se o envio de áudio mais longo for um requisito para a sua aplicação, considere utilizar o [SDK](speech-sdk.md) de Fala ou uma API REST baseada em ficheiros, como [a transcrição](batch-transcription.md)do lote .

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

O ponto final para a Rest API tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua `<REGION_IDENTIFIER>` pelo identificador que corresponde à região da sua subscrição a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de idioma deve ser anexado ao URL para evitar receber um erro de 4xx HTTP. Por exemplo, a língua definida para o inglês americano usando o ponto final dos EUA Ocidental é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na cadeia de consulta da solicitação REST.

| Parâmetro | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `language` | Identifica o idioma falado que está a ser reconhecido. Ver [línguas apoiadas.](language-support.md#speech-to-text) | Necessário |
| `format` | Especifica o formato de resultado. Os valores aceites são `simple` e `detailed`. Os resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset`e `Duration`. Respostas detalhadas incluem vários resultados com valores de confiança e quatro diferentes representações. A definição predefinida é `simple`. | Opcional |
| `profanity` | Especifica como lidar com linguagem inapropriada nos resultados de reconhecimento. Os valores aceites são `masked`, que substitui a profanação por asteriscos, `removed`, que remove toda a profanação do resultado, ou `raw`, que inclui a profanação no resultado. A definição predefinida é `masked`. | Opcional |
| `cid` | Ao utilizar o [portal Custom Speech](how-to-custom-speech.md) para criar modelos personalizados, pode utilizar modelos personalizados através do id **endpoint** encontrado na página **de Implementação.** Utilize o **ID endpoint** como argumento para o parâmetro de corda de consulta `cid`. | Opcional |

## <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para pedidos de voz em texto.

|Cabeçalho| Descrição | Obrigatório / opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A sua chave de subscrição do serviço Speech. | Ou este cabeçalho ou `Authorization` é necessário. |
| `Authorization` | Um símbolo de autorização precedido pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Ou este cabeçalho ou `Ocp-Apim-Subscription-Key` é necessário. |
| `Content-type` | Descreve o formato e o codec de dados de áudio fornecidos. Os valores aceites são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Necessário |
| `Transfer-Encoding` | Especifica que os dados em segmentos de áudio são enviados, em vez de um único arquivo. Utilize este cabeçalho apenas se a segmentação de dados de áudio. | Opcional |
| `Expect` | Se utilizar transferência seleto, envie `Expect: 100-continue`. O serviço de Fala reconhece o pedido inicial e aguarda dados adicionais.| Necessário se o envio de dados de áudio em partes. |
| `Accept` | Se for fornecido, deve ser `application/json`. O serviço de Fala fornece resultados na JSON. Alguns quadros de pedidos fornecem um valor padrão incompatível. É uma boa prática incluir sempre `Accept`. | Opcional mas recomendado. |

## <a name="audio-formats"></a>Formatos de áudio

O áudio é enviado no corpo do pedido de `POST` HTTP. Tem de ser um dos formatos nesta tabela:

| Formato | Codec | Velocidade de transmissão | Taxa de exemplo  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16-bit  | 16 kHz, mono |
| OGG    | OPUS  | 16-bit  | 16 kHz, mono |

>[!NOTE]
>Os formatos acima são suportados através da REST API e WebSocket no serviço De Fala. O [SDK de Speech](speech-sdk.md) suporta atualmente o formato WAV com codec PCM, bem como outros [formatos](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Pedido de exemplo

O exemplo abaixo inclui o nome de anfitrião e cabeçalhos necessários. É importante observar que o serviço espera também os dados de áudio, o que não estão incluídos neste exemplo. Como mencionado anteriormente, segmentação recomenda, no entanto, não é necessário.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica o êxito ou erros comuns.

| Código de estado de HTTP | Descrição | Razão possível |
|------------------|-------------|-----------------|
| `100` | Continuar | O pedido inicial foi aceite. Continue com a enviar o resto dos dados. (Utilizado com transferência em pedaços) |
| `200` | OK | O pedido foi concluída com êxito; o corpo da resposta é um objeto JSON. |
| `400` | Pedido incorreto | Código linguístico não fornecido, nem uma linguagem suportada, ficheiro áudio inválido, etc. |
| `401` | Não autorizado | Chave de subscrição ou autorização token é inválido na região especificada ou ponto final inválido. |
| `403` | Proibido | Chave de subscrição ou autorização em falta token. |

## <a name="chunked-transfer"></a>Transferência em partes

A transferência em pedaços (`Transfer-Encoding: chunked`) pode ajudar a reduzir a latência do reconhecimento. Permite que o serviço de Fala comece a processar o ficheiro áudio enquanto é transmitido. A API REST não fornece resultados parciais ou provisórias.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro segmento deve conter cabeçalho do arquivo de áudio. `request` é um objeto `HttpWebRequest` ligado ao ponto final apropriado do REST. `audioFile` é o caminho para um ficheiro áudio no disco.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O formato `simple` inclui estes campos de alto nível.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Estatuto, como `Success` para o reconhecimento bem sucedido. Consulte a tabela seguinte.|
|`DisplayText`|O texto reconhecido após a capitalização, pontuação, normalização de texto inversa (conversão de texto falado para formas mais curtas, como 200 para "duzentos" ou "Dr. Smith" para "doctor smith"), e máscara de profanação. Apresentar apenas com êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos), no qual a voz reconhecido começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) do voz reconhecido no fluxo de áudio.|

O campo `RecognitionStatus` pode conter estes valores:

| Estado | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi bem sucedido e o campo `DisplayText` está presente. |
| `NoMatch` | Conversão de voz foi detetada no fluxo de áudio, mas sem palavras do idioma de destino foram correspondidas. Normalmente, significa que o idioma de reconhecimento é um idioma diferente daquele que o utilizador está a falar. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço excedeu o tempo aguardar por voz. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço excedeu o tempo aguardar por voz. |
| `Error` | O serviço de reconhecimento de obteve um erro interno e não foi possível continuar. Tente novamente se possível. |

> [!NOTE]
> Se o áudio for composto apenas por profanação, e o parâmetro de consulta de `profanity` for definido para `remove`, o serviço não devolve um resultado de fala.

O formato `detailed` inclui os mesmos dados que o formato `simple`, juntamente com `NBest`, uma lista de interpretações alternativas do mesmo resultado de reconhecimento. Estes resultados são classificados de muito provavelmente para o menos provável. A primeira entrada é a mesma que o resultado principal do reconhecimento.  Ao utilizar o formato `detailed`, `DisplayText` é fornecido como `Display` para cada resultado na lista `NBest`.

Cada objeto na lista de `NBest` inclui:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (sem confiança) para 1,0 (confiança total) |
| `Lexical` | O formulário léxico do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formulário ("canonical") normalizado de texto de inverso do texto reconhecido, com o telefone números, números, abreviaturas ("doutor smith" para "dr smith") e outras transformações aplicadas. |
| `MaskedITN` | O formulário ITN com a máscara de linguagem inapropriada aplicada, se solicitado. |
| `Display` | O formulário de apresentação do texto reconhecido, com a pontuação e capitalização adicionado. Este parâmetro é o mesmo que `DisplayText` fornecido quando o formato está definido para `simple`. |

## <a name="sample-responses"></a>Respostas de exemplo

Uma resposta típica para o reconhecimento `simple`:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Uma resposta típica para o reconhecimento `detailed`:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
