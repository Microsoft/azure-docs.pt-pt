---
title: Melhorar um modelo para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Tipos particulares de transcrições com rótulo humano e textos relacionados podem melhorar a precisão do reconhecimento de um modelo de discurso-texto baseado no cenário de fala.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727880"
---
# <a name="improve-custom-speech-accuracy"></a>Melhorar a precisão da Voz Personalizada

Neste artigo, você vai aprender a melhorar a qualidade do seu modelo personalizado adicionando transcrições áudio, etiquetadas pelo homem e texto relacionado.

## <a name="accuracy-in-different-scenarios"></a>Precisão em diferentes cenários

Os cenários de reconhecimento da fala variam consoante a qualidade do áudio e a linguagem (vocabulário e estilo de fala). A tabela seguinte analisa quatro cenários comuns:

| Cenário | Qualidade áudio | Vocabulário | Estilo de fala |
|----------|---------------|------------|----------------|
| Call-center | Baixo, 8 kHz, pode ser 2 humanos em 1 canal de áudio, poderia ser comprimido | Estreito, único para domínio e produtos | Conversação, vagamente estruturada |
| Assistente de voz (como Cortana, ou uma janela drive-through) | Alto, 16 kHz | Entidade pesada (títulos de música, produtos, locais) | Palavras e frases claramente ditas |
| Ditado (mensagem instantânea, notas, pesquisa) | Alto, 16 kHz | Variado | Tomada de notas |
| Legendas fechadas em vídeo | Variada, incluindo o uso variado do microfone, música adicionada | Variado, de reuniões, discurso recitado, letras musicais | Ler, preparar ou vagamente estruturado |

Diferentes cenários produzem diferentes resultados de qualidade. A tabela seguinte examina como o conteúdo destes quatro cenários é classificado na taxa de erro de [palavras (WER)](how-to-custom-speech-evaluate-data.md). A tabela mostra quais os tipos de erros mais comuns em cada cenário.

| Cenário | Qualidade de Reconhecimento de Voz | Erros de inserção | Erros de Eliminação | Erros de substituição |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call-center | Médio (< 30% WER) | Baixo, exceto quando outras pessoas falam no fundo | Pode ser alto. Os call centers podem ser barulhentos, e os alto-falantes sobrepostos podem confundir o modelo | Média. Produtos e nomes de pessoas podem causar estes erros |
| Assistente de voz | Alto (pode ser < 10% WER) | Baixo | Baixo | Médio, devido a títulos de música, nomes de produtos ou locais |
| Ditado | Alto (pode ser < 10% WER) | Baixo | Baixo | Alto |
| Legendas fechadas em vídeo | Depende do tipo de vídeo (pode ser < 50% WER) | Baixo | Pode ser alto devido a música, ruídos, qualidade do microfone | Jargon pode causar estes erros |

Determinar os componentes do WER (número de erros de inserção, eliminação e substituição) ajuda a determinar que tipo de dados adicionar para melhorar o modelo. Utilize o [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) para ver a qualidade de um modelo de base. O portal informa as taxas de inserção, substituição e erro de eliminação que são combinadas na taxa de qualidade WER.

## <a name="improve-model-recognition"></a>Melhorar o reconhecimento de modelos

Pode reduzir os erros de reconhecimento adicionando dados de formação no [portal Discurso Personalizado](https://speech.microsoft.com/customspeech). 

Planeie manter o seu modelo personalizado adicionando materiais de origem periodicamente. O seu modelo personalizado necessita de formação adicional para se manter atento às alterações às suas entidades. Por exemplo, pode precisar de atualizações para nomes de produtos, nomes de músicas ou novos locais de serviço.

As secções seguintes descrevem como cada tipo de dados adicionais de treino podem reduzir os erros.

### <a name="add-related-text-sentences"></a>Adicionar frases de texto relacionadas

Frases de texto relacionadas adicionais podem reduzir principalmente os erros de substituição relacionados com o reconhecimento errado de palavras comuns e palavras específicas do domínio, mostrando-as em contexto. Palavras específicas do domínio podem ser palavras incomuns ou inventadas, mas a sua pronúncia deve ser simples de ser reconhecida.

> [!NOTE]
> Evite frases de texto relacionadas que incluam ruídos como caracteres irreconhecíveis ou palavras.

### <a name="add-audio-with-human-labeled-transcripts"></a>Adicione áudio com transcrições com rótulo humano

O áudio com transcrições com rótulo humano oferece as maiores melhorias de precisão se o áudio vier do caso de utilização do alvo. As amostras devem cobrir todo o âmbito da fala. Por exemplo, um call center para uma loja receberia a maioria das chamadas sobre roupa de banho e óculos de sol durante os meses de verão. Certifique-se de que a sua amostra inclui todo o âmbito de fala que pretende detetar.

Considere estes detalhes:

* O Custom Speech só pode capturar o contexto da palavra para reduzir erros de substituição, não erros de inserção ou eliminação.
* Evite amostras que incluam erros de transcrição, mas inclua uma diversidade de qualidade áudio.
* Evite frases que não estejam relacionadas com o seu domínio problemático. Frases não relacionadas podem prejudicar o seu modelo.
* Quando a qualidade das transcrições varia, pode duplicar frases excepcionalmente boas (como excelentes transcrições que incluem frases-chave) para aumentar o seu peso.

### <a name="add-new-words-with-pronunciation"></a>Adicione novas palavras com pronúncia

Palavras que são inventadas ou altamente especializadas podem ter pronúncias únicas. Estas palavras podem ser reconhecidas se a palavra pode ser dividida em palavras menores para pronunciá-la. Por exemplo, reconhecer a **Xbox,** pronunciar-se como **caixa X**. Esta abordagem não aumentará a precisão global, mas poderá aumentar o reconhecimento destas palavras-chave.

> [!NOTE]
> Esta técnica só está disponível para algumas línguas neste momento. Consulte a personalização para pronúncia [na tabela Discurso-a-texto](language-support.md) para obter mais detalhes.

## <a name="sources-by-scenario"></a>Fontes por cenário

A tabela que se segue mostra cenários de reconhecimento de voz e lista materiais de origem a considerar nas três categorias de conteúdos de formação acima listadas.

| Cenário | Frases de texto relacionadas | Transcrições com rótulo humano + áudio + | Novas palavras com pronúncia |
|----------|------------------------|------------------------------|------------------------------|
| Call-center             | documentos de marketing, website, análises de produtos relacionadas com a atividade do call center | call center chama transcrita por humanos | termos que têm pronúncias ambíguas (ver Xbox acima) |
| Assistente de voz         | lista de frases usando todas as combinações de comandos e entidades | gravar vozes falando comandos em dispositivo, e transcrever em texto | nomes (filmes, canções, produtos) que têm pronúncias únicas |
| Ditado               | entrada escrita, como mensagens instantâneas ou e-mails | semelhante a acima | semelhante a acima |
| Legendas fechadas em vídeo | Scripts de séries de TV, filmes, conteúdos de marketing, resumos de vídeo | transcrições exatas de vídeos | semelhante a acima |

## <a name="next-steps"></a>Passos seguintes

- [Preparar o seu modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
- [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)