---
title: Básicos cli da fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a ferramenta de comando Do Discurso CLI para trabalhar com o Serviço de Fala sem código e configuração mínima.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 2e75e177c1a5af13c1907b3a1abc9218096e8d45
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800671"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Conheça o básico do Discurso CLI

Neste artigo, você aprende os padrões básicos de uso do Speech CLI, uma ferramenta de linha de comando para usar o serviço de Fala sem escrever código. Você pode testar rapidamente as principais características do serviço de Fala, sem criar ambientes de desenvolvimento ou escrever qualquer código, para ver se os seus casos de uso podem ser adequadamente cumpridos. Além disso, o Speech CLI está pronto para a produção e pode ser usado para automatizar fluxos de trabalho simples no serviço de Fala, usando `.bat` ou shell scripts.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Utilização básica

Esta secção mostra alguns comandos SPX básicos que são muitas vezes úteis para testes e experimentação pela primeira vez. Comece por realizar algum reconhecimento de voz utilizando o microfone predefinido executando o seguinte comando.

```shell
spx recognize --microphone
```

Depois de entrar no comando, o SPX começará a ouvir áudio no dispositivo de entrada ativa atual e parará depois de premir `ENTER` . O discurso gravado é então reconhecido e convertido em texto na saída da consola. A síntese texto-a-fala também é fácil de fazer usando o CLI da fala. 

Executar o seguinte comando tomará o texto introduzido como entrada e produza a fala sintetizada para o dispositivo de saída ativo atual.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Além do reconhecimento da fala e da síntese, também pode fazer tradução da fala com o Discurso CLI. Semelhante ao comando de reconhecimento de voz acima, execute o seguinte comando para capturar áudio do microfone predefinido e execute a tradução para texto na linguagem alvo.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

Neste comando, especifica tanto a fonte (idioma a **traduzir)** como o alvo (idioma a traduzir **para**) línguas. A utilização do argumento ouvirá áudio no dispositivo de entrada ativa atual e para depois de `--microphone` premir `ENTER` . A saída é uma tradução de texto para a linguagem-alvo, escrita para um ficheiro de texto.

> [!NOTE]
> Consulte a língua e o [artigo local](language-support.md) para obter uma lista de todas as línguas apoiadas com os respetivos códigos locais.

## <a name="batch-operations"></a>Operações de lote

Os comandos na secção anterior são ótimos para ver rapidamente como funciona o serviço da Fala. No entanto, ao avaliar se os seus casos de utilização podem ou não ser cumpridos, é provável que necessite de efetuar operações de lote contra uma série de entradas que já tem, para ver como o serviço lida com uma variedade de cenários. Esta secção mostra como:

* Executar reconhecimento de discurso de lote em um diretório de ficheiros áudio
* Iterado através de um `.tsv` ficheiro e executar síntese texto-a-fala

## <a name="batch-speech-recognition"></a>Reconhecimento da fala do lote

Se tiver um diretório de ficheiros áudio, é fácil com o Talk CLI executar rapidamente o reconhecimento da fala em lote. Basta executar o seguinte comando, apontando para o seu diretório com o `--files` comando. Neste exemplo, `\*.wav` anexa-se ao diretório para reconhecer todos os `.wav` ficheiros presentes no dir. Além disso, especifique o `--threads` argumento para executar o reconhecimento em 10 linhas paralelas.

> [!NOTE]
> O `--threads` argumento também pode ser usado na próxima secção para `spx synthesize` comandos, e os fios disponíveis dependerão do CPU e da sua percentagem de carga atual.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

A saída de discurso reconhecida é escrita para `speech_output.tsv` usar o `--output file` argumento. Segue-se um exemplo da estrutura do ficheiro de saída.

    audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
    sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
    sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.

## <a name="batch-text-to-speech-synthesis"></a>Síntese de texto-fala do lote

A maneira mais fácil de executar o texto-a-fala do lote é criar um novo `.tsv` ficheiro (separado de separados) e alavancar o `--foreach` comando no CLI da fala. Considere o seguinte `text_synthesis.tsv` ficheiro:

    audio.output    text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

 Em seguida, executa um comando para `text_synthesis.tsv` apontar, executar síntese em cada `text` campo, e escrever o resultado para o caminho correspondente `audio.output` como um `.wav` arquivo. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Este comando é o equivalente a correr `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **para cada** registo no `.tsv` ficheiro. Algumas coisas a notar:

* Os cabeçalhos da `audio.output` coluna, `text` e, correspondem aos argumentos da linha de comando `--audio output` `--text` e, respectivamente. Argumentos de linha de comando multi-partes como `--audio output` devem ser formatados no ficheiro sem espaços, sem traços principais, e períodos que separam cordas, por `audio.output` exemplo. Quaisquer outros argumentos de linha de comando existentes podem ser adicionados ao ficheiro como colunas adicionais usando este padrão.
* Quando o ficheiro for formatado desta forma, não são necessários argumentos adicionais para `--foreach` .
* Certifique-se de separar cada valor no `.tsv` **separador**.

No entanto, se tiver um `.tsv` ficheiro como o seguinte exemplo, com cabeçalhos de coluna que não **correspondam** aos argumentos da linha de comando:

    wav_path    str_text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

Pode substituir estes nomes de campo com os argumentos corretos utilizando a seguinte sintaxe na `--foreach` chamada. Esta é a mesma chamada que acima.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Passos seguintes

* Complete o [reconhecimento](./quickstarts/speech-to-text-from-microphone.md) da fala ou [a síntese da fala](./quickstarts/text-to-speech.md) começa rapidamente usando o SDK.
