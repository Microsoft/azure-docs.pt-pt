---
title: Referência da API de voz em texto (REST) - serviços de voz
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API de REST de voz em texto. Neste artigo, irá aprender sobre as opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: baaa7b1068e13863293e0968cb0bf1ffb198882b
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894976"
---
# <a name="speech-to-text-rest-api"></a>API REST de voz em texto

Como alternativa para o [SDK de voz](speech-sdk.md), serviços de fala permite que converta voz em texto com uma API REST. Cada ponto de final de acesso está associado uma região. Seu aplicativo requer uma chave de subscrição para o ponto final que pretende utilizar.

Antes de utilizar a API de REST de voz em texto, compreenda:
* Pedidos que utilizam a API de REST só podem conter 10 segundos de áudio gravado.
* A API de REST de voz em texto devolve apenas os resultados finais. Resultados parciais não são fornecidos.

Se enviar áudio mais tempo é um requisito para a sua aplicação, considere utilizar o [SDK de voz](speech-sdk.md) ou [transcrição do batch](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

Estas regiões são suportadas para transcrição de voz em texto com a API REST. Certifique-se de que seleciona o ponto final que corresponde à sua região de subscrição.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na cadeia de consulta da solicitação REST.

| Parâmetro | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `language` | Identifica o idioma falado que está a ser reconhecido. Ver [idiomas suportados](language-support.md#speech-to-text). | Necessário |
| `format` | Especifica o formato de resultado. Aceite os valores são `simple` e `detailed`. Os resultados da simples incluem `RecognitionStatus`, `DisplayText`, `Offset`, e `Duration`. Respostas detalhadas incluem vários resultados com valores de confiança e quatro diferentes representações. A predefinição é `simple`. | Opcional |
| `profanity` | Especifica como lidar com linguagem inapropriada nos resultados de reconhecimento. Aceite os valores são `masked`, que substitui a linguagem inapropriada por asteriscos, `removed`, linguagem inapropriada todos os que remover o resultado ou `raw`, que inclui a linguagem inapropriada no resultado. A predefinição é `masked`. | Opcional |

## <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para pedidos de voz em texto.

|Cabeçalho| Descrição | Obrigatório / opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A chave de subscrição de serviços de voz. | Este cabeçalho de qualquer um dos ou `Authorization` é necessária. |
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Este cabeçalho de qualquer um dos ou `Ocp-Apim-Subscription-Key` é necessária. |
| `Content-type` | Descreve o formato e o codec de dados de áudio fornecidos. Aceite os valores são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Necessário |
| `Transfer-Encoding` | Especifica que os dados em segmentos de áudio são enviados, em vez de um único arquivo. Utilize este cabeçalho apenas se a segmentação de dados de áudio. | Opcional |
| `Expect` | Se utilizar a transferência em partes, enviar `Expect: 100-continue`. Os serviços de fala reconhece o pedido inicial e awaits dados adicionais.| Necessário se o envio de dados de áudio em partes. |
| `Accept` | Se for fornecido, tem de ser `application/json`. Os serviços de voz fornecer resultados em JSON. Algumas estruturas de solicitação da Web, forneça um valor incompatível predefinido se não especificar um, portanto, é recomendável sempre incluir `Accept`. | Opcional mas recomendado. |

## <a name="audio-formats"></a>Formatos de áudio

Áudio é enviado no corpo do HTTP `POST` pedido. Tem de ser um dos formatos nesta tabela:

| Formato | Codec | Velocidade de transmissão | Taxa de exemplo |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>Os formatos acima são suportados através da REST API e WebSocket nos serviços de voz. O [SDK de voz](speech-sdk.md) atualmente apenas suporta o WAV formatar com o codec PCM.

## <a name="sample-request"></a>Pedido de exemplo

Este é um pedido HTTP comuns. O exemplo abaixo inclui o nome de anfitrião e cabeçalhos necessários. É importante observar que o serviço espera também os dados de áudio, o que não estão incluídos neste exemplo. Como mencionado anteriormente, segmentação recomenda, no entanto, não é necessário.

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
| 100 | Continuar | O pedido inicial foi aceite. Continue com a enviar o resto dos dados. (Utilizado com a transferência em partes). |
| 200 | OK | O pedido foi concluída com êxito; o corpo da resposta é um objeto JSON. |
| 400 | Pedido incorreto | Código de idioma fornecido ou não é um idioma suportado; arquivo de áudio inválido. |
| 401 | Não autorizado | Chave de subscrição ou autorização token é inválido na região especificada ou ponto final inválido. |
| 403 | Proibido | Chave de subscrição ou autorização em falta token. |

## <a name="chunked-transfer"></a>Transferência em partes

A transferência (`Transfer-Encoding: chunked`) pode ajudar a reduzir a latência de reconhecimento, pois permite que os serviços de voz começar a processar o ficheiro de áudio, enquanto que está a ser transmitido. A API REST não fornece resultados parciais ou provisórias. Esta opção destina-se exclusivamente para melhorar a capacidade de resposta.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro segmento deve conter cabeçalho do arquivo de áudio. `request` está ligado um objeto HTTPWebRequest para o ponto final REST adequado. `audioFile` é o caminho para um arquivo de áudio no disco.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui estes campos de nível superior.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Estado, tais como `Success` para reconhecimento bem-sucedido. Consulte a tabela seguinte.|
|`DisplayText`|O texto reconhecido depois de capitalização, pontuação, normalização do texto inversa (conversão de texto falado forms mais curto, por exemplo, 200 para "duzentos" ou "Dr. Smith"para"doutor smith") e a máscara de linguagem inapropriada. Apresentar apenas com êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos), no qual a voz reconhecido começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) do voz reconhecido no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Estado | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi concluída com êxito e o `DisplayText` campo está presente. |
| `NoMatch` | Conversão de voz foi detetada no fluxo de áudio, mas sem palavras do idioma de destino foram correspondidas. Normalmente, significa que o idioma de reconhecimento é um idioma diferente daquele que o utilizador está a falar. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço excedeu o tempo aguardar por voz. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço excedeu o tempo aguardar por voz. |
| `Error` | O serviço de reconhecimento de obteve um erro interno e não foi possível continuar. Tente novamente se possível. |

