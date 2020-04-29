---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400285"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para converter texto em discurso sintetizado num ficheiro áudio. O serviço de texto-a-fala fornece inúmeras opções para vozes sintetizadas, sob apoio da [linguagem texto-a-fala.](../../../language-support.md#text-to-speech) Depois de satisfazer alguns pré-requisitos, sintetizar o discurso num ficheiro só dá cinco passos:
> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie um objeto de configuração áudio que especifique o . Nome de ficheiro wav.
> * Crie `SpeechSynthesizer` um objeto utilizando os objetos de configuração de cima.
> * Utilizando `SpeechSynthesizer` o objeto, converta o seu texto em discurso sintetizado, guardando-o no ficheiro áudio especificado.
> * Inspecione `SpeechSynthesizer` os erros devolvidos.
