---
title: Como preparar dados para voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Crie uma voz personalizada para sua marca com os serviços de voz do Azure. Fornecer gravações studio e os scripts associados, o serviço gera um modelo de voz única ajustado para a voz gravada. Utilize essa voz para sintetizar voz em seus produtos, ferramentas e aplicações.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4b49e7453079b848a273aa8c1c706b2d00fff921
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606530"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparar dados para criar uma voz personalizada

Quando estiver pronto para criar uma voz de texto para discurso personalizada para a sua aplicação, a primeira etapa é coletar gravações de áudio e scripts associados para começar a dar formação sobre o modelo de voz. O serviço utiliza estes dados para criar uma voz única ajustada para corresponder à voz nas gravações. Depois de Treinou a voz, pode começar a conversão de voz em seus aplicativos para resumir.

Pode começar com uma pequena quantidade de dados para criar uma prova de conceito. No entanto, quanto mais dados, que fornece, o mais natural soará sua voz personalizada. Antes de pode preparar o seu próprio modelo de texto para discurso de voz, precisará gravações de áudio e transcrições de texto associado. Nesta página, vamos rever os tipos de dados, como são utilizadas e como gerir cada um.

## <a name="data-types"></a>Tipos de dados

Um conjunto de dados de formação de voz inclui gravações de áudio e um arquivo de texto com os transcrições associados. Cada arquivo de áudio deve conter uma única expressão (uma frase única ou uma única ligar-se para um sistema de caixa de diálogo) e ter menos de 15 segundos de duração.

Em alguns casos, pode não ter o conjunto de dados certo pronto e vai querer testar a formação de voz personalizada com disponíveis arquivos de áudio, curtos ou longos, com ou sem transcrições. Fornecemos ferramentas (beta) para ajudar a dividir seu áudio em expressões e preparar transcrições utilizando o [API do Batch transcrição](batch-transcription.md).

Esta tabela lista os tipos de dados e como cada um é utilizado para criar um modelo de texto para discurso de voz personalizada.

| Tipo de dados | Descrição | Quando utilizar | Serviço adicional necessário | Quantidade para preparar um modelo | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Expressões com individuais + correspondente da transcrição** | Uma coleção (. zip) de arquivos de áudio (. wav) como expressões com individuais. Cada arquivo de áudio deve ser 15 segundos ou menos, emparelhado com uma transcrição formatada (. txt). | Profissionais gravações com correspondentes transcrições | Pronto para treinamento. | Sem requisito de disco rígido para en-US e zh-CN. Mais de 2.000 + distintos expressões com outras localidades. | Todas as localidades de voz personalizada |
| **Comprimento de áudio + transcrições (beta)** | Uma coleção (. zip) de longas e não-segmentados arquivos de áudio (mais de 20 segundos), emparelhado com uma transcrição (. txt), que contém todas as palavras faladas. | Tiver arquivos de áudio e transcrições correspondentes, mas eles não são segmentados em expressões. | Segmentação (através de transcrição do batch).<br>Transformação de formato de áudio onde for necessário. | Sem requisito de disco rígido para en-US e zh-CN. | `en-US` e `zh-CN` |
| **Apenas áudio (beta)** | Uma coleção (. zip) de arquivos de áudio sem uma transcrição. | Tem apenas de arquivos de áudio disponíveis, sem transcrições. | Segmentação + geração de transcrição (através de transcrição do batch).<br>Transformação de formato de áudio onde for necessário.| Nenhum requisito rígido quanto `en-US` e `zh-CN`. | `en-US` e `zh-CN` |

Os ficheiros devem ser agrupados por tipo para um conjunto de dados e carregados como um ficheiro zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!NOTE]
> O número máximo de conjuntos de dados podem ser importados por subscrição é. zip 10 ficheiros, gratuitamente, os utilizadores de subscrição (F0) e 500 para utilizadores de subscrição standard (S0).

## <a name="individual-utterances--matching-transcript"></a>Expressões com individuais + correspondente da transcrição

Pode preparar as gravações de expressões com individuais e a transcrição correspondente de duas formas. A escrever um script e fazê-lo lida por um talento de voz ou usar áudio disponível ao público e transcrever para texto. Se fizer a última opção, edite disfluencies dos arquivos de áudio, como "um" e outros sons de preenchimento, stutters, mumbled palavras ou mispronunciations.

