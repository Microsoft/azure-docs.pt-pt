---
title: Como preparar dados para voz personalizada - serviço de fala
titleSuffix: Azure Cognitive Services
description: Crie uma voz personalizada para a sua marca com o serviço Speech. Fornece gravações de estúdio e scripts associados, o serviço gera um modelo de voz único sintonizado com a voz gravada. Utilize esta voz para sintetizar a fala nos seus produtos, ferramentas e aplicações.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 22db0006e6abb38439a2db46584559dcd9ce3f3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722369"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparar dados para criar uma voz personalizada

Quando estiver pronto para criar uma voz personalizada de texto-a-voz para a sua aplicação, o primeiro passo é recolher gravações áudio e scripts associados para começar a treinar o modelo de voz. O serviço Discurso utiliza estes dados para criar uma voz única sintonizada para corresponder à voz nas gravações. Depois de treinar a voz, pode começar a sintetizar o discurso nas suas aplicações.

Antes de treinar o seu próprio modelo de voz de texto para voz, precisará de gravações áudio e transcrições de texto associadas. Nesta página, vamos rever os tipos de dados, como são usados e como gerir cada um deles.

> [!NOTE]
> Se quiser treinar uma voz neural, deve especificar um perfil de talento de voz com o ficheiro de consentimento áudio fornecido pelo talento de voz, reconhecendo usar os seus dados de fala para treinar um modelo de voz personalizado. Ao preparar o seu script de gravação, certifique-se de que inclui a frase abaixo. 

> "Eu [declaro o seu primeiro e último nome] estou ciente de que as gravações da minha voz serão usadas [pelo nome da empresa] para criar e usar uma versão sintética da minha voz."
Esta frase será usada para verificar se os dados de formação são feitos pela mesma pessoa que faz o consentimento. Leia mais sobre a [verificação](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) de talentos de voz aqui.

> A Voz Neural Personalizada está disponível com acesso limitado. Certifique-se de compreender os [requisitos responsáveis da IA](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) e [aplicar o acesso aqui.](https://aka.ms/customneural) 

## <a name="data-types"></a>Tipos de dados

Um conjunto de dados de formação de voz inclui gravações áudio e um ficheiro de texto com as transcrições associadas. Cada ficheiro áudio deve conter uma única expressão (uma única frase ou uma curva para um sistema de diálogo) e ter menos de 15 segundos de duração.

Em alguns casos, pode não ter o conjunto de dados certo pronto e irá querer testar o treino de voz personalizado com ficheiros áudio disponíveis, curtos ou longos, com ou sem transcrições. Fornecemos ferramentas (beta) para ajudá-lo a segmentar o seu áudio em expressões e a preparar transcrições utilizando a API de [Transcrição](batch-transcription.md)de Lote .

Esta tabela lista os tipos de dados e como cada um é usado para criar um modelo personalizado de voz texto-a-voz.

| Tipo de dados | Descrição | Quando utilizar | Processamento adicional necessário | 
| --------- | ----------- | ----------- | --------------------------- |
| **Declarações individuais + transcrição correspondente** | Uma coleção (.zip) de ficheiros áudio (.wav) como expressões individuais. Cada ficheiro áudio deve ter 15 segundos ou menos de comprimento, emparelhado com uma transcrição formatada (.txt). | Gravações profissionais com transcrições correspondentes | Pronto para o treino. |
| **Longa transcrição áudio + (beta)** | Uma coleção (.zip) de ficheiros áudio longos e não-egmentados (mais de 20 segundos), emparelhada com uma transcrição (.txt) que contém todas as palavras faladas. | Tem ficheiros áudio e transcrições correspondentes, mas não são segmentados em expressões. | Segmentação (utilizando a transcrição do lote).<br>Transformação do formato áudio sempre que necessário. | 
| **Apenas áudio (beta)** | Uma coleção (.zip) de ficheiros áudio sem transcrição. | Só tem ficheiros áudio disponíveis, sem transcrições. | Segmentação + geração de transcrição (utilizando a transcrição do lote).<br>Transformação do formato áudio sempre que necessário.| 

Os ficheiros devem ser agrupados por tipo num conjunto de dados e carregados como um ficheiro zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!NOTE]
> O número máximo de conjuntos de dados autorizados a serem importados por subscrição é de 10 ficheiros zip para utilizadores de subscrição gratuita (F0) e 500 para utilizadores de subscrição padrão (S0).

