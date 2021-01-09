---
title: Referência API de voz a texto (REST) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API REST de discurso a texto. Neste artigo, você vai aprender sobre opções de autorização, opções de consulta, como estruturar um pedido e receber uma resposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 1577d63822bdb21eff7fcbb3e1343243a3004409
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033647"
---
# <a name="speech-to-text-rest-api"></a>API REST de conversão de voz em texto

Falar-a-texto tem duas APIs de DESCANSO diferentes. Cada API serve o seu propósito especial e utiliza diferentes conjuntos de pontos finais.

As APIs de repouso de discurso para texto são:
- [O API REST de voz em texto v3.0](#speech-to-text-rest-api-v30) é utilizado para [a transcrição do lote](batch-transcription.md) e discurso [personalizado](custom-speech-overview.md). v3.0 é um [sucessor de v2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [A API REST de voz para texto para áudio curto](#speech-to-text-rest-api-for-short-audio) é usada para a transcrição onLine como alternativa ao [SDK](speech-sdk.md)do discurso . Os pedidos que utilizem esta API só podem transmitir até 60 segundos de áudio por pedido. 

## <a name="speech-to-text-rest-api-v30"></a>A API DE REPOUSO de expressão em texto v3.0

O API REST de voz em texto v3.0 é utilizado para [a transcrição do lote](batch-transcription.md) e discurso [personalizado](custom-speech-overview.md). Se necessitar de comunicar com a transcrição OnLine via REST, utilize [a API REST de voz para texto para áudio curto](#speech-to-text-rest-api-for-short-audio).

Utilizar REST API v3.0 para:
- Copie modelos para outras subscrições caso pretenda que os colegas tenham acesso a um modelo que construiu, ou nos casos em que pretende implementar um modelo para mais de uma região
- Transcreva dados de um recipiente (transcrição a granel) bem como fornecer URLs de ficheiro sonoro múltiplos
- Faça upload de dados a partir de contas de armazenamento Azure através da utilização de um SAS Uri
- Obtenha registos por ponto final se os registos tiverem sido solicitados para esse ponto final
- Solicite o manifesto dos modelos que cria, com o propósito de instalar contentores no local

REST A API v3.0 inclui características como:
- **Notificações-Webhooks**— Todos os processos de execução do serviço suportam agora notificações webhook. REST A API v3.0 fornece as chamadas para que possa registar os seus webhooks onde as notificações são enviadas
- **Atualizar modelos atrás de pontos finais** 
- **Adaptação de modelo com vários conjuntos** de dados — Adapte um modelo usando múltiplas combinações de dados de dados acústicos, linguísticos e de pronúncia
- **Traga o seu próprio armazenamento**— Use as suas próprias contas de armazenamento para registos, ficheiros de transcrição e outros dados

Veja exemplos sobre a utilização de REST API v3.0 com a transcrição do Lote é [este artigo.](batch-transcription.md)

Se estiver a utilizar a API REST v2.0 do Speech-to-text, veja como pode migrar para v3.0 [neste guia](/azure/cognitive-services/speech-service/migrate-v2-to-v3).

Consulte [aqui](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)a referência completa do API REST v3.0 do Discurso-a-Texto .

## <a name="speech-to-text-rest-api-for-short-audio"></a>API DE REPOUSO de voz para texto para áudio curto

Como alternativa ao [SDK](speech-sdk.md)de discurso, o serviço Discurso permite converter Discurso-texto usando uma API REST. Cada ponto final acessível está associado a uma região. A sua aplicação requer uma chave de subscrição para o ponto final que pretende utilizar. O REST API para áudio curto é muito limitado, e só deve ser usado em casos em que o [SDK do discurso](speech-sdk.md) não pode.

Antes de utilizar a API REST discurso-a-texto para áudio curto, considere o seguinte:

* Os pedidos que utilizam a API REST para áudio curto e áudio de transmissão diretamente só podem conter até 60 segundos de áudio.
* A API REST discurso-a-texto para áudio curto apenas devolve os resultados finais. Os resultados parciais não são fornecidos.

Se o envio de áudio mais longo for um requisito para a sua aplicação, considere utilizar o [SPI de voz](speech-sdk.md) [para texto v3.0](#speech-to-text-rest-api-v30).

> [!TIP]
> Veja [este artigo](sovereign-clouds.md) para o Governo Azure e para os pontos finais da Azure China.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Regiões e pontos finais

O ponto final para a API REST para áudio curto tem este formato:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

`<REGION_IDENTIFIER>`Substitua-a pelo identificador correspondente à região da sua subscrição a partir desta tabela:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> O parâmetro da linguagem deve ser anexado ao URL para evitar receber um erro HTTP de 4xx. Por exemplo, a língua definida para o inglês americano usando o ponto final dos EUA é: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="query-parameters"></a>Parâmetros de consulta

Estes parâmetros podem ser incluídos na sequência de consulta do pedido REST.

| Parâmetro | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a língua falada que está a ser reconhecida. Ver [línguas suportadas.](language-support.md#speech-to-text) | Obrigatório |
| `format` | Especifica o formato de resultado. Os valores aceites são `simple` `detailed` e. Os resultados simples `RecognitionStatus` `DisplayText` incluem, `Offset` `Duration` e. As respostas detalhadas incluem quatro representações diferentes do texto do visor. A predefinição é `simple`. | Opcional |
| `profanity` | Especifica como lidar com a profanação nos resultados do reconhecimento. Os `masked` valores aceites são , que substitui a profanação por asteriscos, que `removed` removem toda a profanação do resultado, ou `raw` , que inclui a profanação no resultado. A predefinição é `masked`. | Opcional |
| `cid` | Ao utilizar o [portal Discurso Personalizado](./custom-speech-overview.md) para criar modelos personalizados, pode utilizar modelos personalizados através do seu **ID Endpoint** encontrado na página **De implementação.** Utilize o **ID endpoint** como argumento para o parâmetro de `cid` cadeia de consulta. | Opcional |

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos necessários e opcionais para pedidos de discurso a texto.

|Cabeçalho| Descrição | Obrigatório / Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A chave de subscrição do serviço de discurso. | Ou este cabeçalho ou `Authorization` é necessário. |
| `Authorization` | Um sinal de autorização precedido pela palavra `Bearer` . Para obter mais informações, veja [Autenticação](#authentication). | Ou este cabeçalho ou `Ocp-Apim-Subscription-Key` é necessário. |
| `Pronunciation-Assessment` | Especifica os parâmetros para mostrar pontuações de pronúncia nos resultados do reconhecimento, que avaliam a qualidade de pronúncia da entrada da fala, com indicadores de precisão, fluência, completude, etc. Este parâmetro é um json codificado base64 contendo vários parâmetros detalhados. Consulte os parâmetros de [avaliação da pronúncia](#pronunciation-assessment-parameters) para construir este cabeçalho. | Opcional |
| `Content-type` | Descreve o formato e o codec dos dados áudio fornecidos. Os valores aceites são `audio/wav; codecs=audio/pcm; samplerate=16000` `audio/ogg; codecs=opus` e. | Obrigatório |
| `Transfer-Encoding` | Especifica que os dados de áudio em pedaços estão a ser enviados, em vez de um único ficheiro. Utilize apenas este cabeçalho se os dados áudio em pedaços. | Opcional |
| `Expect` | Se utilizar uma transferência em pedaços, envie `Expect: 100-continue` . O serviço Discurso reconhece o pedido inicial e aguarda dados adicionais.| Necessário se enviar dados áudio em pedaços. |
| `Accept` | Se for fornecido, deve `application/json` ser. O serviço Discurso fornece resultados em JSON. Alguns quadros de pedido fornecem um valor padrão incompatível. É uma boa prática incluir `Accept` sempre. | Opcional, mas recomendado. |

### <a name="audio-formats"></a>Formatos áudio

O áudio é enviado no corpo do `POST` pedido HTTP. Deve estar num dos formatos desta tabela:

| Formato | Codec | Taxa de bit | Taxa de amostra  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, mono |
| OGG    | OPUS  | 256 km/h | 16 kHz, mono |

>[!NOTE]
>Os formatos acima são suportados através da REST API para áudio curto e WebSocket no serviço Speech. O [Speech SDK](speech-sdk.md) suporta atualmente o formato WAV com o codec PCM, bem como [outros formatos](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parâmetros de avaliação da pronúncia

Esta tabela lista os parâmetros necessários e opcionais para a avaliação da pronúncia.

| Parâmetro | Descrição | Necessário? |
|-----------|-------------|---------------------|
| Texto de Referência | O texto contra o que a pronúncia será avaliado. | Obrigatório |
| Sistema de Classificação | O sistema de pontos para a calibração da pontuação. O `FivePoint` sistema dá uma pontuação de 0-5 pontos flutuantes, e dá uma `HundredMark` pontuação de 0-100 pontos flutuantes. Predefinição: `FivePoint`. | Opcional |
| Granularidade | A granularidade de avaliação. Os valores aceites são `Phoneme` , que mostram a pontuação no nível completo do texto, palavra e fon de texto, que mostra a `Word` pontuação no texto completo e no nível de palavra, `FullText` que mostra a pontuação apenas no nível de texto completo. A predefinição é `Phoneme`. | Opcional |
| Dimensão | Define os critérios de saída. Os valores aceites são `Basic` , que mostram apenas a pontuação de precisão, `Comprehensive` mostra pontuações em mais dimensões (por exemplo, pontuação de fluência e pontuação de completude no nível de texto completo, tipo de erro no nível de palavra). Consulte [os parâmetros de resposta](#response-parameters) para ver definições de diferentes dimensões de pontuação e tipos de erros de palavras. A predefinição é `Basic`. | Opcional |
| EnableMiscue | Permite o cálculo do erro. Com isto ativado, as palavras pronunciadas serão comparadas com o texto de referência, e serão marcadas com omissão/inserção com base na comparação. Os valores aceites são `False` `True` e. A predefinição é `False`. | Opcional |
| CenárioId | Um GUID indicando um sistema de pontos personalizado. | Opcional |

Abaixo está um exemplo JSON contendo os parâmetros de avaliação da pronúncia:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

O seguinte código de amostra mostra como construir os parâmetros de avaliação da pronúncia no `Pronunciation-Assessment` cabeçalho:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Recomendamos vivamente o streaming (em pedaços) ao publicar os dados áudio, o que pode reduzir significativamente a latência. Consulte [o código de amostra em diferentes linguagens de programação](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) para saber como permitir o streaming.

>[!NOTE]
>A funcionalidade de avaliação da pronúncia só está atualmente disponível em `westus` `eastasia` regiões e `centralindia` regiões. E esta funcionalidade está atualmente disponível apenas em `en-US` idioma.

### <a name="sample-request"></a>Pedido de amostra

A amostra abaixo inclui o nome de hospedeiro e cabeçalhos necessários. É importante notar que o serviço também espera dados áudio, que não estão incluídos nesta amostra. Como mencionado anteriormente, recomenda-se, no entanto, que não seja necessário o chunking.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Para ativar a avaliação da pronúncia, pode adicionar abaixo o cabeçalho. Consulte os parâmetros de [avaliação da pronúncia](#pronunciation-assessment-parameters) para construir este cabeçalho.

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica sucesso ou erros comuns.

| Código de estado de HTTP | Descrição | Motivo possível |
|------------------|-------------|-----------------|
| `100` | Continuar | O pedido inicial foi aceite. Proceda ao envio do resto dos dados. (Usado com transferência em pedaços) |
| `200` | OK | O pedido foi bem sucedido; o corpo de resposta é um objeto JSON. |
| `400` | Mau pedido | Código idioma não fornecido, não um idioma suportado, ficheiro áudio inválido, etc. |
| `401` | Não autorizado | A chave de subscrição ou o token de autorização são inválidos na região especificada, ou ponto final inválido. |
| `403` | Proibido | Chave de assinatura em falta ou sinal de autorização. |

### <a name="chunked-transfer"></a>Transferência em pedaços

A transferência em `Transfer-Encoding: chunked` pedaços pode ajudar a reduzir a latência do reconhecimento. Permite que o serviço Desematado comece a processar o ficheiro áudio enquanto este é transmitido. A API REST para áudio curto não fornece resultados parciais ou intercalares.

Esta amostra de código mostra como enviar áudio em pedaços. Apenas o primeiro pedaço deve conter o cabeçalho do ficheiro áudio. `request` é um `HttpWebRequest` objeto ligado ao ponto final apropriado REST. `audioFile` é o caminho para um ficheiro áudio no disco.

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

### <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui estes campos de alto nível.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Estatuto, como `Success` para o reconhecimento bem sucedido. Ver a próxima mesa.|
|`DisplayText`|O texto reconhecido após capitalização, pontuação, normalização inversa do texto (conversão de texto falado para formas mais curtas, como 200 para "duzentos" ou "Dr. Smith" para "doutor smith"), e máscara de profanação. Presente apenas no sucesso.|
|`Offset`|A hora (em 100-nanosegundos) em que o discurso reconhecido começa no fluxo de áudio.|
|`Duration`|A duração (em 100-nanosegundos) do discurso reconhecido no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Estado | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi bem sucedido e o `DisplayText` campo está presente. |
| `NoMatch` | A fala foi detetada no fluxo de áudio, mas nenhuma palavra da língua alvo foi correspondida. Normalmente significa que a linguagem de reconhecimento é uma língua diferente daquela que o utilizador está a falar. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio, e o serviço estava fora à espera de discurso. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído, e o serviço estava fora à espera de discurso. |
| `Error` | O serviço de reconhecimento encontrou um erro interno e não pôde continuar. Tente de novo, se possível. |

> [!NOTE]
> Se o áudio for constituído apenas por profanação, e o `profanity` parâmetro de consulta for definido para , o serviço não devolve um resultado de `remove` fala.

O `detailed` formato inclui formas adicionais de resultados reconhecidos.
Ao utilizar o `detailed` formato, `DisplayText` é fornecido como para cada resultado na `Display` `NBest` lista.

O objeto da `NBest` lista pode incluir:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0.0 (sem confiança) para 1.0 (plena confiança) |
| `Lexical` | A forma lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | A forma inversamente normalizada de texto ("canónica") do texto reconhecido, com números de telefone, números, abreviaturas ("doctor smith" para "dr smith"), e outras transformações aplicadas. |
| `MaskedITN` | O formulário ITN com máscara de profanação aplicada, se solicitado. |
| `Display` | A forma de exibição do texto reconhecido, com pontuação e capitalização adicionadas. Este parâmetro é o mesmo que o fornecido quando o `DisplayText` formato é definido para `simple` . |
| `AccuracyScore` | Precisão de pronúncia do discurso. A precisão indica como os fones correspondem à pronúncia de um altifalante nativo. A pontuação de precisão do nível de texto completo e palavra é agregada a partir da pontuação de precisão do nível do telefone. |
| `FluencyScore` | Fluência do discurso dado. A fluência indica o quão perto o discurso corresponde ao uso de quebras silenciosas de um orador nativo entre as palavras. |
| `CompletenessScore` | Completude da fala, determinada calculando a relação de palavras pronunciadas com a entrada de texto de referência. |
| `PronScore` | Pontuação geral indicando a qualidade da pronúncia do discurso dado. Isto é agregado de `AccuracyScore` , `FluencyScore` e com `CompletenessScore` peso. |
| `ErrorType` | Este valor indica se uma palavra é omitida, inserida ou mal pronunciada, em comparação com `ReferenceText` . Os valores possíveis são `None` (ou seja, nenhum erro nesta palavra), `Omission` `Insertion` e `Mispronunciation` . |

### <a name="sample-responses"></a>Respostas da amostra

Uma resposta típica para `simple` o reconhecimento:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Uma resposta típica para `detailed` o reconhecimento:

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
      }
  ]
}
```

Uma resposta típica para o reconhecimento com avaliação da pronúncia:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta do Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
- [Personalizar modelos acústicos](./how-to-custom-speech-train-model.md)
- [Personalizar modelos de idioma](./how-to-custom-speech-train-model.md)
- [Familiarize-se com a transcrição do Lote](batch-transcription.md)