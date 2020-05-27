---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806284"
---
## <a name="run-the-speech-cli"></a>Executar o ClI do Discurso

Agora estás pronto para executar o Discurso CLI para sintetizar o discurso do texto para um novo ficheiro áudio.

Da linha de comando, mude para o diretório que contém o ficheiro binário CLI da fala, e escreva:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

O Speech CLI produzirá linguagem natural em inglês no `greetings.wav` ficheiro áudio.
No Windows, pode reproduzir o ficheiro áudio entrando `start greetings.wav` .
