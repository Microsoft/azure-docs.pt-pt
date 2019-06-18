---
title: Resolver problemas de conversão de voz SDK - serviços de voz
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudar a resolver problemas que poderá encontrar ao utilizar o SDK de voz.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: a3741cb351b11b8cfd7c5d38713bb71232cf010e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515443"
---
# <a name="troubleshoot-the-speech-sdk"></a>Resolver problemas com o SDK de Voz

Este artigo fornece informações para ajudar a resolver problemas que poderá encontrar ao utilizar o SDK de voz.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Erro: Falha ao atualizar o WebSocket com um erro de autenticação (403)

Terá do ponto de extremidade errado para a sua região ou o serviço. Verifique o URI para se certificar de que está correto.

Além disso, pode haver um problema com a sua chave de subscrição ou autorização token. Para obter mais informações, consulte a secção seguinte.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Erro: HTTP 403 proibido ou HTTP 401 não autorizado

Muitas vezes, este erro é causado por problemas de autenticação. Pedidos de ligação sem válido `Ocp-Apim-Subscription-Key` ou `Authorization` cabeçalho são rejeitadas com o estado 403 ou 401.

* Se estiver a utilizar uma chave de subscrição para a autenticação, poderá ver o erro porque:

    - a chave de subscrição está em falta ou é inválido
    - que tenha excedido a quota de utilização da sua subscrição

* Se estiver a utilizar um token de autorização para autenticação, poderá ver o erro porque:

    - o token de autorização é inválido
    - o token de autorização está expirado

### <a name="validate-your-subscription-key"></a>Valide a sua chave de subscrição

Pode verificar que tem uma chave de subscrição válido ao executar um dos seguintes comandos.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` e `YOUR_REGION` com sua própria chave de subscrição e região associada.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Se introduzir uma chave de subscrição válido, o comando devolve um token de autorização, caso contrário, é devolvido um erro.

### <a name="validate-an-authorization-token"></a>Validar um token de autorização

Se utilizar um token de autorização para autenticação, execute um dos seguintes comandos para verificar se o token de autorização é válido. Tokens são válidos durante 10 minutos.

> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` com o caminho para o ficheiro de áudio pré-gravados. Substitua `YOUR_ACCESS_TOKEN` com o token de autorização devolvido no passo anterior. Substitua `YOUR_REGION` com a região correta.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Se tiver introduzido um token de autorização válido, o comando devolve a transcrição para seu arquivo de áudio, caso contrário, que é devolvido um erro.

---

## <a name="error-http-400-bad-request"></a>Erro: Pedido do HTTP 400 inválido

Este erro ocorre normalmente quando o corpo do pedido contém dados de áudio inválidos. Formato WAV apenas é suportado. Além disso, verifique os cabeçalhos da solicitação para se certificar de que especifique valores corretos para o `Content-Type` e `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Erro: Tempo limite do HTTP 408 pedido

O maior probabilidade de erro ocorre porque não existem dados de áudio são enviados para o serviço. Este erro também pode ser causado por problemas de rede.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" na resposta é "InitialSilenceTimeout"

Normalmente, este problema é causado por dados de áudio. Poderá ver este erro porque:

* Há uma longa stretch de silêncio no início do áudio. Nesse caso, o serviço para o reconhecimento após alguns segundos e devolve `InitialSilenceTimeout`.

* O áudio usa um formato de codec não suportado, o que faz com que os dados de áudio deve ser tratada como silêncio.

## <a name="next-steps"></a>Passos Seguintes

* [Reveja as notas de versão](releasenotes.md)
