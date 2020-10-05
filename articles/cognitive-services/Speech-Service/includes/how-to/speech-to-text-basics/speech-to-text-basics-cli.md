---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327127"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer e transcrever a fala humana (muitas vezes referida como discurso-a-texto). Neste arranque rápido, aprende-se a usar o CLI do Discurso nas suas apps e produtos para realizar conversão de fala a texto de alta qualidade.

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