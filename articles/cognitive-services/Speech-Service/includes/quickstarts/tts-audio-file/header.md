---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961339"
---
Neste arranque rápido, utilizará o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) do Discurso para converter texto em discurso sintetizado num ficheiro áudio. O serviço de texto-a-fala fornece inúmeras opções para vozes sintetizadas, sob apoio da [linguagem texto-a-fala.](../../../language-support.md#text-to-speech) Depois de atender a alguns pré-requisitos, o sintetizamento de fala em um arquivo usa apenas cinco etapas:
> [!div class="checklist"]
> * Crie um objeto de `SpeechConfig` de sua chave de assinatura e região.
> * Crie um objeto de configuração de áudio que especifique o. Nome do arquivo WAV.
> * Crie um `SpeechSynthesizer` objeto utilizando os objetos de configuração de cima.
> * Utilizando o `SpeechSynthesizer` objeto, converta o seu texto em discurso sintetizado, guardando-o no ficheiro áudio especificado.
> * Inspecione o `SpeechSynthesizer` devolvido por erros.