## <a name="individual-utterances--matching-transcript"></a>Declarações individuais + transcrição correspondente

Pode preparar gravações de declarações individuais e a transcrição correspondente de duas maneiras. Ou escreve um guião e fazê-lo ler por um talento de voz ou usar áudio disponível ao público e transcrevê-lo para texto. Se fizer este último, edite disfluências dos ficheiros áudio, tais como "um" e outros sons de enchimento, gaguez, palavras murmurantes ou pronúncias erradas.

Para produzir um bom modelo de voz, crie as gravações numa sala tranquila com um microfone de alta qualidade. Volume consistente, taxa de fala, tom de fala e maneirismos expressivos da fala são essenciais.

> [!TIP]
> Para criar uma voz para uso de produção, recomendamos que use um estúdio de gravação profissional e talento de voz. Para obter mais informações, consulte [Como gravar amostras de voz para uma voz personalizada](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ficheiros áudio

Cada ficheiro áudio deve conter uma única expressão (uma única frase ou uma única volta de um sistema de diálogo), com menos de 15 segundos de duração. Todos os ficheiros devem estar na mesma língua falada. Não são suportadas vozes de texto-a-fala personalizadas multi-linguárias, com exceção da Chinese-English bi-lingual. Cada ficheiro áudio deve ter um nome numérico único com a extensão do nome de ficheiro .wav.

Siga estas diretrizes ao preparar o áudio.

| Propriedade | Valor |
| -------- | ----- |
| Formato do ficheiro | RIFF (.wav), agrupado num ficheiro .zip |
| Taxa de amostragem | Pelo menos 16.000 Hz |
| Sample format (Formato de exemplo) | PCM, 16-bit |
| Nome de ficheiro | Numérico, com .wav extensão. Não são permitidos nomes de ficheiros duplicados. |
| Comprimento áudio | Menos de 15 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo do arquivo | 2048 MB |

> [!NOTE]
> .wav ficheiros com uma taxa de amostragem inferior a 16.000 Hz serão rejeitados. Se um ficheiro .zip contiver ficheiros .wav com taxas de amostra diferentes, apenas aqueles iguais ou superiores a 16.000 Hz serão importados. O portal importa atualmente .zip arquivos até 200 MB. No entanto, vários arquivos podem ser carregados.

### <a name="transcripts"></a>Transcrições

O ficheiro de transcrição é um ficheiro de texto simples. Use estas diretrizes para preparar as suas transcrições.

| Propriedade | Valor |
| -------- | ----- |
| Formato do ficheiro | Texto simples (.txt) |
| Formato codificante | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE. Para as codificações zh-CN, ANSI/ASCII e UTF-8 não são suportadas. |
| N.º de expressões por linha | **Um** - Cada linha do ficheiro de transcrição deve conter o nome de um dos ficheiros áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t). |
| Tamanho máximo do ficheiro | 2048 MB |

Abaixo está um exemplo de como as transcrições são organizadas por expressão num .txt ficheiro:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
É importante que as transcrições sejam 100% precisas transcrições do áudio correspondente. Erros nas transcrições introduzirão perda de qualidade durante o treino.

## <a name="long-audio--transcript-beta"></a>Longa transcrição áudio + (beta)

Em alguns casos, pode não ter áudio segmentado disponível. Fornecemos um serviço (beta) através do portal de voz personalizado para ajudá-lo a segmentar longos ficheiros de áudio e criar transcrições. Tenha em mente que este serviço será cobrado para o uso da subscrição de voz a texto.

> [!NOTE]
> O serviço de segmentação de áudio longo irá aproveitar a funcionalidade de transcrição de lote de discurso a texto, que apenas suporta utilizadores de subscrição padrão (S0). Durante o processamento da segmentação, os seus ficheiros áudio e as transcrições também serão enviados para o serviço de Discurso Personalizado para refinar o modelo de reconhecimento para que a precisão possa ser melhorada para os seus dados. Nenhum dado será retido durante este processo. Após a segmentação ser feita, apenas as expressões segmentadas e as suas transcrições de mapeamento serão armazenadas para o seu download e treino.

### <a name="audio-files"></a>Ficheiros áudio

Siga estas orientações ao preparar áudio para segmentação.

