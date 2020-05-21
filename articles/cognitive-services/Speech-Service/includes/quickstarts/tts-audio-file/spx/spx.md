---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 161138ba09baacdd2a3d18d35c930c645ee78103
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715184"
---
## <a name="run-the-spx-tool"></a>Executar a ferramenta SPX

Agora está pronto para executar a ferramenta SPX para sintetizar o discurso do texto para um novo ficheiro áudio.

Da linha de comando, mude para o diretório que contém ficheiro binário de ferramentaS SPX e escreva:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A ferramenta SPX produzirá linguagem natural em inglês no `greetings.wav` ficheiro áudio.
No Windows, pode reproduzir o ficheiro áudio entrando `start greetings.wav` .
