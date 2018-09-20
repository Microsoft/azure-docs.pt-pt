---
title: Como Stream de áudio de transferência em partes | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Como utilizar trasfer em partes para enviar o fluxo de áudio para o serviço de voz do Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 1796a82b85182e6d231cb0bf1536cda2406e2c53
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364912"
---
# <a name="chunked-transfer-encoding"></a>A codificação da transferência

A transcrição de voz em texto, API de reconhecimento de voz de Microsoft permite-lhe para enviar o áudio como um segmento inteiro ou chop o áudio em pequenos segmentos. Para uma transmissão de áudio eficaz e reduzindo a latência de transcrição, recomenda-se que utilize [codificação de transferência segmentada](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) transmitir áudio para o serviço. Outras implementações podem resultar numa maior latência percebido pelo usuário. Para obter mais informações, consulte a [fluxos de áudio](../concepts.md#audio-streams) página.

> [!NOTE]
> Não pode carregar mais de 10 segundos de áudio em qualquer uma solicitação e a duração total do pedido não pode exceder os 14 segundos.
> [!NOTE]
> Tem de especificar a transferência em partes codificação apenas se utilizar o [REST APIs](../GetStarted/GetStartedREST.md) para chamar o serviço de voz. Aplicativos que usam [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) não é necessário configurar a codificação de transferência em partes.

O código a seguir mostra como definir a codificação de transferência segmentada e para enviar um arquivo de áudio que está a ser segmentado em segmentos de 1024 bytes.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

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
