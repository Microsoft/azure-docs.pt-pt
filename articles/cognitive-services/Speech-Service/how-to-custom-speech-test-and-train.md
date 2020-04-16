---
title: Preparar dados para discurso personalizado - Serviço de discurso
titleSuffix: Azure Cognitive Services
description: Ao testar a precisão do reconhecimento de voz da Microsoft ou treinar os seus modelos personalizados, necessitará de dados de áudio e texto. Nesta página, cobrimos os tipos de dados, como usá-los e geri-los.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 78857709447f99895c36f23d8760f44f8468ba7c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402129"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para Voz Personalizada

Ao testar a precisão do reconhecimento de voz da Microsoft ou treinar os seus modelos personalizados, necessitará de dados de áudio e texto. Nesta página, cobrimos os tipos de dados, como usá-los e geri-los.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceites, quando cada tipo de dados deve ser utilizado, e a quantidade recomendada. Nem todos os tipos de dados são necessários para criar um modelo. Os requisitos de dados variarão dependendo se está a criar um teste ou a treinar um modelo.

| Tipo de dados | Utilizado para testes | Quantidade recomendada | Usado para treinar | Quantidade recomendada |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Sim<br>Utilizado para inspeção visual | 5+ ficheiros áudio | Não | N/a |
| [Audio + Transcrições com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Usado para avaliar a precisão | 0,5-5 horas de áudio | Sim | 1-1.000 horas de áudio |
| [Texto relacionado](#related-text-data-for-training) | Não | N/a | Sim | 1-200 MB de texto relacionado |

Os ficheiros devem ser agrupados por tipo num conjunto de dados e carregados como um ficheiro .zip. Cada conjunto de dados só pode conter um único tipo de dados.

> [!TIP]
> Para começar rapidamente, considere usar dados da amostra. Consulte este repositório GitHub para <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">amostrar dados <span class="docon docon-navigate-external x-hidden-focus"></span> da Fala Personalizada</a>

## <a name="upload-data"></a>Carregar dados

Para fazer upload dos seus dados, navegue para o <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>de Discurso Personalizado </a>. A partir do portal, clique em **carregar dados** para lançar o assistente e criar o seu primeiro conjunto de dados. Será-lhe pedido que selecione um tipo de dados de fala para o seu conjunto de dados, antes de permitir o upload dos seus dados.

![Selecione áudio do Portal da Fala](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que envia deve satisfazer os requisitos para o tipo de dados que escolher. Os seus dados devem ser corretamente formatados antes de serem carregados. Os dados devidamente formatados garantem que serão processados com precisão pelo serviço de Discurso Personalizado. Os requisitos estão listados nas seguintes secções.

Depois de o seu conjunto de dados ser carregado, tem algumas opções:

* Pode navegar para o separador **Testing** e inspecionar visualmente apenas áudio ou áudio + dados de transcrição com rótulo humano.
* Pode navegar para o separador **Training** e utilizar dados de transcrição áudio + humano ou dados de texto relacionados para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados áudio para testes

Os dados áudio são ideais para testar a precisão do modelo de voz e texto da Microsoft ou de um modelo personalizado. Tenha em mente que os dados áudio são utilizados para inspecionar a precisão da fala no que diz respeito ao desempenho de um modelo específico. Se procura quantificar a precisão de um modelo, utilize [dados de transcrição áudio + com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining).

Utilize esta tabela para garantir que os seus ficheiros áudio estão formatados corretamente para utilização com a Fala Personalizada:

| Propriedade                 | Valor                 |
|--------------------------|-----------------------|
| Formato de ficheiro              | RIFF (WAV)            |
| Taxa de amostragem              | 8.000 Hz ou 16.000 Hz |
| Canais                 | 1 (mono)              |
| Comprimento máximo por áudio | Duas horas               |
| Formato de amostra            | PCM, 16-bit           |
| Formato de arquivo           | .zip                  |
| Tamanho máximo do arquivo     | 2GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Ao carregar os dados de treino e teste, o tamanho do ficheiro .zip não pode exceder 2 GB. Se necessitar de mais dados para o treino, divida-os em vários ficheiros .zip e carregue-os separadamente. Mais tarde, pode optar por treinar a partir de *vários* conjuntos de dados. No entanto, só pode testar a partir de um *único* conjunto de dados.

Utilize <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">o <span class="docon docon-navigate-external x-hidden-focus"></span> SoX</a> para verificar as propriedades áudio ou converter áudio existente para os formatos apropriados. Abaixo estão alguns exemplos de como cada uma destas atividades pode ser feita através da linha de comando SoX:

| Atividade | Descrição | Comando SoX |
|----------|-------------|-------------|
| Ver formato áudio | Use este comando para verificar<br>o formato de ficheiro áudio. | `sox --i <filename>` |
| Converter formato de áudio | Use este comando para converter<br>o ficheiro áudio para um único canal, 16-bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Áudio + dados de transcrição com rótulo humano para teste/treino

Para medir a precisão da precisão do discurso ao texto da Microsoft ao processar os seus ficheiros áudio, deve fornecer transcrições com rótulo humano (palavra a palavra) para comparação. Embora a transcrição com rótulo humano seja muitas vezes morosa, é necessário avaliar a precisão e treinar o modelo para os seus casos de utilização. Tenha em mente que as melhorias no reconhecimento só serão tão boas quanto os dados fornecidos. Por isso, é importante que apenas transcrições de alta qualidade sejam carregadas.

| Propriedade                 | Valor                               |
|--------------------------|-------------------------------------|
| Formato de ficheiro              | RIFF (WAV)                          |
| Taxa de amostragem              | 8.000 Hz ou 16.000 Hz               |
| Canais                 | 1 (mono)                            |
| Comprimento máximo por áudio | 2 horas (testes) / 60 s (treino) |
| Formato de amostra            | PCM, 16-bit                         |
| Formato de arquivo           | .zip                                |
| Tamanho máximo do fecho         | 2GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Ao carregar os dados de treino e teste, o tamanho do ficheiro .zip não pode exceder 2 GB. Só pode testar a partir de um *único* conjunto de dados, certifique-se de mantê-lo dentro do tamanho de ficheiro apropriado. Além disso, cada ficheiro de treino não pode exceder 60 segundos, caso contrário, irá errar.

Para abordar questões como a eliminação de palavras ou a substituição, é necessária uma quantidade significativa de dados para melhorar o reconhecimento. Geralmente, é recomendado fornecer transcrições palavra-a-palavra para cerca de 10 a 1.000 horas de áudio. As transcrições para todos os ficheiros WAV devem estar contidas num único ficheiro de texto simples. Cada linha do ficheiro de transcrição deve ter o nome de um dos ficheiros de áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t).

  Por exemplo:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> A transcrição deve ser codificada como UTF-8 byte order mark (BOM).

As transcrições são normalizadas para texto, de modo a que o sistema as possa processar. No entanto, existem algumas normalizações importantes que devem ser feitas antes de enviar os dados para o Speech Studio. Para que a linguagem adequada utilize quando preparar as suas transcrições, consulte [Como criar uma transcrição com rótulo humano](how-to-custom-speech-human-labeled-transcriptions.md)

Depois de ter recolhido os seus ficheiros áudio e transcrições correspondentes, embalhe-os como um único ficheiro .zip antes de enviar para o <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>De Discurso Personalizado </a>. Abaixo está um conjunto de dados de exemplo com três ficheiros áudio e um ficheiro de transcrição com rótulo humano:

> [!div class="mx-imgBorder"]
> ![Selecione áudio do Portal da Fala](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dados de texto relacionados para a formação

Os nomes ou funcionalidades do produto que são únicos, devem incluir dados de texto relacionados para a formação. O texto relacionado ajuda a garantir o reconhecimento correto. Podem ser fornecidos dois tipos de dados de texto relacionados para melhorar o reconhecimento:

| Tipo de dados | Como estes dados melhoram o reconhecimento |
|-----------|------------------------------------|
| Frases (pronunciamentos) | Melhorar a precisão ao reconhecer os nomes dos produtos ou o vocabulário específico da indústria no contexto de uma frase. |
| Pronúncias | Melhorar a pronúncia de termos, siglas ou outras palavras incomuns com pronúncias indefinidas. |

As frases podem ser fornecidas como um único ficheiro de texto ou vários ficheiros de texto. Para melhorar a precisão, utilize dados de texto mais próximos das pronunciações faladas esperadas. As pronúncias devem ser fornecidas como um único ficheiro de texto. Tudo pode ser embalado como um único ficheiro zip e enviado para o <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>de Discurso Personalizado </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Diretrizes para criar um ficheiro de frases

Para criar um modelo personalizado usando frases, terá de fornecer uma lista de declarações de amostra. As expressões _não_ precisam de ser completas ou gramaticalmente corretas, mas devem refletir com precisão a entrada falada que espera na produção. Se quiser que determinados termos tenham um peso acrescido, adicione várias frases que incluem estes termos específicos.

Como orientação geral, a adaptação do modelo é mais eficaz quando o texto de formação está o mais próximo possível do texto real esperado na produção. O jargão específico do domínio e as frases que está a apontar para melhorar, devem ser incluídas no texto de treino. Quando possível, tente ter uma frase ou palavra-chave controlada numa linha separada. Para palavras-chave e frases que são importantes para si (por exemplo, nomes de produtos), pode copiá-las algumas vezes. Mas lembre-se, não copie muito- pode afetar a taxa de reconhecimento geral.

Utilize esta tabela para garantir que o seu ficheiro de dados relacionado para as expressões é formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM |
| N.º de expressões por linha | 1 |
| Tamanho máximo do ficheiro | 200 MB |

Além disso, irá prestar contas das seguintes restrições:

* Evite repetir caracteres mais de quatro vezes. Por exemplo: "aaaa" ou "uuuuu".
* Não utilize caracteres especiais ou caracteres `U+00A1`UTF-8 acima .
* Os URIs serão rejeitados.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um ficheiro de pronúncia

Se houver termos incomuns sem pronúncias padrão que os seus utilizadores irão encontrar ou usar, pode fornecer um ficheiro de pronúncia personalizado para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é aconselhável utilizar ficheiros de pronúncia personalizados para alterar a pronúncia das palavras comuns.

Isto inclui exemplos de uma expressão falada, e uma pronúncia personalizada para cada um:

| Formulário reconhecido/exibido | Forma falada |
|--------------|--------------------------|
| 3CPO | três c p o |
| CNTK | c n t k |
| IEEE | i triplo e |

O formulário falado é a sequência fonética escrita. Pode ser composto por letras, palavras, silabáveis, ou uma combinação de todos os três.

A pronúncia personalizada está`en-US`disponível`de-DE`em inglês ( ) e alemão . Esta tabela mostra caracteres suportados por linguagem:

| Idioma | Região | Personagens |
|----------|--------|------------|
| Inglês | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Alemão | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Utilize a tabela seguinte para garantir que o seu ficheiro de dados relacionado para pronúncias está corretamente formatado. Os ficheiros de pronúncia são pequenos e devem ter apenas alguns quilobytes de tamanho.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM (ANSI também é apoiado para inglês) |
| # de pronúncias por linha | 1 |
| Tamanho máximo do ficheiro | 1 MB (1 KB para o nível livre) |

## <a name="next-steps"></a>Passos seguintes

* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implante o seu modelo](how-to-custom-speech-deploy-model.md)
