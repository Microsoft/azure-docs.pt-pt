---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "81422300"
---
Neste arranque rápido, você usará o serviço [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço language understanding (LUIS) para reconhecer as intenções de dados áudio capturados a partir de um microfone. Especificamente, você usará o Speech SDK para capturar o discurso, e um domínio pré-construído da LUIS para identificar intenções para a domótica, como acender e desligar uma luz. 

Depois de satisfazer alguns pré-requisitos, reconhecer a fala e identificar intenções de um microfone apenas dá alguns passos:

> [!div class="checklist"]
>
> * Crie um `SpeechConfig` objeto a partir da sua chave de subscrição e região.
> * Crie um `IntentRecognizer` objeto utilizando o objeto de `SpeechConfig` cima.
> * Utilizando o `IntentRecognizer` objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione o `IntentRecognitionResult` devolvido.
