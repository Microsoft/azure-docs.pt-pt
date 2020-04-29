---
title: Como preparar dados para voz personalizada - serviço de fala
titleSuffix: Azure Cognitive Services
description: Crie uma voz personalizada para a sua marca com o serviço Speech. Fornece gravações de estúdio e scripts associados, o serviço gera um modelo de voz único sintonizado com a voz gravada. Use esta voz para sintetizar a fala nos seus produtos, ferramentas e aplicações.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74805982"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparar dados para criar uma voz personalizada

Quando estiver pronto para criar uma voz de texto-a-voz personalizada para a sua aplicação, o primeiro passo é recolher gravações áudio e scripts associados para começar a treinar o modelo de voz. O serviço De Fala utiliza estes dados para criar uma voz única sintonizada para combinar com a voz nas gravações. Depois de treinar a voz, pode começar a sintetizar o discurso nas suas candidaturas.

Pode começar com uma pequena quantidade de dados para criar uma prova de conceito. No entanto, quanto mais dados fornecer, mais natural será a sua voz personalizada. Antes de poder treinar o seu próprio modelo de voz de texto para voz, precisará de gravações áudio e transcrições de texto associadas. Nesta página, vamos rever os tipos de dados, como são usados e como gerir cada um.

## <a name="data-types"></a>Tipos de dados

Um conjunto de dados de treino de voz inclui gravações áudio e um ficheiro de texto com as transcrições associadas. Cada ficheiro áudio deve conter uma única expressão (uma única frase ou uma única volta para um sistema de diálogo) e ter menos de 15 segundos de duração.

Em alguns casos, pode não ter o conjunto de dados certo pronto e quererá testar o treino de voz personalizado com ficheiros áudio disponíveis, curtos ou longos, com ou sem transcrições. Fornecemos ferramentas (beta) para ajudá-lo a segmentar o seu áudio em expressões e preparar transcrições usando a API de [Transcrição](batch-transcription.md)de Lote .

Esta tabela lista os tipos de dados e como cada um é usado para criar um modelo personalizado de voz de texto a voz.

