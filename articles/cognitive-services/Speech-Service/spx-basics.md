---
title: Início rápido do CLI do discurso - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Começa com o CLI do Discurso azul. Pode interagir com serviços de fala como discurso a texto, texto a fala e tradução de fala sem código de escrita.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 53138a22c58e89ade4af234630e9429a19738a6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556473"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Começa com o Azure Speech CLI

Neste artigo, você aprenderá a usar o CLI do Discurso, uma interface de linha de comando, para aceder a serviços de fala como fala a texto, texto para fala e tradução de fala sem código de escrita. O Speech CLI está pronto para a produção e pode ser usado para automatizar fluxos de trabalho simples no serviço Discurso, utilizando `.bat` ou shell scripts.

Este artigo pressupõe que você tem conhecimento de trabalho da solicitação de comando, terminal ou PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Utilização básica

Esta secção mostra alguns comandos SPX básicos que são frequentemente úteis para testes e experimentação pela primeira vez. Comece por visualizar a ajuda incorporada na ferramenta executando o seguinte comando.

```console
spx
```

Pode pesquisar tópicos de ajuda por palavra-chave. Por exemplo, insira o seguinte comando para ver uma lista de exemplos de utilização do CLI do discurso:

```console
spx help find --topics "examples"
```

Introduza o seguinte comando para ver opções para o comando de reconhecimento:

```console
spx help recognize
```

Comandos de ajuda adicionais listados na coluna certa. Pode introduzir estes comandos para obter ajuda detalhada sobre subcometrodes.

## <a name="speech-to-text-speech-recognition"></a>Discurso ao texto (reconhecimento da fala)

Vamos usar o CLI do Discurso para converter a fala em texto (reconhecimento de voz) utilizando o microfone padrão do seu sistema. Depois de entrar no comando, o SPX começará a ouvir áudio no dispositivo de entrada ativo atual e para quando premir **ENTER**. O discurso gravado é então reconhecido e convertido em texto na saída da consola.

>[!IMPORTANT]
> Se estiver a usar um contentor Docker, `--microphone` não funcionará.

Execute este comando:

```console
spx recognize --microphone
```

Com o CLI do Discurso, também pode reconhecer a fala a partir de um ficheiro áudio.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Se estiver a reconhecer a fala a partir de um ficheiro áudio num recipiente Docker, certifique-se de que o ficheiro áudio está localizado no diretório que montou no degrau anterior.

Não se esqueça, se ficar preso ou quiser saber mais sobre as opções de reconhecimento do Speech CLI, basta escrever:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Texto para a fala (síntese da fala)

Executar o seguinte comando tomará o texto como entrada e a voz sintetizada para o dispositivo de saída ativo atual (por exemplo, os altifalantes do computador).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Também pode guardar a saída sintetizada para arquivar. Neste exemplo, vamos criar um ficheiro nomeado `my-sample.wav` no diretório que o comando é executado.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

Estes exemplos presumem que está a testar em inglês. No entanto, apoiamos a síntese da fala em muitas línguas. Pode retirar uma lista completa de vozes com este comando, ou visitando a página de [suporte linguístico](./language-support.md).

```console
spx synthesize --voices
```

É assim que se usa uma das vozes que descobriu.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Não se esqueça, se ficar preso ou quiser saber mais sobre as opções de síntese do Speech CLI, basta escrever:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Tradução de texto

Com o Discurso CLI, também pode fazer discurso para tradução de texto. Executar este comando para capturar áudio a partir do microfone predefinido e descoduça a tradução como texto. Tenha em mente que precisa fornecer o `source` e a linguagem com o `target` `translate` comando.

```console
spx translate --microphone --source en-US --target ru-RU
```

Ao traduzir em várias línguas, separe os códigos linguísticos com `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Se quiser guardar a saída da sua tradução, utilize a `--output` bandeira. Neste exemplo, também vai ler a partir de um ficheiro.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Consulte o [idioma e o artigo local](language-support.md) para obter uma lista de todas as línguas suportadas com os respetivos códigos locais.

Não se esqueça, se ficar preso ou quiser saber mais sobre as opções de tradução do Speech CLI, basta escrever:

```console
spx help translate
```

## <a name="next-steps"></a>Passos seguintes

* [Opções de configuração da CLI de Voz](./spx-data-store-configuration.md)
* [Operações de lote com o CLI do Discurso](./spx-batch-operations.md)
