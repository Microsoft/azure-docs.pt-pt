---
title: Solução de problemas Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Como resolver problemas ao usar o Fala do Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f222d9d4cf6c56dea0832938dcb132cf711491bc
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934065"
---
# <a name="troubleshooting-bing-speech"></a>Solução de problemas Fala do Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Ao`HTTP 403 Forbidden`

Ao usar a API de reconhecimento de fala, `HTTP 403 Forbidden` ela retorna um erro.

### <a name="cause"></a>Causa

Esse erro geralmente é causado por problemas de autenticação. Solicitações de conexão sem `Ocp-Apim-Subscription-Key` válido `Authorization` ou cabeçalho são rejeitadas pelo serviço com `HTTP 403 Forbidden` uma resposta.

Se você estiver usando a chave de assinatura para autenticação, o motivo pode ser

- a chave de assinatura está ausente ou é inválida
- a cota de uso da chave de assinatura foi excedida
- o `Ocp-Apim-Subscription-Key` campo não é definido no cabeçalho da solicitação, quando a API REST é chamada

Se você estiver usando o token de autorização para autenticação, os motivos a seguir podem causar o erro.

- o `Authorization` cabeçalho está ausente na solicitação ao usar REST
- o token de autorização especificado no cabeçalho de autorização é inválido
- o token de autorização expirou. O token de acesso tem uma expiração de 10 minutos

Para obter mais informações sobre autenticação, consulte a página [autenticação](How-to/how-to-authentication.md) .

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

#### <a name="verify-that-your-subscription-key-is-valid"></a>Verifique se sua chave de assinatura é válida

Você pode executar o comando a seguir para verificação. Observação para substituir *YOUR_SUBSCRIPTION_KEY* por sua própria chave de assinatura. Se sua chave de assinatura for válida, você receberá na resposta o token de autorização como um JWT (token Web JSON). Caso contrário, você receberá um erro em resposta.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` pela sua própria chave de assinatura.

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

O exemplo usa a ondulação no Linux com bash. Se não estiver disponível em sua plataforma, talvez seja necessário instalar a ondulação. O exemplo deve funcionar também em Cygwin no Windows, git bash, zsh e outros shells.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Certifique-se de usar a mesma chave de assinatura em seu aplicativo ou na solicitação REST, como é usado acima.

#### <a name="verify-the-authorization-token"></a>Verificar o token de autorização

Essa etapa só será necessária se você usar o token de autorização para autenticação. Execute o comando a seguir para verificar se o token de autorização ainda é válido. O comando faz uma solicitação POST para o serviço e espera uma mensagem de resposta do serviço. Se você ainda receber um `403 Forbidden` erro de http, verifique se o token de acesso está definido corretamente e se não expirou.

> [!IMPORTANT]
> O token tem uma expiração de 10 minutos.
> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` pelo caminho para o arquivo de áudio pré-gravados e `YOUR_ACCESS_TOKEN` pelo token de autorização retornado na etapa anterior.

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

## <a name="error-http-400-bad-request"></a>Ao`HTTP 400 Bad Request`

Esse motivo é geralmente que o corpo da solicitação contém dados de áudio inválidos. Atualmente, há suporte apenas para o arquivo WAV.

## <a name="error-http-408-request-timeout"></a>Ao`HTTP 408 Request Timeout`

O erro é provavelmente porque nenhum dado de áudio é enviado ao serviço e o serviço retorna esse erro após o tempo limite. Para a API REST, os dados de áudio devem ser colocados no corpo da solicitação.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>O `RecognitionStatus` na resposta é`InitialSilenceTimeout`

Os dados de áudio geralmente são o motivo de causar o problema. Por exemplo,

- o áudio tem um longo tempo de silêncio no início. O serviço interromperá o reconhecimento após um número de segundos e retorna `InitialSilenceTimeout`.
- o áudio usa o formato de codec sem suporte, o que faz com que os dados de áudio sejam tratados como silêncio.
