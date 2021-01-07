---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a752b723980c45e1488f1471ee8139190853b54c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97966638"
---
Neste arranque rápido, você usará o serviço [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço language understanding (LUIS) para reconhecer as intenções de dados áudio capturados a partir de um microfone. Especificamente, você usará o Speech SDK para capturar o discurso, e um domínio pré-construído da LUIS para identificar intenções para a domótica, como acender e desligar uma luz. 

Depois de satisfazer alguns pré-requisitos, reconhecer a fala e identificar intenções de um microfone apenas dá alguns passos:

> [!div class="checklist"]
>
> * Crie um `SpeechConfig` objeto a partir da sua chave de subscrição e região.
> * Crie um `IntentRecognizer` objeto utilizando o objeto de `SpeechConfig` cima.
> * Utilizando o `IntentRecognizer` objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione o `IntentRecognitionResult` devolvido.

> [!NOTE]
> Pode criar um LanguageUnderstandingModel passando um URL de ponto final para o método FromEndpoint.
> A SDK da fala só suporta pontos finais LUIS v2.0, e os pontos finais LUIS v2.0 seguem sempre um destes dois padrões:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
