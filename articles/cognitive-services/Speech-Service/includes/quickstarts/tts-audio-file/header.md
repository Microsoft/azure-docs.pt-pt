---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961339"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para converter texto em discurso sintetizado num ficheiro áudio. O serviço de texto-a-fala fornece inúmeras opções para vozes sintetizadas, sob apoio da [linguagem texto-a-fala.](../../../language-support.md#text-to-speech) Depois de satisfazer alguns pré-requisitos, sintetizar o discurso num ficheiro só dá cinco passos:
> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie um objeto de configuração áudio que especifique o . Nome de ficheiro wav.
> * Crie `SpeechSynthesizer` um objeto utilizando os objetos de configuração de cima.
> * Utilizando `SpeechSynthesizer` o objeto, converta o seu texto em discurso sintetizado, guardando-o no ficheiro áudio especificado.
> * Inspecione `SpeechSynthesizer` os erros devolvidos.