Para produzir um tipo de voz boa, crie as gravações num ambiente silencioso com um microfone de alta qualidade. Volume consistente, falando taxa, fala pitch e expressivas mannerisms de voz são essenciais.

> [!TIP]
> Para criar uma voz para uso em produção, recomendamos que usar o talento de studio e de voz de gravação profissional. Para obter mais informações, consulte [exemplos de como grave voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter uma única expressão (uma frase única ou uma única ligar-se de um sistema de caixa de diálogo), menos de 15 segundos de duração. Todos os ficheiros têm de estar no mesmo idioma falado. Vários idiomas vozes de texto para voz personalizadas não são suportadas, com exceção do chinês-inglês bi-multilingues. Cada arquivo de áudio tem de ter um nome de ficheiro numérico exclusivo com a extensão de nome de arquivo. wav.

Siga estas diretrizes quando preparar o áudio.

| Propriedade | Value |
| -------- | ----- |
| Formato de ficheiro | RIFF (. wav), agrupadas num arquivo. zip |
| Frequência de amostragem | pelo menos de 16 000 Hz |
| Formato de exemplo | PCM, 16 bits |
| Nome de ficheiro | Numéricos, com a extensão. wav. Não existem nomes de ficheiros duplicados são permitidos. |
| Comprimento de áudio | Menos de 15 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

> [!NOTE]
> arquivos. wav com uma taxa de amostragem mais baixo do que 16.000 Hz serão rejeitados. Se um ficheiro. zip contém arquivos. wav com taxas de exemplo diferentes, apenas esses igual ou maior do que 16.000 Hz serão importados. O portal atualmente. zip de importações arquiva até 200 MB. No entanto, podem ser carregados vários arquivos.

### <a name="transcripts"></a>Transcrições

O arquivo de transcrição é um ficheiro de texto sem formatação. Utilize estas diretrizes para preparar sua transcrições.

| Propriedade | Value |
| -------- | ----- |
| Formato de ficheiro | Texto sem formatação (. txt) |
| Formato de codificação | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE. Para zh-CN, codificações ANSI/ASCII e UTF-8 não são suportadas. |
| N.º de expressões por linha | **Um** -cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguidos de transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t). |
| Tamanho máximo do ficheiro | 50 MB |

Segue-se um exemplo de como as transcrições são organizadas expressão pela expressão num arquivo. txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
É importante que as transcrições são transcrições de precisão de 100% do áudio correspondente. Erros nas transcrições apresentará a perda de qualidade durante o treinamento.

> [!TIP]
> Ao criar produção texto para discurso vozes expressões com selecionadas (ou escrever scripts) que levam em conta cobertura fonética e eficiência. A ter problemas ao obter os resultados, deseja? [Entre em contato com a voz personalizada](mailto:speechsupport@microsoft.com) equipe para descobrir mais sobre-na ter consulte.

## <a name="long-audio--transcript-beta"></a>Comprimento de áudio + transcrições (beta)

Em alguns casos, pode não ter segmentadas áudio disponível. Fornecemos um serviço (beta) através do portal de voz personalizada para o ajudar a segmentar arquivos de tempo de áudio e transcrições de criar. Tenha em mente, este serviço será faturado em direção a utilização de subscrição de voz em texto.

> [!NOTE]
> O serviço de segmentação de áudio longa irá tirar partido da funcionalidade de transcrição de batch de voz em texto, que suporta apenas a utilizadores de subscrição standard (S0). Durante o processamento da segmentação, seus arquivos de áudio e as transcrições serão também enviadas para o serviço de voz personalizadas para refinar o modelo de reconhecimento, pelo que a precisão pode ser melhorada para os seus dados. Não existem dados ficarão retidos durante este processo. Depois de terminar a segmentação, apenas as expressões segmentadas e suas transcrições de mapeamento serão armazenadas para o download e treinamento.

### <a name="audio-files"></a>Arquivos de áudio

Siga estas diretrizes quando preparar o áudio de segmentação.

| Propriedade | Value |
| -------- | ----- |
| Formato de ficheiro | RIFF (. wav) com uma taxa de amostragem de, pelo menos, 16 khz-16 bits no PCM ou. mp3, com uma taxa de bits de, pelo menos, 256 KBps, agrupadas num arquivo. zip |
| Nome de ficheiro | Carateres ASCII. Caracteres Unicode no nome irão falhar (por exemplo, os caracteres do chinês ou símbolos como "—"). Não existem nomes duplicados são permitidos. |
| Comprimento de áudio | Há mais do que 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

