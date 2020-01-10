---
title: Preparar dados de teste para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Ao testar a precisão do reconhecimento de fala da Microsoft ou treinar seus modelos personalizados, você precisará de dados de áudio e texto. Nesta página, abordamos os tipos de dados, como usá-los e gerenciá-los.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 6100ac6a6b01a7d0eac74b0e83539bf4e671cb89
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660414"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para Fala Personalizada

Ao testar a precisão do reconhecimento de fala da Microsoft ou treinar seus modelos personalizados, você precisará de dados de áudio e texto. Nesta página, abordamos os tipos de dados, como usá-los e gerenciá-los.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceitos, quando cada tipo de dados deve ser usado e a quantidade recomendada. Nem todos os tipos de dados são necessários para criar um modelo. Os requisitos de dados irão variar dependendo se você estiver criando um teste ou treinando um modelo.

| Data type | Usado para teste | Quantidade recomendada | Usado para treinamento | Quantidade recomendada |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Sim<br>Usado para inspeção visual | mais de 5 arquivos de áudio | Não | N/a |
| [Áudio + transcrições com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Usado para avaliar a precisão | 0,5 a 5 horas de áudio | Sim | 1 a 1.000 horas de áudio |
| [Texto relacionado](#related-text-data-for-training) | Não | N/a | Sim | 1-200 MB de texto relacionado |

Os arquivos devem ser agrupados por tipo em um conjunto de um e carregados como um arquivo. zip. Cada conjunto de dados só pode conter um único tipo de dado.

> [!TIP]
> Para começar rapidamente, considere o uso de dados de exemplo. Consulte este repositório GitHub para obter <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">dados <span class="docon docon-navigate-external x-hidden-focus"></span> de fala personalizada de exemplo</a>

## <a name="upload-data"></a>Carregar dados

Para carregar seus dados, navegue até o <a href="https://speech.microsoft.com/customspeech" target="_blank">Portal <span class="docon docon-navigate-external x-hidden-focus"> </span>de fala personalizada </a>. No portal, clique em **carregar dados** para iniciar o assistente e criar o seu primeiro conjunto. Você será solicitado a selecionar um tipo de dados de fala para o seu conjunto, antes de permitir que você carregue seus dados.

![Selecionar áudio no portal de fala](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que você carrega deve atender aos requisitos para o tipo de dado que você escolher. Seus dados devem ser formatados corretamente antes de serem carregados. Os dados formatados corretamente garantem que serão processados com precisão pelo serviço de Fala Personalizada. Os requisitos são listados nas seções a seguir.

Depois que o conjunto de seus conjuntos de um for carregado, você terá algumas opções:

* Você pode navegar até a guia **teste** e inspecionar visualmente apenas áudio ou áudio + dados de transcrição com rótulo humano.
* Você pode navegar até a guia **treinamento** e usar áudio + dados de transcrição humana ou dados de texto relacionados para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados de áudio para teste

Os dados de áudio são ideais para testar a precisão do modelo de fala-para-texto de linha de base da Microsoft ou de um modelo personalizado. Tenha em mente que os dados de áudio são usados para inspecionar a precisão da fala em relação ao desempenho de um modelo específico. Se você estiver procurando quantificar a precisão de um modelo, use [áudio + dados de transcrição com rótulo humano](#audio--human-labeled-transcript-data-for-testingtraining).

Use esta tabela para garantir que os arquivos de áudio estejam formatados corretamente para uso com Fala Personalizada:

| Propriedade | Valor |
|----------|-------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostra | 8\.000 Hz ou 16.000 Hz |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 2 horas |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo morto | .zip |
| Tamanho máximo do arquivo morto | 2GB |

> [!TIP]
> Ao carregar dados de treinamento e teste, o tamanho do arquivo. zip não pode exceder 2 GB. Se você precisar de mais dados para treinamento, divida-os em vários arquivos. zip e carregue-os separadamente. Posteriormente, você pode optar por treinar a partir de *vários* conjuntos de valores. No entanto, você só pode testar a partir de um *único* conjunto de uma.

Use <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">o <span class="docon docon-navigate-external x-hidden-focus"></span> Sox</a> para verificar as propriedades de áudio ou converter o áudio existente nos formatos apropriados. Abaixo estão alguns exemplos de como cada uma dessas atividades pode ser feita por meio da linha de comando SoX:

| Atividade | Descrição | Comando SoX |
|----------|-------------|-------------|
| Verificar o formato de áudio | Use este comando para verificar<br>o formato do arquivo de áudio. | `sox --i <filename>` |
| Converter formato de áudio | Use este comando para converter<br>o arquivo de áudio para um único canal, 16 bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Áudio + dados de transcrição com rótulo humano para teste/treinamento

Para medir a precisão da precisão de fala para texto da Microsoft ao processar seus arquivos de áudio, você deve fornecer transcrições com rótulo humano (palavra por palavra) para comparação. Embora a transcrição com rótulo humano sempre seja demorada, é necessário avaliar a precisão e treinar o modelo para seus casos de uso. Tenha em mente que os aprimoramentos no reconhecimento serão tão bons quanto os dados fornecidos. Por esse motivo, é importante que apenas transcrições de alta qualidade sejam carregadas.

| Propriedade | Valor |
|----------|-------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostra | 8\.000 Hz ou 16.000 Hz |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 2 horas (teste)/60 s (treinamento) |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo morto | .zip |
| Tamanho máximo do zip | 2GB |

> [!NOTE]
> Ao carregar dados de treinamento e teste, o tamanho do arquivo. zip não pode exceder 2 GB. Você só pode testar a partir de um *único* conjunto de uma, certifique-se de mantê-lo dentro do tamanho apropriado do arquivo.

Para resolver problemas como exclusão ou substituição de palavras, uma quantidade significativa de dados é necessária para melhorar o reconhecimento. Em geral, é recomendável fornecer transcrições de palavra por palavra por aproximadamente 10 a 1.000 horas de áudio. As transcrições para todos os ficheiros WAV devem estar contidas num único ficheiro de texto simples. Cada linha do ficheiro de transcrição deve ter o nome de um dos ficheiros de áudio, seguido da transcrição correspondente. O nome de ficheiro e a transcrição devem estar separados por uma tabulação (\t).

  Por exemplo:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> A transcrição deve ser codificada como UTF-8 byte order mark (BOM).

As transcrições são normalizadas para texto, de modo a que o sistema as possa processar. No entanto, há algumas normalizações importantes que devem ser feitas antes de carregar os dados no Speech Studio. Para o idioma apropriado a ser usado ao preparar suas transcrições, consulte [como criar uma transcrição rotulada por pessoas](how-to-custom-speech-human-labeled-transcriptions.md)

Depois de coletar os arquivos de áudio e as transcrições correspondentes, empacote-os como um único arquivo. zip antes de carregar no <a href="https://speech.microsoft.com/customspeech" target="_blank">Portal <span class="docon docon-navigate-external x-hidden-focus"> </span>de fala personalizada </a>. Veja abaixo um exemplo de conjunto de exemplos com três arquivos de áudio e um arquivo de transcrição com rótulo humano:

> [!div class="mx-imgBorder"]
> ![selecionar áudio no portal de fala](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dados de texto relacionados para treinamento

Os nomes de produtos ou recursos que são exclusivos devem incluir dados de texto relacionados para treinamento. O texto relacionado ajuda a garantir o reconhecimento correto. Dois tipos de dados de texto relacionados podem ser fornecidos para melhorar o reconhecimento:

| Data type | Como esses dados aprimoram o reconhecimento |
|-----------|------------------------------------|
| Sentenças (declarações) | Melhore a precisão ao reconhecer nomes de produtos ou vocabulário específico do setor dentro do contexto de uma frase. |
| Pronúncias | Melhore a pronúncia de termos, acrônimos ou outras palavras incomuns, com pronúncias indefinidas. |

As frases podem ser fornecidas como um único arquivo de texto ou vários arquivos de texto. Para melhorar a precisão, use dados de texto que estejam mais próximos do declarações falado esperado. As pronúncias devem ser fornecidas como um único arquivo de texto. Tudo pode ser empacotado como um único arquivo zip e carregado no <a href="https://speech.microsoft.com/customspeech" target="_blank">Portal <span class="docon docon-navigate-external x-hidden-focus"> </span>de fala personalizada </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Diretrizes para criar um arquivo de sentenças

Para criar um modelo personalizado usando frases, você precisará fornecer uma lista de exemplos de declarações. Declarações _não_ precisam ser concluídos ou gramaticalmente corretos, mas eles devem refletir com precisão a entrada falada que você espera na produção. Se você quiser que determinados termos tenham um aumento de peso, adicione várias frases que incluam esses termos específicos.

Como orientação geral, a adaptação do modelo é mais eficaz quando o texto de treinamento é o mais próximo possível do texto real esperado na produção. O jargão e as frases específicas do domínio que você está direcionando para aprimorar devem ser incluídos no texto de treinamento. Quando possível, tente ter uma frase ou palavra-chave controlada em uma linha separada. Para palavras-chave e frases que são importantes para você (por exemplo, nomes de produtos), você pode copiá-las algumas vezes. Mas tenha em mente, não copie muito, isso pode afetar a taxa geral de reconhecimento.

Use esta tabela para garantir que o arquivo de dados relacionado para declarações esteja formatado corretamente:

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | UTF-8 BOM |
| N.º de expressões por linha | 1 |
| Tamanho máximo do ficheiro | 200 MB |

Além disso, você desejará considerar as seguintes restrições:

* Evite repetir caracteres mais de quatro vezes. Por exemplo: "aaaa" ou "uuuu".
* Não use caracteres especiais ou caracteres UTF-8 acima `U+00A1`.
* Os URIs serão rejeitados.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um arquivo de pronúncia

Se houver termos incomuns sem pronúncias padrão que seus usuários irão encontrar ou usar, você poderá fornecer um arquivo de pronúncia personalizado para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é recomendável usar arquivos de pronúncia personalizados para alterar a pronúncia de palavras comuns.

Isso inclui exemplos de um expressão falado e uma pronúncia personalizada para cada um:

| Formulário reconhecido/exibido | Formulário falado |
|--------------|--------------------------|
| 3CPO | três p e r |
| CNTK | c n t k |
| IEEE1394 | i triplo e |

O formulário falado é a seqüência fonética escrita. Ele pode ser composto por letras, palavras, sílabas ou uma combinação de todos os três.

A pronúncia personalizada está disponível em inglês (`en-US`) e alemão (`de-DE`). Esta tabela mostra os caracteres com suporte por idioma:

| Linguagem | Região | Carateres |
|----------|--------|------------|
| Português | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Alemão | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Use a tabela a seguir para garantir que o arquivo de dados relacionado para pronúncias esteja formatado corretamente. Os arquivos de pronúncia são pequenos e devem ter apenas alguns quilobytes de tamanho.

| Propriedade | Valor |
|----------|-------|
| Codificação de texto | A BOM UTF-8 (ANSI também tem suporte para inglês) |
| número de pronúncias por linha | 1 |
| Tamanho máximo do ficheiro | 1 MB (1 KB para camada gratuita) |

## <a name="next-steps"></a>Passos seguintes

* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
* [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
