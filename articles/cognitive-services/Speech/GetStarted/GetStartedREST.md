---
title: Comece com a API de reconhecimento de voz do Bing utilizando o REST | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize REST para aceder à API de reconhecimento de fala em serviços cognitivos da Microsoft para converter áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e29d4c0a4279ae44a45b082c5a36b471af3cb12a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110193"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Início rápido: Utilizar a API de REST de reconhecimento de voz do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com o serviço de voz do Bing com base na cloud, pode desenvolver aplicativos com a API de REST para converter áudio falado em texto.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Subscrever a API de voz e obter uma chave de subscrição de avaliação gratuita

A API de voz faz parte dos serviços cognitivos (anteriormente o projeto Oxford). Pode obter chaves de subscrição de avaliação gratuita do [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página. Depois de selecionar a API de voz, selecione **obter a chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão associadas para a mesma cota, pelo que pode utilizar qualquer uma das chaves.

> [!IMPORTANT]
>* Obter uma chave de subscrição. Antes de poder aceder a API REST, tem de ter uma [chave de subscrição](https://azure.microsoft.com/try/cognitive-services/).
>
>* Utilize a sua chave de subscrição. Nos exemplos a seguir REST, substitua YOUR_SUBSCRIPTION_KEY sua própria chave de subscrição.
>
>* Consulte a [autenticação](../how-to/how-to-authentication.md) página para saber como obter uma chave de subscrição.

### <a name="prerecorded-audio-file"></a>Arquivo de áudio pré-gravados

Neste exemplo, utilizamos um arquivo de áudio gravado para ilustrar como usar a API REST. Grave um arquivo de áudio de mesmo dizendo que uma frase curta. Por exemplo, digamos que "O que é a meteorologia como hoje?" ou "Find filmes engraçados ver". A API de reconhecimento de voz também suporta a entrada do microfone externo.

> [!NOTE]
> O exemplo requer que o áudio é registado como um arquivo WAV com **PCM canal único (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Criar um pedido de reconhecimento e enviá-lo para o serviço de reconhecimento de fala

A próxima etapa para reconhecimento de fala é para enviar um pedido POST para os pontos finais de HTTP de voz com o cabeçalho de pedido adequado e o corpo.

### <a name="service-uri"></a>URI de serviço

O serviço de reconhecimento de fala URI é definido com base na [modos de reconhecimento](../concepts.md#recognition-modes) e [idiomas de reconhecimento](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Especifica o modo de reconhecimento e tem de ser um dos seguintes valores: `interactive`, `conversation`, ou `dictation`. É um caminho de recurso necessário no URI. Para obter mais informações, consulte [modos de reconhecimento](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` é um parâmetro necessário na cadeia de consulta. Define o idioma de destino para a conversão de áudio: por exemplo, `en-US` para inglês (Estados Unidos). Para obter mais informações, consulte [idiomas de reconhecimento](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` é um parâmetro opcional na cadeia de consulta. Os valores permitidos são `simple` e `detailed`. Por predefinição, o serviço retorna resultados no `simple` formato. Para obter mais informações, consulte [formato de saída](../concepts.md#output-format).

Alguns exemplos de serviço URIs estão listados na tabela seguinte.

| Modo de reconhecimento  | Idioma | Formato de saída | URI de serviço |
|---|---|---|---|
| `interactive` | pt-BR | Predefinição | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Detalhado | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | FR-FR | Simples | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> O URI de serviço é necessário apenas quando a sua aplicação utilizar REST APIs para chamar o serviço de reconhecimento de fala. Se utilizar um da [bibliotecas de cliente](GetStartedClientLibraries.md), normalmente, não precisa de saber qual URI é utilizado. As bibliotecas de cliente podem utilizar o serviço de diferente URIs, que são aplicáveis apenas para uma biblioteca de cliente específico. Para obter mais informações, consulte a biblioteca de cliente da sua preferência.

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes campos tem de ser definidos no cabeçalho do pedido:

- `Ocp-Apim-Subscription-Key`: Sempre que chamar o serviço, tem de passar a chave de subscrição a `Ocp-Apim-Subscription-Key` cabeçalho. Serviço de voz também suporta a autorização de passar tokens em vez de chaves de subscrição. Para obter mais informações, veja [Autenticação](../How-to/how-to-authentication.md).
- `Content-type`: O `Content-type` campo descreve o formato e o codec do fluxo de áudio. Atualmente, apenas arquivo WAV e a codificação do PCM Mono 16000 é suportada. O valor de tipo de conteúdo para esse formato é `audio/wav; codec=audio/pcm; samplerate=16000`.

O campo `Transfer-Encoding` é opcional. Se definir este campo para `chunked`, pode chop o áudio em pequenos segmentos. Para obter mais informações, consulte [segmentado transferência](../How-to/how-to-chunked-transfer.md).

Segue-se um cabeçalho de pedido de exemplo:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Enviar um pedido para o serviço

O exemplo seguinte mostra como enviar um pedido de reconhecimento de voz para pontos finais REST de voz. Ele usa o `interactive` modo de reconhecimento.

> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` com o caminho para o ficheiro de áudio pré-gravados. Substitua `YOUR_SUBSCRIPTION_KEY` com sua própria chave de subscrição.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

O exemplo utiliza o curl no Linux com o bash. Se não estiver disponível na sua plataforma, precisará de instalar o curl. O exemplo também funciona em Cygwin no Windows, do Git Bash, zsh e outros shells.

> [!NOTE]
> Manter o `@` antes do nome de arquivo de áudio ao substituir `YOUR_AUDIO_FILE` com o caminho para o ficheiro de áudio pré-gravados, como ele indica que o valor de `--data-binary` é um nome de ficheiro em vez de dados.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
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

---

## <a name="process-the-speech-recognition-response"></a>Processar a resposta de reconhecimento de voz

Depois de processar o pedido, o serviço de voz retorna os resultados numa resposta como formato JSON.

> [!NOTE]
> Se o código anterior retorna um erro, consulte [resolução de problemas](../troubleshooting.md) para localizar a causa possível.

O fragmento de código seguinte mostra um exemplo de como é possível ler a resposta do fluxo.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Neste exemplo, o curl diretamente devolve a mensagem de resposta numa cadeia de caracteres. Se quiser mostrá-lo no formato JSON, pode utilizar ferramentas adicionais, por exemplo, jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

O exemplo a seguir é uma resposta JSON:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Limitações

A API REST tem algumas limitações:

- Suporta a transmissão de áudio apenas de 15 segundos.
- Ele não dá suporte resultados intermédios durante o reconhecimento. Os utilizadores recebem apenas o resultado final de reconhecimento.

Para remover essas limitações, utilize a voz [bibliotecas de cliente](GetStartedClientLibraries.md). Ou pode trabalhar diretamente com o [protocolo WebSocket de voz](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Passos seguintes

- Para ver como utilizar a API REST em c#, Java, etc., vê-los [aplicações de exemplo](../samples.md).
- Para localizar e corrigir erros, veja [resolução de problemas](../troubleshooting.md).
- Para utilizar as funcionalidades mais avançadas, veja como começar, utilizando voz [bibliotecas de cliente](GetStartedClientLibraries.md).

### <a name="license"></a>Licença

Todos os exemplos e SDKs de serviços cognitivos são licenciados com a licença do MIT. Para obter mais informações, consulte [licença](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
