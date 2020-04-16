---
title: Referência da API do discurso ao texto (REST) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a API REST speech-to-text. Neste artigo, você vai aprender sobre opções de autorização, opções de consulta, como estruturar um pedido e receber uma resposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: fbb4d114d1fee21d7950e53b06fc16c96b5c930b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400187"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de voz em texto

Como alternativa ao [SDK](speech-sdk.md)da Fala, o serviço de Fala permite converter o discurso-a-texto usando uma API REST. Cada ponto final acessível está associado a uma região. A sua aplicação requer uma chave de subscrição para o ponto final que pretende utilizar. A API REST é muito limitada, e só deve ser usada em casos se o [SDK da fala](speech-sdk.md) não puder.

Antes de utilizar a API REST speech-to-text, entenda:

* Os pedidos que utilizam a API REST e transmitem áudio diretamente só podem conter até 60 segundos de áudio.
* A API do discurso-a-texto REST apenas devolve os resultados finais. Os resultados parciais não são fornecidos.

Se o envio de áudio mais longo for um requisito para a sua aplicação, considere utilizar o [SDK](speech-sdk.md) de Fala ou uma API REST baseada em ficheiros, como [a transcrição](batch-transcription.md)do lote .

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiões e pontos finais

O ponto final para a Rest API tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Substitua-o `<REGION_IDENTIFIER>` pelo identificador que corresponde à região da sua subscrição a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro de idioma deve ser anexado ao URL para evitar receber um erro de 4xx HTTP. Por exemplo, a língua definida para o inglês `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`americano usando o ponto final dos EUA Ocidental é: .

## <a name="query-parameters"></a>Parâmetros de consulta

Estes parâmetros podem ser incluídos na cadeia de consulta do pedido REST.

