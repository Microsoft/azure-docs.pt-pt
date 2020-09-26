---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332465"
---
Neste arranque rápido, você aprende padrões de design comuns para fazer síntese de texto-a-fala usando o SDK do discurso. Começa por fazer configuração e síntese básicas e passa a exemplos mais avançados para o desenvolvimento de aplicações personalizadas, incluindo:

* Obtenção de respostas como fluxos de memória
* Personalização da taxa de amostra de saída e da taxa de bits
* Submeter pedidos de síntese utilizando SSML (linguagem de marcação da síntese da fala)
* Usando vozes neurais

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

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