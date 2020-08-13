---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173135"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Sintetizar a voz num altifalante

Agora está pronto para executar o Discurso CLI para sintetizar o discurso a partir do texto. Da linha de comando, mude para o diretório que contém o ficheiro binário Speech CLI. Em seguida, executar o seguinte comando.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

O Speech CLI produzirá linguagem natural em inglês através do altifalante de computador.

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um ficheiro

Executar o seguinte comando para alterar a saída do seu altifalante para um `.wav` ficheiro.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

O Speech CLI produzirá linguagem natural em inglês no `greetings.wav` ficheiro áudio.
No Windows, pode reproduzir o ficheiro áudio `start greetings.wav` introduzindo.