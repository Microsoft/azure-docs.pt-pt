---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400682"
---
Neste arranque rápido, você usa o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer interativamente o discurso a partir de uma entrada de microfone, e obter a transcrição de texto a partir de áudio capturado. É fácil integrar esta funcionalidade nas suas apps ou dispositivos para tarefas comuns de reconhecimento, como a transcrição de conversas. Também pode ser usado para integrações mais complexas, como usar o Quadro Bot com o SDK de Fala para construir assistentes de voz.

Depois de satisfazer alguns pré-requisitos, reconhecer o discurso de um microfone só dá quatro passos:

> [!div class="checklist"]
> * Crie `SpeechConfig` um objeto a partir da sua chave de subscrição e região.
> * Crie `SpeechRecognizer` um `SpeechConfig` objeto utilizando o objeto de cima.
> * Utilizando `SpeechRecognizer` o objeto, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione `SpeechRecognitionResult` os devolvidos.
