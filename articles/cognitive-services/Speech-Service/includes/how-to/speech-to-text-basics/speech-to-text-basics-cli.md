---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570605"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Discurso-a-texto a partir do microfone

Ligue e ligue o microfone do PC e desligue quaisquer aplicações que possam também utilizar o microfone. Alguns computadores têm um microfone incorporado, enquanto outros requerem a configuração de um dispositivo Bluetooth.

Agora está pronto para executar o Discurso CLI para reconhecer o discurso do seu microfone. Da linha de comando, mude para o diretório que contém o ficheiro binário Speech CLI e execute o seguinte comando.

```bash
spx recognize --microphone
```

> [!NOTE]
> O CLI do Discurso não tem em inglês. Pode escolher uma língua diferente [da tabela Fala-a-texto.](../../../../language-support.md)
> Por exemplo, adicione `--source de-DE` para reconhecer o discurso alemão.

Fala para o microfone e vês a transcrição das tuas palavras em texto em tempo real. O Discurso CLI para após um período de silêncio, ou quando premir ctrl-C.

## <a name="speech-to-text-from-audio-file"></a>Discurso-a-texto a partir de ficheiro áudio

O CLI do Discurso pode reconhecer a fala em muitos formatos de ficheiros e linguagens naturais. Neste exemplo, pode utilizar qualquer ficheiro WAV (16kHz ou 8kHz, 16-bit e mono PCM) que contenha a fala em inglês. Ou se quiser uma amostra rápida, descarregue o ficheiro <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a> e copie-o para o mesmo diretório que o ficheiro binário Speech CLI.

Agora está pronto para executar o CLI do Discurso para reconhecer a fala encontrada no ficheiro áudio executando o seguinte comando.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> O CLI do Discurso não tem em inglês. Pode escolher uma língua diferente [da tabela Fala-a-texto.](../../../../language-support.md)
> Por exemplo, adicione `--source de-DE` para reconhecer o discurso alemão.

O Discurso CLI mostrará uma transcrição de texto do discurso no ecrã.