| Propriedade | Valor |
| -------- | ----- |
| Formato do ficheiro | RIFF (.wav) com uma taxa de amostragem de pelo menos 16 khz-16-bit em PCM ou .mp3 com uma taxa de pelo menos 256 KBps, agrupadas num ficheiro .zip |
| Nome de ficheiro | Caracteres ASCII e Unicode suportados. Não são permitidos nomes duplicados. |
| Comprimento áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo do arquivo | 2048 MB |

Todos os ficheiros áudio devem ser agrupados num ficheiro zip. Não faz mal colocar ficheiros .wav e .mp3 ficheiros num único fecho de áudio. Por exemplo, pode carregar um ficheiro zip contendo um ficheiro áudio chamado 'kingstory.wav', 45 segundos de comprimento e outro áudio chamado 'queenstory.mp3', de 200 segundos. Todos os ficheiros .mp3 serão transformados no formato .wav após o processamento.

### <a name="transcripts"></a>Transcrições

As transcrições devem estar preparadas de acordo com as especificações enumeradas nesta tabela. Cada ficheiro áudio deve ser combinado com uma transcrição.

| Propriedade | Valor |
| -------- | ----- |
| Formato do ficheiro | Texto simples (.txt), agrupado numa .zip |
| Nome de ficheiro | Use o mesmo nome que o ficheiro áudio correspondente |
| Formato codificante | UTF-8-BOM apenas |
| N.º de expressões por linha | Sem limite |
| Tamanho máximo do ficheiro | 2048 MB |

Todos os ficheiros de transcrições deste tipo de dados devem ser agrupados num ficheiro zip. Por exemplo, carregou um ficheiro zip contendo um ficheiro áudio chamado "kingstory.wav", com 45 segundos de duração, e outro chamado "queenstory.mp3", com 200 segundos de duração. Terá de carregar outro ficheiro zip contendo duas transcrições, uma chamada "kingstory.txt", a outra "queenstory.txt". Dentro de cada ficheiro de texto simples, irá fornecer a transcrição completa e correta para o áudio correspondente.

Depois do seu conjunto de dados ser carregado com sucesso, iremos ajudá-lo a segmentar o ficheiro áudio em expressões com base na transcrição fornecida. Pode verificar as expressões segmentadas e as transcrições correspondentes descarregando o conjunto de dados. Os IDs exclusivos serão atribuídos automaticamente às expressões segmentadas. É importante que certifique-se de que as transcrições que fornece são 100% precisas. Os erros nas transcrições podem reduzir a precisão durante a segmentação áudio e introduzir ainda mais a perda de qualidade na fase de treino que vem depois.

## <a name="audio-only-beta"></a>Apenas áudio (beta)

Se não tiver transcrições para as suas gravações áudio, utilize a única opção **Áudio** para fazer o upload dos seus dados. O nosso sistema pode ajudá-lo a segmentar e a transcrever os seus ficheiros áudio. Tenha em mente que este serviço contará para o uso da subscrição de voz a texto.

Siga estas diretrizes ao preparar o áudio.

> [!NOTE]
> O serviço de segmentação de áudio longo irá aproveitar a funcionalidade de transcrição de lote de discurso a texto, que apenas suporta utilizadores de subscrição padrão (S0).

| Propriedade | Valor |
| -------- | ----- |
| Formato do ficheiro | RIFF (.wav) com uma taxa de amostragem de pelo menos 16 khz-16-bit em PCM ou .mp3 com uma taxa de pelo menos 256 KBps, agrupadas num ficheiro .zip |
| Nome de ficheiro | Caracteres ASCII e Unicode suportados. Não é permitido um nome duplicado. |
| Comprimento áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo do arquivo | 2048 MB |

Todos os ficheiros áudio devem ser agrupados num ficheiro zip. Assim que o seu conjunto de dados for carregado com sucesso, iremos ajudá-lo a segmentar o ficheiro áudio em expressões baseadas no nosso serviço de transcrição de lote de fala. Os IDs exclusivos serão atribuídos automaticamente às expressões segmentadas. As transcrições correspondentes serão geradas através do reconhecimento da fala. Todos os ficheiros .mp3 serão transformados no formato .wav após o processamento. Pode verificar as expressões segmentadas e as transcrições correspondentes descarregando o conjunto de dados.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave as suas amostras de voz](record-custom-voice-samples.md)