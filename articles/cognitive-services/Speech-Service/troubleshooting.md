---
title: Resolução de problemas do Discurso SDK - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar quando utilizar o Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74815408"
---
# <a name="troubleshoot-the-speech-sdk"></a>Resolver problemas com o SDK de Voz

Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar quando utilizar o Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Erro: WebSocket Upgrade falhou com um erro de autenticação (403)

Pode ter o ponto final errado para a sua região ou serviço. Verifique o URI para se certificar de que está correto.

Além disso, pode haver um problema com a chave de subscrição ou sinal de autorização. Para mais informações, consulte a secção seguinte.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Erro: HTTP 403 Proibido ou HTTP 401 Não Autorizado

Este erro é frequentemente causado por problemas de autenticação. Os pedidos de ligação sem um cabeçalho válido `Ocp-Apim-Subscription-Key` ou não são `Authorization` rejeitados com um estatuto de 403 ou 401.

* Se estiver a utilizar uma chave de subscrição para autenticação, poderá ver o erro porque:

    - A chave de subscrição está em falta ou inválida
    - Excedeu a quota de utilização da sua subscrição

* Se estiver a usar um sinal de autorização para autenticação, poderá ver o erro porque:

    - O sinal de autorização é inválido
    - O sinal de autorização expirou

### <a name="validate-your-subscription-key"></a>Valide a sua chave de subscrição

Pode verificar se tem uma chave de subscrição válida executando um dos seguintes comandos.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` e pela sua própria chave de `YOUR_REGION` subscrição e região associada.

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

Se introduziu uma chave de subscrição válida, o comando devolve um token de autorização, caso contrário, um erro é devolvido.

### <a name="validate-an-authorization-token"></a>Validar um token de autorização

Se utilizar um sinal de autorização para autenticação, execute um dos seguintes comandos para verificar se o token de autorização ainda é válido. As fichas são válidas por 10 minutos.

> [!NOTE]
> `YOUR_AUDIO_FILE`Substitua-o pelo caminho do seu ficheiro áudio pré-gravado. `YOUR_ACCESS_TOKEN`Substitua-a pelo sinal de autorização devolvido no passo anterior. `YOUR_REGION`Substitua-a pela região correta.

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

Se introduziu um token de autorização válido, o comando devolve a transcrição do seu ficheiro áudio, caso contrário, um erro é devolvido.

---

## <a name="error-http-400-bad-request"></a>Erro: HTTP 400 Mau Pedido

Este erro ocorre geralmente quando o organismo de pedido contém dados áudio inválidos. Apenas o formato WAV é suportado. Além disso, verifique os cabeçalhos do pedido para se certificar de que especifica os valores apropriados para `Content-Type` e `Content-Length` .

## <a name="error-http-408-request-timeout"></a>Erro: HTTP 408 Tempo de pedido

O erro ocorre provavelmente porque não estão a ser enviados dados áudio para o serviço. Este erro também pode ser causado por problemas de rede.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" na resposta é "InitialSilenceTimeout"

Este problema é geralmente causado por dados áudio. Pode ver este erro porque:

* Há uma longa extensão de silêncio no início do áudio. Nesse caso, o serviço para o reconhecimento após alguns segundos e regressa `InitialSilenceTimeout` .

* O áudio usa um formato codec não suportado, o que faz com que os dados áudio sejam tratados como silêncio.

## <a name="next-steps"></a>Passos seguintes

* [Rever as notas de lançamento](releasenotes.md)
