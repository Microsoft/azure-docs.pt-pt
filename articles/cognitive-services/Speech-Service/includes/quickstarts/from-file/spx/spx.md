---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806480"
---
## <a name="find-a-file-that-contains-speech"></a>Encontre um ficheiro que contenha fala

O Speech CLI pode reconhecer a fala em muitos formatos de ficheiros e línguas naturais. Para este arranque rápido, pode utilizar um ficheiro WAV (16kHz ou 8kHz, 16-bit s e mono PCM) que contenha a fala inglesa.

1. Baixe o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a>.
2. Copie o `whatstheweatherlike.wav` ficheiro para o mesmo diretório que o ficheiro binário do Speech CLI.

## <a name="run-the-speech-cli"></a>Executar o ClI do Discurso

Agora estás pronto para executar o DISCURSO CLI para reconhecer o discurso encontrado no ficheiro de som.

Da linha de comando, mude para o diretório que contém o ficheiro binário CLI da fala, e escreva:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> O Discurso CLI falha em inglês. Pode escolher uma língua diferente [da tabela Discurso-a-texto](../../../../language-support.md).
> Por exemplo, adicione `--source de-DE` a reconhecer o discurso alemão.

O Discurso CLI mostrará uma transcrição de texto do discurso no ecrã. Então o CLI da fala vai fechar.
