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
ms.openlocfilehash: acc19d9a04909dcf0e79c93e0c8a3fb8225ee1b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546914"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Conheça os fundamentos do Discurso CLI

Neste artigo, você aprende os padrões básicos de uso do CLI do discurso, uma ferramenta de linha de comando para usar o serviço De Discurso sem escrever código. Pode testar rapidamente as principais características do serviço Speech, sem criar ambientes de desenvolvimento ou escrever qualquer código, para ver se os seus casos de uso podem ser adequadamente cumpridos. O Speech CLI está pronto para a produção e pode ser usado para automatizar fluxos de trabalho simples no serviço Discurso, utilizando `.bat` ou shell scripts.

Este artigo pressupõe que você tem conhecimento de trabalho da solicitação de comando, terminal ou PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Utilização básica

Esta secção mostra alguns comandos SPX básicos que são frequentemente úteis para testes e experimentação pela primeira vez. Comece por visualizar a ajuda incorporada na ferramenta executando o seguinte comando.

```shell
spx
```

Nota **ver:** tópicos de ajuda listados direito de parâmetros de comando. Pode introduzir estes comandos para obter ajuda detalhada sobre sub-comandos.

Pode pesquisar tópicos de ajuda por palavra-chave. Por exemplo, insira o seguinte comando para ver uma lista de exemplos de utilização do CLI do discurso:

```shell
spx help find --topics "examples"
```

Introduza o seguinte comando para ver opções para o comando de reconhecimento:

```shell
spx help recognize
```

Agora, vamos usar o CLI do discurso para realizar o reconhecimento da fala usando o microfone padrão do seu sistema. 

>[!WARNING]
> Se estiver a usar um contentor Docker, este comando não funcionará.

Execute este comando:

```shell
spx recognize --microphone
```

Com o CLI do Discurso também pode reconhecer a fala a partir de um ficheiro áudio.

```shell
spx recognize --file /path/to/file.wav
```
> [!TIP]
> Se estiver a reconhecer a fala a partir de um ficheiro áudio num recipiente Docker, certifique-se de que o ficheiro áudio está localizado no diretório que montou no degrau anterior.

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

### <a name="configuration-files-in-the-datastore"></a>Ficheiros de configuração na loja de dados

O comportamento do CLI do discurso pode depender de definições em ficheiros de configuração, aos quais pode consultar-se dentro das chamadas CLI do Discurso usando um símbolo @.
O CLI da fala salva uma nova configuração num novo `./spx/data` subdiretório que cria no diretório de trabalho atual.
Ao procurar um valor de configuração, o Speech CLI olha para o seu diretório de trabalho atual, depois na loja de dados `./spx/data` em , e depois em outras datastores, incluindo uma loja de dados apenas de leitura final no `spx` binário.
Anteriormente, utilizou a datastore para guardar os seus `@key` `@region` valores, pelo que não precisava de os especificar com cada chamada de linha de comando.
Também pode utilizar ficheiros de configuração para armazenar as suas próprias definições de configuração, ou até usá-los para passar URLs ou outros conteúdos dinâmicos gerados em tempo de execução.

Esta secção mostra a utilização de um ficheiro de configuração na loja de dados local para armazenar e buscar as definições de comando utilizando `spx config` , e armazenar a saída do Speech CLI utilizando a `--output` opção.

O exemplo a seguir limpa o `@my.defaults` ficheiro de configuração, adiciona pares de valor-chave para **chave** e **região** no ficheiro e utiliza a configuração numa chamada para `spx recognize` .

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Também pode escrever conteúdo dinâmico num ficheiro de configuração. Por exemplo, o seguinte comando cria um modelo de fala personalizado e armazena o URL do novo modelo num ficheiro de configuração. O comando seguinte aguarda até que o modelo dessa URL esteja pronto para ser utilizado antes de regressar.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

O exemplo a seguir escreve dois URLs no `@my.datasets.txt` ficheiro de configuração.
Neste cenário, `--output` pode incluir uma palavra-chave de **adicionar** opcional para criar um ficheiro de configuração ou anexar ao existente.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Para obter mais detalhes sobre ficheiros de datastore, incluindo a utilização de ficheiros de configuração predefinidos `@spx.default` `@default.config` (, , e `@*.default.config` para definições padrão específicas do comando), insira este comando:

```shell
spx help advanced setup
```

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

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um ficheiro

Executar o seguinte comando para alterar a saída do seu altifalante para um `.wav` ficheiro.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

O Speech CLI produzirá linguagem natural em inglês no `greetings.wav` ficheiro áudio.
No Windows, pode reproduzir o ficheiro áudio `start greetings.wav` introduzindo.


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

* Complete os [rápidos](get-started-speech-to-text.md?pivots=programmer-tool-spx) de reconhecimento de voz ou [síntese de fala](get-started-text-to-speech.md?pivots=programmer-tool-spx) utilizando o Speech CLI.
