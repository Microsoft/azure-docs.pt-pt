---
title: Preparar os dados de teste para conversão de voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Se estiver a testar para ver o reconhecimento de voz de Microsoft grau de precisão é ou treinamento seus próprios modelos, terá de dados (na forma de áudio e/ou de texto). Nesta página, vamos abordar os tipos de dados, como elas são usadas e como gerenciá-los.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: de2f1009c574d9768330d4e6a38a219ba1f81daa
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237957"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para a conversão de voz personalizada

Se estiver a testar para ver o reconhecimento de voz de Microsoft grau de precisão é ou treinamento seus próprios modelos, terá de dados na forma de texto e áudio. Nesta página, vamos abordar os tipos de dados, como elas são usadas e como gerenciá-los.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceite, quando cada tipo de dados deve ser usado e a quantidade recomendada. Nem todo tipo de dados é necessário para criar um modelo. Requisitos de dados irão variar consoante a criação de um teste ou preparar um modelo.

| Tipo de dados | Utilizado de teste | Quantidade | Usada para treinamento | Quantidade |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Sim<br>Utilizado para inspeção visual | 5 + arquivos de áudio | Não | N/a |
| [Transcrições áudio + com etiqueta humanos](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Utilizado para avaliar a precisão | 0,5 - 5 horas de áudio | Sim | 1 - 1000 horas de áudio |
| [Introduza texto relacionado com](##related-text-data-for-training) | Não | N/a | Sim | 1-200 MB de texto relacionado |

Os ficheiros devem ser agrupados por tipo para um conjunto de dados e carregados como um ficheiro zip. Cada conjunto de dados só pode conter um único tipo de dados.

## <a name="upload-data"></a>Carregar dados

Quando estiver pronto para carregar os dados, clique em **carregar dados** para iniciar o assistente e criar seu primeiro conjunto de dados. Será solicitado a selecionar um tipo de dados de voz para o conjunto de dados, antes de permitir-lhe carregar os dados.

![Selecione áudio a partir do Portal da voz](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que carrega têm de cumprir os requisitos para o tipo de dados que escolher. É importante Formatar corretamente os seus dados antes de ser carregado. Isto garante que os dados serão processados com precisão o serviço de voz personalizada. Requisitos estão listados nas secções seguintes.

Depois do conjunto de dados é carregado, tem algumas opções:

* Pode navegar para o **teste** separador e inspecionar visualmente apenas áudio ou dados de transcrição de áudio + rotulado como humanos.
* Pode navegar para o **treinamento** separador e nos dados de transcrição de áudio + humano ou dados de texto relacionados para preparar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados de áudio para fins de teste

Dados de áudio são ideais para testar a precisão do modelo de voz em texto de linha de base da Microsoft ou um modelo personalizado. Tenha em mente, dados de áudio são utilizados para inspecionar a precisão de conversão de voz com respeito a desempenho de um modelo específico. Se estiver à procura de quantificar a precisão de um modelo, utilize [dados de transcrição de áudio + com etiqueta humanos](#audio--human-labeled-transcript-data-for-testingtraining).

Utilize esta tabela para garantir que seus arquivos de áudio são devidamente formatados para utilização com a conversão de voz personalizada:

| Propriedade | Value |
|----------|-------|
| Formato de ficheiro | RIFF (WAV) |
| Taxa da amostragem | 8.000 Hz ou Hz 16.000 |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | Duas horas |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2 GB |

Se o áudio não satisfazer essas propriedades ou pretende verificar se ele faz, sugerimos que baixar [sox](http://sox.sourceforge.net) para verificar ou converta o áudio. Seguem-se alguns exemplos de como cada uma destas atividades pode ser feita através da linha de comandos:

| Atividade | Descrição | Comando SOx |
|----------|-------------|-------------|
| Verifique o formato de áudio | Utilize este comando para verificar o formato de arquivo de áudio. | `sox --i <filename>` |
| Converter o formato de áudio | Utilize este comando para converter o arquivo de áudio para o canal único, de 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dados de transcrição de áudio + com etiqueta humanos para teste/treinamento

Para medir a precisão de precisão de conversão de voz em texto da Microsoft durante o processamento de seus arquivos de áudio, tem de fornecer com etiqueta humanos transcrições (palavra por palavra) para comparação. Transcrição de etiqueta humanos geralmente é demorada, mas é necessária para avaliar a precisão e para preparar o modelo para seus casos de utilização. Tenha em mente, os aprimoramentos em reconhecimento só pode ser tão boas quanto os dados fornecidos. Por esse motivo, é importante que apenas de alta qualidade transcrições são carregadas.  

| Propriedade | Value |
|----------|-------|
| Formato de ficheiro | RIFF (WAV) |
| Taxa da amostragem | 8.000 Hz ou Hz 16.000 |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 60 s |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo | .zip |
| Tamanho máximo de zip | 2 GB |

Para resolver problemas, como a eliminação do word ou substituição, uma quantidade significativa de dados é necessária para melhorar o reconhecimento. Em geral, é recomendado para fornecer a palavra por palavra transcrições de aproximadamente 10 para 1000 horas de áudio. As transcrições para todos os ficheiros WAV devem estar contidas num único ficheiro de texto simples. Cada linha do ficheiro de transcrição deve ter o nome de um dos ficheiros de áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t).

  Por exemplo:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> A transcrição deve ser codificada como UTF-8 byte order mark (BOM).

As transcrições são normalizadas para texto, de modo a que o sistema as possa processar. No entanto, existem algumas normalizações importantes que devem ser feitas pelo utilizador _antes_ de carregar os dados para o Serviço de Voz Personalizada. Para o idioma adequado utilizar quando preparar sua transcrições, consulte [como criar uma transcrição de etiqueta humanos](how-to-custom-speech-human-labeled-transcriptions.md)

Depois que reuniu seus arquivos de áudio e transcrições correspondentes, eles devem ser empacotados como um único ficheiro. zip antes de carregar para o portal de voz personalizada. Este é um conjunto de dados de exemplo com três arquivos de áudio e um arquivo de transcrição de etiqueta humanos:

![Selecione áudio a partir do Portal da voz](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Introduza texto relacionado com dados de treinamento

Se tiver nomes de produtos ou funcionalidades que são exclusivas e pretender certificar-se de que eles são reconhecidos corretamente, é importante incluir dados relacionados de texto para treinamento. Dois tipos de dados de texto relacionadas podem ser fornecidos para melhorar o reconhecimento de:

| Tipo de dados | Como esses dados melhora o reconhecimento |
|-----------|------------------------------------|
| Expressões com e/ou frases | Estes podem melhorar a precisão ao reconhecimento de nomes de produtos ou específicos da indústria vocabulário dentro do contexto de uma frase. |
| Pronunciations | Isso podem melhorar pronúncia de termos incomuns, acrônimos ou outras palavras, com pronunciations indefinidos. |

Expressões com podem ser fornecidos como um único ou vários ficheiros de texto. Quanto mais próximo o texto de dados são o que será dito, quanto maior a probabilidade de que a precisão é aprimorada. Pronunciations devem ser fornecidos como um único arquivo de texto. Tudo o que pode ser empacotado como um ficheiro zip único e carregado para o portal de voz personalizada.

### <a name="guidelines-to-create-an-utterances-file"></a>Diretrizes para criar um arquivo de expressões

Para criar um modelo personalizado usando o texto correspondente, terá de fornecer uma lista de expressões de exemplo. Estas expressões de com não precisam ser sentenças completas ou sentenças gramaticalmente corretas, mas elas têm refletem com exatidão a falado entrada que espera de produção. Se pretender que determinados termos ter aumentado o peso, pode adicionar vários frases ao seu ficheiro de dados relacionados que incluem estes termos específicos.

Utilize esta tabela para garantir que seu arquivo de dados relacionados para expressões com está formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM |
| N.º de expressões por linha | 1 |
| Tamanho máximo do ficheiro | 200 MB |

Além disso, poderá ser útil para levar em conta as seguintes restrições:

* Evite carateres repetidos mais de quatro vezes. Por exemplo: "aaaa" ou "uuuu".
* Não utilize carateres especiais ou UTF-8 carateres acima U+0B95 + 00A1.
* URIs será rejeitada.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um ficheiro de pronúncia

Se existirem termos incomuns sem pronunciations padrão que encontrar ou utilizar os seus utilizadores, pode fornecer um ficheiro de pronúncia personalizada para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é recomendado para utilizar esta funcionalidade para alterar a pronúncia de palavras comuns.

Isto inclui exemplos de uma expressão falada e uma pronúncia personalizada para cada:

| Formulário falado | Formulário reconhecido/apresentado |
|--------------|--------------------------|
| o três de c de p | 3CPO |  
| k de n de t c | CNTK |
| i triplicar e | IEEE |

O formulário falado é a sequência de fonética escrita. Ele pode ser composto por letra, palavras, sílabas ou uma combinação de todos os três.

Pronúncia personalizada está disponível em inglês (en-US) e alemão (Alemanha-DE). Esta tabela mostra carateres suportados pela linguagem:

| Idioma | Região | Carateres |
|----------|--------|------------|
| Inglês | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Utilize esta tabela para garantir que seu arquivo de dados relacionados para pronunciations está formatado corretamente. Pronúncia ficheiros são pequenos e não devem exceder os KBs alguns.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM (ANSI também é suportado para inglês) |
| n. º de pronunciations por linha | 1 |
| Tamanho máximo do ficheiro | 1 MB (1 KB para o escalão gratuito) |

## <a name="next-steps"></a>Passos Seguintes

* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)