| Parâmetro | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a língua falada que está a ser reconhecida. Ver [línguas apoiadas.](language-support.md#speech-to-text) | Necessário |
| `format` | Especifica o formato de resultados. Os valores aceites são `simple` e. `detailed` Os resultados `DisplayText` `Offset`simples `Duration`incluem, `RecognitionStatus`e . As respostas detalhadas incluem múltiplos resultados com valores de confiança e quatro representações diferentes. A predefinição é `simple`. | Opcional |
| `profanity` | Especifica como lidar com a profanação nos resultados de reconhecimento. Os valores aceites são `masked`, que substitui `removed`a profanação por asteriscos, `raw`que remove toda a profanação do resultado, ou , que inclui a profanação no resultado. A predefinição é `masked`. | Opcional |
| `cid` | Ao utilizar o [portal Custom Speech](how-to-custom-speech.md) para criar modelos personalizados, pode utilizar modelos personalizados através do id **endpoint** encontrado na página **de Implementação.** Utilize o **ID endpoint** como `cid` argumento para o parâmetro de corda de consulta. | Opcional |

## <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista seletiva e cabeçalhos opcionais para pedidos de discurso a texto.

|Cabeçalho| Descrição | Obrigatório / Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A sua chave de subscrição do serviço Speech. | Ou este `Authorization` cabeçalho ou é necessário. |
| `Authorization` | Um símbolo de autorização precedido pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Ou este `Ocp-Apim-Subscription-Key` cabeçalho ou é necessário. |
| `Content-type` | Descreve o formato e o codec dos dados áudio fornecidos. Os valores aceites são `audio/wav; codecs=audio/pcm; samplerate=16000` e. `audio/ogg; codecs=opus` | Necessário |
| `Transfer-Encoding` | Especifica que os dados áudio em pedaços estão a ser enviados, em vez de um único ficheiro. Utilize este cabeçalho apenas se os dados áudio de bits de dados. | Opcional |
| `Expect` | Se utilizar transferência saqueada, envie `Expect: 100-continue`. O serviço de Fala reconhece o pedido inicial e aguarda dados adicionais.| Necessário se enviar dados de áudio em pedaços. |
| `Accept` | Se fornecido, deve `application/json`ser. O serviço de Fala fornece resultados na JSON. Alguns quadros de pedidos fornecem um valor padrão incompatível. É uma boa prática `Accept`incluir sempre. | Opcional, mas recomendado. |

## <a name="audio-formats"></a>Formatos áudio

O áudio é enviado no `POST` corpo do pedido HTTP. Deve estar num dos formatos desta tabela:

| Formato | Codec | Bitrate | Taxa de amostragem  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16 bits  | 16 kHz, mono |
| OGG    | OPUS  | 16 bits  | 16 kHz, mono |

>[!NOTE]
>Os formatos acima são suportados através da REST API e WebSocket no serviço De Fala. O [SDK de Speech](speech-sdk.md) suporta atualmente o formato WAV com codec PCM, bem como outros [formatos](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Pedido de amostra

A amostra abaixo inclui o nome de anfitrião e os cabeçalhos necessários. É importante notar que o serviço também espera dados áudio, que não estão incluídos nesta amostra. No entanto, como mencionado anteriormente, recomenda-se a chunking.

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

O código de estado HTTP para cada resposta indica sucesso ou erros comuns.

| Código de estado de HTTP | Descrição | Possível razão |
|------------------|-------------|-----------------|
| `100` | Continuar | O pedido inicial foi aceite. Continue com o envio do resto dos dados. (Utilizado com transferência em pedaços) |
| `200` | OK | O pedido foi bem sucedido; o corpo de resposta é um objeto JSON. |
| `400` | Mau pedido | Código linguístico não fornecido, nem uma linguagem suportada, ficheiro áudio inválido, etc. |
| `401` | Não autorizado | A chave de assinatura ou o sinal de autorização são inválidos na região especificada ou ponto final inválido. |
| `403` | Proibido | Chave de assinatura em falta ou ficha de autorização. |

## <a name="chunked-transfer"></a>Transferência em pedaços

A transferência`Transfer-Encoding: chunked`em pedaços () pode ajudar a reduzir a latência do reconhecimento. Permite que o serviço de Fala comece a processar o ficheiro áudio enquanto é transmitido. A API REST não fornece resultados parciais ou intercalares.

Esta amostra de código mostra como enviar áudio em pedaços. Apenas o primeiro pedaço deve conter o cabeçalho do ficheiro áudio. `request`é `HttpWebRequest` um objeto ligado ao ponto final de REST apropriado. `audioFile`é o caminho para um ficheiro áudio no disco.

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

Os resultados são fornecidos como JSON. O `simple` formato inclui estes campos de alto nível.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Estatuto, como `Success` o reconhecimento bem sucedido. Veja a próxima mesa.|
|`DisplayText`|O texto reconhecido após a capitalização, pontuação, normalização de texto inversa (conversão de texto falado para formas mais curtas, como 200 para "duzentos" ou "Dr. Smith" para "doctor smith"), e máscara de profanação. Presente apenas no sucesso.|
|`Offset`|O tempo (em 100 unidades de nanossegundos) em que o discurso reconhecido começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) do discurso reconhecido no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Estado | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi `DisplayText` bem sucedido e o campo está presente. |
| `NoMatch` | A fala foi detetada no fluxo de áudio, mas não foram correspondidas palavras da linguagem-alvo. Normalmente significa que a linguagem de reconhecimento é uma linguagem diferente daquela que o utilizador está a falar. |
| `InitialSilenceTimeout` | O início da corrente de áudio continha apenas silêncio, e o serviço cronometrava à espera da fala. |
| `BabbleTimeout` | O início da corrente de áudio continha apenas ruído, e o serviço cronometrava à espera de discurso. |
| `Error` | O serviço de reconhecimento deparou-se com um erro interno e não pôde continuar. Tente de novo, se possível. |

> [!NOTE]
> Se o áudio for composto apenas `profanity` por profanação, e `remove`se o parâmetro de consulta estiver definido, o serviço não devolve um resultado de fala.

O `detailed` formato inclui os `simple` mesmos `NBest`dados que o formato, juntamente com, uma lista de interpretações alternativas do mesmo resultado de reconhecimento. Estes resultados são classificados de muito provavelmente para o menos provável. A primeira entrada é a mesma que o resultado principal do reconhecimento.  Ao utilizar `detailed` o `DisplayText` formato, é fornecido quanto `Display` a cada resultado na `NBest` lista.

Cada objeto `NBest` da lista inclui:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0.0 (sem confiança) para 1.0 (plena confiança) |
| `Lexical` | A forma lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | A forma inversa-por texto normalizada ("canónica") do texto reconhecido, com números de telefone, números, abreviaturas ("doctor smith" a "dr smith"), e outras transformações aplicadas. |
| `MaskedITN` | A forma ITN com máscara profana aplicada, se solicitado. |
| `Display` | A forma de exibição do texto reconhecido, com pontuação e capitalização adicionadas. Este parâmetro é o `DisplayText` mesmo que fornecido `simple`quando o formato está definido para . |

## <a name="sample-responses"></a>Respostas da amostra

Uma resposta `simple` típica para reconhecimento:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Uma resposta `detailed` típica para reconhecimento:

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
