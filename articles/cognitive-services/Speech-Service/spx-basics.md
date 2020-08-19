---
title: Básicos do CLI da fala
titleSuffix: Azure Cognitive Services
description: Aprenda a utilizar a ferramenta de comando CLI do Discurso para trabalhar com o Serviço de Fala sem código e configuração mínima.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 2f5a1d190c6e63056c2377641446f617edaa1bd3
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590222"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Conheça os fundamentos do Discurso CLI

Neste artigo, você aprende os padrões básicos de uso do CLI do discurso, uma ferramenta de linha de comando para usar o serviço De Discurso sem escrever código. Pode testar rapidamente as principais características do serviço Speech, sem criar ambientes de desenvolvimento ou escrever qualquer código, para ver se os seus casos de uso podem ser adequadamente cumpridos. Além disso, o CLI do Discurso está pronto para a produção e pode ser usado para automatizar fluxos de trabalho simples no serviço Discurso, utilizando `.bat` ou shell scripts.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Utilização básica

Esta secção mostra alguns comandos SPX básicos que são frequentemente úteis para testes e experimentação pela primeira vez. Comece por visualizar a ajuda incorporada na ferramenta executando o seguinte comando.

```shell
spx
```

Note **ver também** tópicos de ajuda listados direito de parâmetros de comando. Também pode pesquisar tópicos por palavra-chave. Por exemplo, insira o seguinte comando para ver uma lista de tópicos de ajuda sobre exemplos de CLI do discurso:

```shell
spx help find --topics "examples"
```

Agora utilize o serviço Desempenhado para realizar algum reconhecimento de voz utilizando o microfone predefinido executando o seguinte comando.

```shell
spx recognize --microphone
```

Depois de entrar no comando, o SPX começará a ouvir áudio no dispositivo de entrada ativo atual e para depois de premir `ENTER` . O discurso gravado é então reconhecido e convertido em texto na saída da consola. A síntese de texto-a-discurso também é fácil de fazer utilizando o CLI do discurso. 

Executando o seguinte comando tomará o texto introduzido como entrada e irá colocar a fala sintetizada para o dispositivo de saída ativo atual.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Além do reconhecimento da fala e da síntese, também pode fazer tradução de discurso com o Discurso CLI. Semelhante ao comando de reconhecimento de voz acima, execute o seguinte comando para capturar áudio do microfone padrão e execute a tradução para texto no idioma alvo.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

Neste comando, especifica tanto a origem (linguagem a **traduzir),** como o alvo (linguagem para traduzir **para)** línguas. A utilização do `--microphone` argumento ouvirá áudio no dispositivo de entrada ativo atual e para depois de premir `ENTER` . A saída é uma tradução de texto para a língua-alvo, escrita num ficheiro de texto.

> [!NOTE]
> Consulte o [idioma e o artigo local](language-support.md) para obter uma lista de todas as línguas suportadas com os respetivos códigos locais.

## <a name="batch-operations"></a>Operações de lote

Os comandos na secção anterior são ótimos para ver rapidamente como funciona o serviço Discurso. No entanto, ao avaliar se os seus casos de utilização podem ou não ser atendidos, é provável que necessite de realizar operações de lote contra uma gama de entradas que já tem, para ver como o serviço lida com uma variedade de cenários. Esta secção mostra como:

* Executar reconhecimento de voz de lote em um diretório de ficheiros áudio
* Iterar através de um `.tsv` ficheiro e executar síntese de texto-a-fala de lote

## <a name="batch-speech-recognition"></a>Reconhecimento de voz de lote

Se tiver um diretório de ficheiros áudio, é fácil com o CLI do Discurso executar rapidamente o reconhecimento da fala de lote. Basta executar o seguinte comando, apontando para o seu diretório com o `--files` comando. Neste exemplo, você anexa `\*.wav` ao diretório para reconhecer todos os `.wav` ficheiros presentes no dir. Além disso, especifique o `--threads` argumento para executar o reconhecimento em 10 linhas paralelas.

> [!NOTE]
> O `--threads` argumento também pode ser usado na secção seguinte para `spx synthesize` comandos, e os fios disponíveis dependerão do CPU e da sua percentagem de carga atual.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

A produção de discurso reconhecida é escrita para `speech_output.tsv` usar o `--output file` argumento. Segue-se um exemplo da estrutura do ficheiro de saída.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Síntese de texto-a-fala do lote

A maneira mais fácil de executar o lote texto-a-fala é criar um novo `.tsv` ficheiro (separado de valor separado) e aproveitar o `--foreach` comando no CLI do discurso. Considere o seguinte `text_synthesis.tsv` ficheiro:

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Em seguida, executa um comando para `text_synthesis.tsv` apontar, efetue a síntese em cada `text` campo e escreva o resultado para o caminho correspondente `audio.output` como `.wav` ficheiro. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Este comando é o equivalente a correr `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **para cada** registo no `.tsv` ficheiro. Algumas coisas a notar:

* Os cabeçalhos da coluna `audio.output` `text` correspondem aos argumentos da linha de comando `--audio output` `--text` e, respectivamente. Argumentos de linha de comando multi-partes como `--audio output` deve ser formatado no ficheiro sem espaços, sem traços principais, e períodos que separam cordas, por `audio.output` exemplo. Quaisquer outros argumentos existentes da linha de comando podem ser adicionados ao ficheiro como colunas adicionais usando este padrão.
* Quando o ficheiro é formatado desta formação, não são necessários argumentos adicionais para `--foreach` .
* Certifique-se de separar cada valor no `.tsv` separador com um **separador**.

No entanto, se tiver um `.tsv` ficheiro como o seguinte exemplo, com cabeçalhos de coluna que não **correspondam aos** argumentos da linha de comando:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Pode substituir estes nomes de campo aos argumentos corretos utilizando a seguinte sintaxe na `--foreach` chamada. Esta é a mesma chamada que acima.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Passos seguintes

* Complete os [rápidos](./quickstarts/speech-to-text-from-microphone.md) de reconhecimento de voz ou [síntese de fala](./quickstarts/text-to-speech.md) utilizando o SDK.