> [!NOTE]
> Se o áudio consiste apenas em linguagem inapropriada e o `profanity` parâmetro de consulta está definido como `remove`, o serviço não devolve um resultado de conversão de voz.

O `detailed` formato inclui os mesmos dados que o `simple` formatar, juntamente com `NBest`, uma lista de alternativas interpretações sobre o mesmo resultado de reconhecimento. Esses resultados estão ordenados do maior probabilidade de menos provável que a primeira entrada é o mesmo como o resultado do reconhecimento principal.  Ao utilizar o `detailed` formato, `DisplayText` é fornecido como `Display` para cada resultado no `NBest` lista.

Cada objeto no `NBest` lista inclui:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (sem confiança) para 1,0 (confiança total) |
| `Lexical` | O formulário léxico do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formulário ("canonical") normalizado de texto de inverso do texto reconhecido, com o telefone números, números, abreviaturas ("doutor smith" para "dr smith") e outras transformações aplicadas. |
| `MaskedITN` | O formulário ITN com a máscara de linguagem inapropriada aplicada, se solicitado. |
| `Display` | O formulário de apresentação do texto reconhecido, com a pontuação e capitalização adicionado. Este parâmetro é o mesmo que `DisplayText` fornecida quando o formato está definido como `simple`. |

## <a name="sample-responses"></a>Respostas de exemplo

Esta é uma resposta típica para `simple` reconhecimento.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Esta é uma resposta típica para `detailed` reconhecimento.

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

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
