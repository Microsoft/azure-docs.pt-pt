---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422300"
---
Neste arranque rápido, utilizará o serviço [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e o Language Understanding (LUIS) para reconhecer as intenções a partir de dados áudio captados a partir de um microfone. Especificamente, você usará o SDK de discurso para capturar o discurso, e um domínio pré-construído do LUIS para identificar intençãos de automação doméstica, como ligar e apagar uma luz. 

Depois de satisfazer alguns pré-requisitos, reconhecer a fala e identificar as intenções de um microfone dá apenas alguns passos:

> [!div class="checklist"]
>
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `IntentRecognizer` um `SpeechConfig` objeto utilizando o objeto de cima.
> * Utilizando `IntentRecognizer` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione `IntentRecognitionResult` os devolvidos.
