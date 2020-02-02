---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961558"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para converter texto em discurso sintetizado. O serviço de texto-a-fala fornece inúmeras opções para vozes sintetizadas, sob apoio da [linguagem texto-a-fala.](../../../language-support.md#text-to-speech) Depois de satisfazer alguns pré-requisitos, a renderização da fala sintetizada aos altifalantes padrão só dá quatro passos:
> [!div class="checklist"]
> * Crie um objeto de `SpeechConfig` de sua chave de assinatura e região.
> * Crie um objeto `SpeechSynthesizer` utilizando o objeto `SpeechConfig` de cima.
> * Usando o `SpeechSynthesizer` oobjecto para falar o texto.
> * Verifique se o `SpeechSynthesisResult` devolvido por erros.
