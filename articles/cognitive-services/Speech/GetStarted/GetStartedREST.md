---
title: Introdução à API de reconhecimento de Fala do Bing usando REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use REST para acessar a API de reconhecimento de fala nos serviços cognitivas da Microsoft para converter áudio falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965774"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Início rápido: Usar a API REST de reconhecimento de Fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com o serviço de Fala do Bing baseado em nuvem, você pode desenvolver aplicativos usando a API REST para converter áudio falado em texto.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Assine o Speech API e obtenha uma chave de assinatura de avaliação gratuita

O Speech API faz parte de serviços cognitivas (anteriormente Oxford de projeto). Você pode obter chaves de assinatura de avaliação gratuita da página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) . Depois de selecionar a Speech API, selecione **obter chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, para que você possa usar qualquer chave.

> [!IMPORTANT]
>* Obtenha uma chave de assinatura. Antes de poder acessar a API REST, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
>* Use sua chave de assinatura. Nos exemplos de REST a seguir, substitua YOUR_SUBSCRIPTION_KEY por sua própria chave de assinatura.
>
>* Consulte a página de [autenticação](../how-to/how-to-authentication.md) para saber como obter uma chave de assinatura.

### <a name="prerecorded-audio-file"></a>Arquivo de áudio pré-gravados

Neste exemplo, usamos um arquivo de áudio gravado para ilustrar como usar a API REST. Registre um arquivo de áudio de você mesmo dizendo uma frase curta. Por exemplo, digamos "Qual é o clima como hoje?" ou "Encontre filmes divertidos para assistir". A API de reconhecimento de fala também dá suporte à entrada de microfone externa.

> [!NOTE]
> O exemplo requer que o áudio seja registrado como um arquivo WAV com o **mono (PCM Single Channel), 16 kHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Criar uma solicitação de reconhecimento e enviá-la para o serviço de reconhecimento de fala

A próxima etapa para o reconhecimento de fala é enviar uma solicitação POST para os pontos de extremidade HTTP de fala com o cabeçalho e o corpo de solicitação apropriados.

### <a name="service-uri"></a>URI de serviço

O URI do serviço de reconhecimento de fala é definido com base nos [modos de reconhecimento](../concepts.md#recognition-modes) e nos [idiomas de reconhecimento](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`Especifica o modo de reconhecimento e deve ser um dos seguintes valores: `interactive`, `conversation`ou `dictation`. É um caminho de recurso necessário no URI. Para obter mais informações, consulte [modos de reconhecimento](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>`é um parâmetro obrigatório na cadeia de caracteres de consulta. Ele define o idioma de destino para conversão de áudio: por `en-US` exemplo, para inglês (Estados Unidos). Para obter mais informações, consulte [linguagens de reconhecimento](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>`é um parâmetro opcional na cadeia de caracteres de consulta. Seus valores permitidos são `simple` e `detailed`. Por padrão, o serviço retorna resultados no `simple` formato. Para obter mais informações, consulte [formato de saída](../concepts.md#output-format).

Alguns exemplos de URIs de serviço são listados na tabela a seguir.

| Modo de reconhecimento  | Idioma | Formato de saída | URI de serviço |
|---|---|---|---|
| `interactive` | pt-BR | Predefinição | https:\//Speech.Platform.Bing.com/Speech/Recognition/Interactive/cognitiveservices/v1?language=pt-br |
| `conversation` | en-US | Detalhado | https:\//Speech.Platform.Bing.com/Speech/Recognition/Conversation/cognitiveservices/v1?language=en-US&Format=detailed |
| `dictation` | FR-FR | Simples | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> O URI de serviço é necessário somente quando seu aplicativo usa APIs REST para chamar o serviço de reconhecimento de fala. Se você usar uma das [bibliotecas de cliente](GetStartedClientLibraries.md), normalmente não precisará saber qual URI é usado. As bibliotecas de cliente podem usar URIs de serviço diferentes, que são aplicáveis somente para uma biblioteca de cliente específica. Para obter mais informações, consulte a biblioteca de cliente de sua escolha.

### <a name="request-headers"></a>Cabeçalhos do pedido

Os campos a seguir devem ser definidos no cabeçalho da solicitação:

- `Ocp-Apim-Subscription-Key`: Cada vez que você chama o serviço, você deve passar sua chave de assinatura no `Ocp-Apim-Subscription-Key` cabeçalho. O Speech Service também dá suporte à passagem de tokens de autorização em vez de chaves de assinatura. Para obter mais informações, veja [Autenticação](../How-to/how-to-authentication.md).
- `Content-type`: O `Content-type` campo descreve o formato e o codec do fluxo de áudio. Atualmente, há suporte apenas para o arquivo WAV e a codificação PCM mono 16000. O valor do tipo de conteúdo para esse formato `audio/wav; codec=audio/pcm; samplerate=16000`é.

O campo `Transfer-Encoding` é opcional. Se você definir esse campo como `chunked`, poderá Chop o áudio em pequenas partes. Para obter mais informações, consulte [transferência em partes](../How-to/how-to-chunked-transfer.md).

Este é um cabeçalho de solicitação de exemplo:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Enviar uma solicitação para o serviço

O exemplo a seguir mostra como enviar uma solicitação de reconhecimento de fala para pontos de extremidade REST de fala. Ele usa o `interactive` modo de reconhecimento.

> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` com o caminho para o ficheiro de áudio pré-gravados. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua própria chave de assinatura.

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

O exemplo usa a ondulação no Linux com bash. Se ele não estiver disponível em sua plataforma, talvez seja necessário instalar a ondulação. O exemplo também funciona em Cygwin no Windows, git bash, zsh e outros shells.

> [!NOTE]
> Mantenha o `@` antes do nome do arquivo de áudio `YOUR_AUDIO_FILE` ao substituir pelo caminho para o arquivo de áudio pré-gravados, como indica que o valor de `--data-binary` é um nome de arquivo em vez de dados.

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

## <a name="process-the-speech-recognition-response"></a>Processar a resposta de reconhecimento de fala

Após o processamento da solicitação, o serviço de fala retorna os resultados em uma resposta como formato JSON.

> [!NOTE]
> Se o código anterior retornar um erro, consulte [solução de problemas](../troubleshooting.md) para localizar a causa possível.

O trecho de código a seguir mostra um exemplo de como você pode ler a resposta do fluxo.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Neste exemplo, a ondulação retorna diretamente a mensagem de resposta em uma cadeia de caracteres. Se você quiser mostrá-lo no formato JSON, poderá usar ferramentas adicionais, por exemplo, JQ.

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

- Ele dá suporte a fluxo de áudio de até 15 segundos.
- Ele não dá suporte a resultados intermediários durante o reconhecimento. Os usuários recebem apenas o resultado de reconhecimento final.

Para remover essas limitações, use as [bibliotecas de cliente](GetStartedClientLibraries.md)de fala. Ou você pode trabalhar diretamente com o [protocolo WebSocket de fala](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Passos seguintes

- Para ver como usar a API REST no C#, Java, etc., consulte esses aplicativos de [exemplo](../samples.md).
- Para localizar e corrigir erros, consulte [solução de problemas](../troubleshooting.md).
- Para usar recursos mais avançados, consulte como começar usando as [bibliotecas de cliente](GetStartedClientLibraries.md)de fala.

### <a name="license"></a>Licença

Todos os SDKs e exemplos de serviços cognitivas são licenciados com a licença MIT. Para obter mais informações, consulte [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
