---
title: Como transferir um fluxo de áudio de transferência em partes | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Como usar trasfer em partes para enviar fluxo de áudio para o serviço de Fala do Bing
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966493"
---
# <a name="chunked-transfer-encoding"></a>Codificação de transferência em partes

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Para transcrever a fala para o texto, a API de reconhecimento de fala da Microsoft permite que você envie o áudio como uma parte inteira ou Chop o áudio em pequenas partes. Para streaming de áudio eficiente e redução da latência de transcrição, é recomendável usar a [codificação de transferência em partes](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) para transmitir o áudio para o serviço. Outras implementações podem resultar em maior latência percebida pelo usuário. Para obter mais informações, consulte a página [fluxos de áudio](../concepts.md#audio-streams) .

> [!NOTE]
> Você não pode carregar mais de 10 segundos de áudio em uma solicitação e a duração total da solicitação não pode exceder 14 segundos.
> [!NOTE]
> Você precisará especificar a codificação de transferência em partes somente se usar as [APIs REST](../GetStarted/GetStartedREST.md) para chamar o serviço de fala. Os aplicativos que usam [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) não precisam configurar a codificação de transferência em partes.

O código a seguir mostra como definir a codificação de transferência em partes e enviar um arquivo de áudio que está sendo fragmentado em partes de 1024 bytes.

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
