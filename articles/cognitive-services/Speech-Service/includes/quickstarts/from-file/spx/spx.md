---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9ff21105e61be59b0000d86455ee19ba6f0d1aeb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714698"
---
## <a name="find-a-file-that-contains-speech"></a>Encontre um ficheiro que contenha fala

A ferramenta SPX pode reconhecer a fala em muitos formatos de ficheiros e línguas naturais. Para este arranque rápido, pode utilizar um ficheiro WAV (16kHz ou 8kHz, 16-bit s e mono PCM) que contenha a fala inglesa.

1. Baixe o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Copie o `whatstheweatherlike.wav` ficheiro para o mesmo diretório que o ficheiro binário da ferramenta SPX.

## <a name="run-the-spx-tool"></a>Executar a ferramenta SPX

Agora estápronto para executar a ferramenta SPX para reconhecer o discurso encontrado no ficheiro de som.

Da linha de comando, mude para o diretório que contém o ficheiro binário da ferramenta SPX e escreva:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> A ferramenta SPX falha em inglês. Pode escolher uma língua diferente [da tabela Discurso-a-texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` a reconhecer o discurso alemão.

A ferramenta SPX mostrará uma transcrição de texto do discurso no ecrã. Em seguida, a ferramenta SPX fechará.