Todos os arquivos de áudio deverão ser agrupados num arquivo zip. É OK para colocar os arquivos. wav e arquivos. mp3 num postal de áudio, mas nenhuma subpasta é permitida no ficheiro zip. Por exemplo, pode carregar um ficheiro zip que contém um arquivo de áudio com o nome 'kingstory.wav', 45-segundo-longa e outra um nomeado 'queenstory.mp3", 200-segundo-longa, sem qualquer subpasta. Todos os arquivos. mp3 serão transformados no formato. wav após o processamento.

### <a name="transcripts"></a>Transcrições

Transcrições devem estar preparadas para as especificações listadas nesta tabela. Cada arquivo de áudio ser correspondido com uma transcrição.

| Propriedade | Value |
| -------- | ----- |
| Formato de ficheiro | Texto sem formatação (. txt), agrupado num. zip |
| Nome de ficheiro | Utilize o mesmo nome que o arquivo de áudio correspondente |
| Formato de codificação | UTF-8-BOM apenas |
| N.º de expressões por linha | Sem limite |
| Tamanho máximo do ficheiro | 50 MILHÕES |

Todos os ficheiros de transcrições neste tipo de dados devem ser agrupados num arquivo zip. Nenhuma subpasta é permitida no ficheiro zip. Por exemplo, carregar um ficheiro zip que contém um arquivo de áudio com o nome 'kingstory.wav', 45 segundos de duração e outra um nomeado 'queenstory.mp3", 200 segundos há muito tempo. Terá de carregar outro ficheiro zip que contém dois transcrições, um com o nome "kingstory.txt', a outros um 'queenstory.txt'. Dentro de cada arquivo de texto sem formatação, deve fornecer a transcrição completa correta para o áudio correspondente.

Depois do conjunto de dados é carregado com êxito, ajudaremos dividir o arquivo de áudio em expressões com base na transcrição fornecida. Pode verificar as expressões segmentadas e as transcrições correspondentes ao transferir o conjunto de dados. IDs exclusivos serão atribuídos automaticamente para as expressões segmentadas. É importante que verifique se as transcrições que é fornecer são 100% precisa. Erros nas transcrições podem reduzir a precisão durante a segmentação de áudio e introduzir mais perda de qualidade na fase de treinamento que vem mais tarde.

## <a name="audio-only-beta"></a>Apenas áudio (beta)

Se não tiver transcrições do seu gravações de áudio, utilize o **apenas áudio** opção de carregar os dados. Nosso sistema pode ajudá-lo a segmentar e transcrição de seus arquivos de áudio. Tenha em mente, este serviço será contam para a utilização de subscrição de voz em texto.

Siga estas diretrizes quando preparar o áudio.

> [!NOTE]
> O serviço de segmentação de áudio longa irá tirar partido da funcionalidade de transcrição de batch de voz em texto, que suporta apenas a utilizadores de subscrição standard (S0).

| Propriedade | Value |
| -------- | ----- |
| Formato de ficheiro | RIFF (. wav) com uma taxa de amostragem de, pelo menos, 16 khz-16 bits no PCM ou. mp3, com uma taxa de bits de, pelo menos, 256 KBps, agrupadas num arquivo. zip |
| Nome de ficheiro | Carateres ASCII. Caracteres Unicode no nome irão falhar (por exemplo, os caracteres do chinês ou símbolos como "—"). Não permitido um nome duplicado. |
| Comprimento de áudio | Há mais do que 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

Todos os arquivos de áudio deverão ser agrupados num arquivo zip. Nenhuma subpasta é permitida no ficheiro zip. Depois do conjunto de dados é carregado com êxito, ajudaremos dividir o arquivo de áudio em expressões com base no nosso serviço de transcrição de batch de voz. IDs exclusivos serão atribuídos automaticamente para as expressões segmentadas. Correspondência de transcrições será gerado por meio de reconhecimento de fala. Todos os arquivos. mp3 serão transformados no formato. wav após o processamento. Pode verificar as expressões segmentadas e as transcrições correspondentes ao transferir o conjunto de dados.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia de: Gravar seus exemplos de voz](record-custom-voice-samples.md)
