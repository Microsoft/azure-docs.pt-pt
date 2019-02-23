---
title: Resolução de problemas de voz do Bing | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Como resolver problemas ao utilizar a voz do Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e70e7b79be7dd4ea55c56898eaf8007d25732366
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673023"
---
# <a name="troubleshooting-bing-speech"></a>Resolução de problemas de voz do Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Erro `HTTP 403 Forbidden`

Quando utilizar a API de reconhecimento de voz, ele retorna um `HTTP 403 Forbidden` erro.

### <a name="cause"></a>Causa

Este erro normalmente é causado por problemas de autenticação. Pedidos de ligação sem válido `Ocp-Apim-Subscription-Key` ou `Authorization` cabeçalho são rejeitados pelo serviço com um `HTTP 403 Forbidden` resposta.

Se estiver a utilizar a chave de subscrição para a autenticação, o motivo pelo qual poderia ser

- a chave de subscrição está em falta ou é inválido
- a quota de utilização da chave de subscrição foi excedida
- o `Ocp-Apim-Subscription-Key` campo não está definido no cabeçalho do pedido, quando é chamada de REST API

Se estiver a utilizar o token de autorização para autenticação, pelas seguintes razões poderiam fazer com que o erro.

- o `Authorization` cabeçalho está em falta no pedido ao utilizar o REST
- o token de autorização especificado no cabeçalho de autorização é inválido
- o token de autorização está expirado. O token de acesso tem uma expiração de 10 minutos

Para obter mais informações sobre a autenticação, consulte a [autenticação](How-to/how-to-authentication.md) página.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

#### <a name="verify-that-your-subscription-key-is-valid"></a>Certifique-se de que a sua chave de assinatura é válida

Pode executar o seguinte comando para a verificação. Nota para substituir *YOUR_SUBSCRIPTION_KEY* com sua própria chave de subscrição. Se a chave de subscrição é válida, receberá na resposta o token de autorização como um JSON Web Token (JWT). Caso contrário, receberá um erro na resposta.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` com sua própria chave de subscrição.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

O exemplo utiliza o curl no Linux com o bash. Se não estiver disponível na sua plataforma, terá de instalar o curl. O exemplo deve funcionar também na Cygwin no Windows, do Git Bash, zsh e outros shells.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Certifique-se de que utilize a mesma chave de subscrição na sua aplicação ou na solicitação REST como o que é utilizado acima.

#### <a name="verify-the-authorization-token"></a>Verifique se o token de autorização

Este passo só é necessária se utilizar o token de autorização para autenticação. Execute o seguinte comando para verificar se o token de autorização é válido. O comando faz um pedido POST para o serviço e espera que uma mensagem de resposta do serviço. Se continuar a receber HTTP `403 Forbidden` erro, verifique novamente o acesso ao token está definido corretamente e não expirou.

> [!IMPORTANT]
> O token tem uma expiração de 10 minutos.
> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` com o caminho para o arquivo de áudio pré-gravados, e `YOUR_ACCESS_TOKEN` com o token de autorização devolvido no passo anterior.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Erro `HTTP 400 Bad Request`

Este motivo, normalmente, é que o corpo do pedido contém dados de áudio inválidos. Atualmente suportamos apenas arquivo WAV.

## <a name="error-http-408-request-timeout"></a>Erro `HTTP 408 Request Timeout`

O erro é provavelmente uma vez que não existem dados de áudio são enviados para o serviço e o serviço retorna este erro após o tempo limite. Para a API do REST, os dados de áudio devem ser colocados no corpo do pedido.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>O `RecognitionStatus` na resposta é `InitialSilenceTimeout`

Dados de áudio são, normalmente, o motivo pelo qual a causar o problema. Por exemplo,

- o áudio tem um tempo longo silêncio no início. O serviço deixa o reconhecimento após um número de segundos e devolve `InitialSilenceTimeout`.
- o áudio utiliza o formato de codec não suportado, o que faz com que os dados de áudio seja tratado como silêncio.
