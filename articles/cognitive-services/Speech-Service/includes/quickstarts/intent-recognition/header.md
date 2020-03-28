---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900585"
---
Neste arranque rápido, utilizará o serviço [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) e o Language Understanding (LUIS) para reconhecer as intenções a partir de dados áudio captados a partir de um microfone. Especificamente, você usará o SDK de discurso para capturar o discurso, e um domínio pré-construído do LUIS para identificar intençãos de automação doméstica, como ligar e apagar uma luz. 

Depois de satisfazer alguns pré-requisitos, reconhecer a fala e identificar as intenções de um microfone dá apenas alguns passos:

> [!div class="checklist"]
>
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `IntentRecognizer` um `SpeechConfig` objeto utilizando o objeto de cima.
> * Utilizando `IntentRecognizer` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione `IntentRecognitionResult` os devolvidos.
