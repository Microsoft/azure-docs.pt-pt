---
title: Operações de lote CLI de fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: aprender a fazer discurso de lote a texto (reconhecimento de voz), texto de lote para a fala (síntese de fala) com o CLI do discurso.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540061"
---
# <a name="speech-cli-batch-operations"></a>Operações de lote CLI de fala

Tarefas comuns ao utilizar os serviços de Discurso Azure, são operações de lote. Neste artigo, você aprenderá a fazer discurso de lote a texto (reconhecimento de voz), texto de lote para a fala (síntese de fala) com o Discurso CLI. Especificamente, aprenderá como:

* Executar reconhecimento de voz de lote em um diretório de ficheiros áudio
* Executar síntese de fala de lote iterando sobre um `.tsv` arquivo

## <a name="batch-speech-to-text-speech-recognition"></a>Discurso de lote para texto (reconhecimento de voz)

O serviço de discurso é frequentemente utilizado para reconhecer a fala a partir de ficheiros áudio. Neste exemplo, aprenderá a iterar sobre um diretório usando o Speech CLI para capturar a saída de reconhecimento para cada `.wav` ficheiro. A `--files` bandeira é usada para apontar para o diretório onde os ficheiros áudio são armazenados, e o wildcard é usado para dizer ao `*.wav` CLI do Discurso para executar reconhecimento em todos os ficheiros com a extensão `.wav` . A saída para cada ficheiro de reconhecimento é escrita como um valor separado do separado da aba em `speech_output.tsv` .

> [!NOTE]
> O `--threads` argumento também pode ser usado na secção seguinte para `spx synthesize` comandos, e os fios disponíveis dependerão do CPU e da sua percentagem de carga atual.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Segue-se um exemplo da estrutura do ficheiro de saída.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Texto de lote para a fala (síntese da fala)

A maneira mais fácil de executar o lote texto-a-fala é criar um novo `.tsv` ficheiro (separado de valor separado) e usar o `--foreach` comando no CLI do discurso. Pode criar um `.tsv` ficheiro utilizando o seu editor de texto favorito, para este exemplo, vamos chamá-lo: `text_synthesis.tsv`

>[!IMPORTANT]
> Ao copiar o conteúdo deste ficheiro de texto, certifique-se de que o seu ficheiro não tem um **separador** e não espaços entre a localização do ficheiro e o texto. Por vezes, ao copiar o conteúdo deste exemplo, os separadores são convertidos em espaços que fazem com que o `spx` comando falhe quando é executado.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Em seguida, executa um comando para `text_synthesis.tsv` apontar, efetue a síntese em cada `text` campo e escreva o resultado para o caminho correspondente `audio.output` como `.wav` ficheiro.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Este comando é o equivalente a correr `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **para cada** registo no `.tsv` ficheiro.

Algumas coisas a notar:

* Os cabeçalhos da coluna `audio.output` `text` correspondem aos argumentos da linha de comando `--audio output` `--text` e, respectivamente. Argumentos de linha de comando multi-partes como `--audio output` deve ser formatado no ficheiro sem espaços, sem traços principais, e períodos que separam cordas, por exemplo, `audio.output` . Quaisquer outros argumentos existentes da linha de comando podem ser adicionados ao ficheiro como colunas adicionais utilizando este padrão.
* Quando o ficheiro é formatado desta formação, não são necessários argumentos adicionais para `--foreach` .
* Certifique-se de separar cada valor no `.tsv` separador com um **separador**.

No entanto, se tiver um `.tsv` ficheiro como o seguinte exemplo, com cabeçalhos de coluna que não **correspondam aos** argumentos da linha de comando:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Pode substituir estes nomes de campo aos argumentos corretos utilizando a seguinte sintaxe na `--foreach` chamada. Esta é a mesma chamada que acima.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da CLI de Voz](./spx-overview.md)
* [Início rápido do CLI do discurso](./spx-basics.md)