| Tipo de dados | Descrição | Quando utilizar | Serviço adicional necessário | Quantidade para treinar um modelo | Locais(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Expressões individuais + transcrição correspondente** | Uma coleção (.zip) de ficheiros áudio (.wav) como expressões individuais. Cada ficheiro áudio deve ter 15 segundos ou menos de comprimento, emparelhado com uma transcrição formatada (.txt). | Gravações profissionais com transcrições correspondentes | Pronto para o treino. | Nenhum requisito difícil para en-US e zh-CN. Mais de 2.000 proclamações distintas para outros locais. | [Todos os locais de voz personalizada](language-support.md#customization) |
| **Áudio longo + transcrição (beta)** | Uma coleção (.zip) de ficheiros áudio longos e não segmentados (mais de 20 segundos), emparelhado com uma transcrição (.txt) que contém todas as palavras faladas. | Tem ficheiros áudio e transcrições correspondentes, mas não são segmentados em expressões. | Segmentação (utilizando a transcrição do lote).<br>Transformação do formato áudio sempre que necessário. | Sem exigência difícil  | [Todos os locais de voz personalizada](language-support.md#customization) |
| **Apenas áudio (beta)** | Uma coleção (.zip) de ficheiros áudio sem transcrição. | Só tem ficheiros áudio disponíveis, sem transcrições. | Segmentação + geração de transcrição (utilizando transcrição de lote).<br>Transformação do formato áudio sempre que necessário.| Sem exigência difícil | [Todos os locais de voz personalizada](language-support.md#customization) |

Os ficheiros devem ser agrupados por tipo num conjunto de dados e carregados como ficheiro zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!NOTE]
> O número máximo de conjuntos de dados autorizados a serem importados por subscrição é de 10 .ficheiros zip para utilizadores de subscrição gratuita (F0) e 500 para utilizadores de subscrição padrão (S0).

## <a name="individual-utterances--matching-transcript"></a>Expressões individuais + transcrição correspondente

Pode preparar gravações de expressões individuais e a transcrição correspondente de duas maneiras. Ou escreve um guião e o tem lido por um talento de voz ou usa áudio disponível publicamente e transcreve-o para texto. Se fizer este último, edite as disfluências dos ficheiros áudio, tais como "um" e outros sons de enchimento, gaguez, palavras murmuradas ou pronúncias erradas.

Para produzir uma boa fonte de voz, crie as gravações numa sala tranquila com um microfone de alta qualidade. Volume consistente, taxa de fala, discurso e maneirismos expressivos da fala são essenciais.

> [!TIP]
> Para criar uma voz para uso de produção, recomendamos que use um estúdio de gravação profissional e talento de voz. Para mais informações, consulte [Como gravar amostras de voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ficheiros áudio

Cada ficheiro áudio deve conter uma única expressão (uma única frase ou uma única volta de um sistema de diálogo), com menos de 15 segundos de duração. Todos os ficheiros devem estar na mesma língua falada. Não são apoiadas vozes de texto para discurso personalizadas multilingues, com exceção da bilingual chinês-inglês. Cada ficheiro áudio deve ter um nome de ficheiro numérico único com a extensão do nome de ficheiro .wav.

Siga estas orientações ao preparar áudio.

| Propriedade | Valor |
| -------- | ----- |
| Formato de ficheiro | RIFF (.wav), agrupado em um ficheiro .zip |
| Taxa de amostragem | Pelo menos 16.000 Hz |
| Formato de amostra | PCM, 16-bit |
| Nome de ficheiro | Numérico, com extensão .wav. Não são permitidos nomes de ficheiros duplicados. |
| Comprimento de áudio | Menos de 15 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo do arquivo | 2048 MB |

> [!NOTE]
> .os ficheiros de awav com uma taxa de amostragem inferior a 16.000 Hz serão rejeitados. Se um ficheiro .zip contiver ficheiros .wav com taxas de amostra diferentes, apenas serão importados aqueles iguais ou superiores a 16.000 Hz. O portal atualmente importa .zip arquivos até 200 MB. No entanto, vários arquivos podem ser carregados.

### <a name="transcripts"></a>Transcrições

O ficheiro de transcrição é um ficheiro de texto simples. Use estas diretrizes para preparar as suas transcrições.

| Propriedade | Valor |
| -------- | ----- |
| Formato de ficheiro | Texto simples (.txt) |
| Formato de codificação | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE. Para as codificações zh-CN, ANSI/ASCII e UTF-8 não são suportadas. |
| N.º de expressões por linha | **Um** - Cada linha do ficheiro de transcrição deve conter o nome de um dos ficheiros áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t). |
| Tamanho máximo do ficheiro | 2048 MB |

Abaixo está um exemplo de como as transcrições são organizadas por expressão num ficheiro .txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
É importante que as transcrições sejam transcrições 100% precisas do áudio correspondente. Erros nas transcrições introduzirão perda de qualidade durante o treino.

> [!TIP]
> Ao construir vozes de texto para discurso, selecione expressões (ou escreva scripts) que têm em conta tanto a cobertura fonética como a eficiência. Está com problemas para obter os resultados que quer? [Contacte a](mailto:speechsupport@microsoft.com) equipa Custom Voice para saber mais sobre como nos consultar.

## <a name="long-audio--transcript-beta"></a>Áudio longo + transcrição (beta)

Em alguns casos, pode não ter áudio segmentado disponível. Fornecemos um serviço (beta) através do portal de voz personalizado para ajudá-lo a segmentar longos ficheiros de áudio e criar transcrições. Tenha em mente que este serviço será cobrado para o seu uso de subscrição de discurso-a-texto.

> [!NOTE]
> O serviço de segmentação de áudio longo irá aproveitar a funcionalidade de transcrição do lote de voz a texto, que apenas suporta utilizadores de subscrição padrão (S0). Durante o processamento da segmentação, os seus ficheiros áudio e as transcrições também serão enviados para o serviço de Discurso Personalizado para aperfeiçoar o modelo de reconhecimento para que a precisão possa ser melhorada para os seus dados. Nenhum dado será retido durante este processo. Após a segmentação, apenas as expressões segmentadas e as suas transcrições de mapeamento serão armazenadas para o seu download e treino.

### <a name="audio-files"></a>Ficheiros áudio

Siga estas orientações ao preparar áudio para segmentação.

| Propriedade | Valor |
| -------- | ----- |
| Formato de ficheiro | RIFF (.wav) com uma taxa de amostragem de pelo menos 16 khz-16 bits em PCM ou .mp3 com uma taxa bit de pelo menos 256 KBps, agrupado em um ficheiro .zip |
| Nome de ficheiro | Personagens ASCII e Unicode suportados. Não são permitidos nomes duplicados. |
| Comprimento de áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo do arquivo | 2048 MB |

Todos os ficheiros áudio devem ser agrupados num ficheiro zip. Não faz mal colocar ficheiros .wav e ficheiros .mp3 num fecho de áudio. Por exemplo, pode carregar um ficheiro zip contendo um ficheiro áudio chamado 'kingstory.wav', 45 segundos de comprimento, e outro áudio chamado 'queenstory.mp3', de 200 segundos de comprimento. Todos os ficheiros .mp3 serão transformados no formato .wav após o processamento.

### <a name="transcripts"></a>Transcrições

As transcrições devem estar preparadas de acordo com as especificações enumeradas nesta tabela. Cada ficheiro áudio deve ser combinado com uma transcrição.

| Propriedade | Valor |
| -------- | ----- |
| Formato de ficheiro | Texto simples (.txt), agrupado em um .zip |
| Nome de ficheiro | Use o mesmo nome que o ficheiro áudio correspondente |
| Formato de codificação | UTF-8-BOM apenas |
| N.º de expressões por linha | Sem limite |
| Tamanho máximo do ficheiro | 2048 MB |

Todos os ficheiros de transcrições deste tipo de dados devem ser agrupados num ficheiro zip. Por exemplo, você carregou um ficheiro zip contendo um ficheiro áudio chamado 'kingstory.wav', 45 segundos de duração, e outro chamado 'queenstory.mp3', 200 segundos de duração. Terá sabotado outro ficheiro zip contendo duas transcrições, uma chamada "kingstory.txt", a outra 'queenstory.txt'. Dentro de cada ficheiro de texto simples, fornecerá a transcrição completa correta para o áudio correspondente.

Depois do seu conjunto de dados ser carregado com sucesso, vamos ajudá-lo a segmentar o ficheiro áudio em expressões com base na transcrição fornecida. Pode verificar as expressões segmentadas e as transcrições correspondentes, baixando o conjunto de dados. IDs exclusivos serão atribuídos automaticamente às expressões segmentadas. É importante que certifique-se de que as transcrições que fornece são 100% precisas. Erros nas transcrições podem reduzir a precisão durante a segmentação de áudio e introduzir ainda mais perda de qualidade na fase de treino que vem mais tarde.

## <a name="audio-only-beta"></a>Apenas áudio (beta)

Se não tiver transcrições para as suas gravações áudio, utilize a **opção Áudio apenas** para fazer o upload dos seus dados. O nosso sistema pode ajudá-lo a segmentar e transcrever os seus ficheiros áudio. Tenha em mente que este serviço contará para o seu uso de subscrição de discurso-a-texto.

Siga estas orientações ao preparar áudio.

> [!NOTE]
> O serviço de segmentação de áudio longo irá aproveitar a funcionalidade de transcrição do lote de voz a texto, que apenas suporta utilizadores de subscrição padrão (S0).

| Propriedade | Valor |
| -------- | ----- |
| Formato de ficheiro | RIFF (.wav) com uma taxa de amostragem de pelo menos 16 khz-16 bits em PCM ou .mp3 com uma taxa bit de pelo menos 256 KBps, agrupado em um ficheiro .zip |
| Nome de ficheiro | Personagens ASCII e Unicode suportados. Não é permitido um nome duplicado. |
| Comprimento de áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo do arquivo | 2048 MB |

Todos os ficheiros áudio devem ser agrupados num ficheiro zip. Assim que o seu conjunto de dados for carregado com sucesso, vamos ajudá-lo a segmentar o ficheiro áudio em expressões com base no nosso serviço de transcrição de lote de fala. IDs exclusivos serão atribuídos automaticamente às expressões segmentadas. As transcrições correspondentes serão geradas através do reconhecimento da fala. Todos os ficheiros .mp3 serão transformados no formato .wav após o processamento. Pode verificar as expressões segmentadas e as transcrições correspondentes, baixando o conjunto de dados.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave as amostras de voz](record-custom-voice-samples.md)
