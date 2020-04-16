---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400400"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para converter texto em discurso sintetizado. O serviço de texto-a-fala fornece inúmeras opções para vozes sintetizadas, sob apoio da [linguagem texto-a-fala.](../../../language-support.md#text-to-speech) Depois de satisfazer alguns pré-requisitos, a renderização da fala sintetizada aos altifalantes padrão só dá quatro passos:
> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `SpeechSynthesizer` um `SpeechConfig` objeto utilizando o objeto de cima.
> * Usando `SpeechSynthesizer` o objeto para falar o texto.
> * Verifique `SpeechSynthesisResult` se os erros devolvidos.
