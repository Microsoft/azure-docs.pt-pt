---
title: Melhorar um modelo para discurso personalizado - serviço de fala
titleSuffix: Azure Cognitive Services
description: Tipos específicos de transcrições com rótulo humano e texto conexo podem melhorar a precisão do reconhecimento de um modelo de fala-a-texto baseado no cenário de falar.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727880"
---
# <a name="improve-custom-speech-accuracy"></a>Melhorar a precisão da fala personalizada

Neste artigo, você vai aprender a melhorar a qualidade do seu modelo personalizado adicionando áudio, transcrições com rótulo humano e texto relacionado.

## <a name="accuracy-in-different-scenarios"></a>Precisão em diferentes cenários

Os cenários de reconhecimento da fala variam de qualidade e linguagem áudio (estilo vocabulário e fala). A tabela que se segue examina quatro cenários comuns:

| Cenário | Qualidade áudio | Vocabulário | Estilo de Falar |
|----------|---------------|------------|----------------|
| Call-center | Baixo, 8 kHz, pode ser 2 humanos em 1 canal de áudio, pode ser comprimido | Estreito, único ao domínio e produtos | Conversacional, vagamente estruturado |
| Assistente de voz (como Cortana, ou uma janela drive-through) | Alto, 16 kHz | Entidade pesada (títulos de música, produtos, localizações) | Palavras e frases claramente declaradas |
| Ditado (mensagem instantânea, notas, pesquisa) | Alto, 16 kHz | Variado | Tomada de nota |
| Legenda de vídeo fechada | Variado, incluindo uso variado de microfone, adicionado música | Variado, de reuniões, discurso recitado, letra musical | Ler, preparar ou estruturar vagamente |

Diferentes cenários produzem diferentes resultados de qualidade. O quadro seguinte examina como o conteúdo destes quatro cenários classifica a taxa de [erro da palavra (WER)](how-to-custom-speech-evaluate-data.md). A tabela mostra quais os tipos de erro mais comuns em cada cenário.

| Cenário | Qualidade de reconhecimento de voz | Erros de inserção | Erros de eliminação | Erros de Substituição |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call-center | Médio (< 30% WER) | Baixo, exceto quando outras pessoas falam no fundo | Pode ser alto. Call centers pode ser barulhento, e alto-falantes sobrepostos podem confundir o modelo | Média. Produtos e nomes das pessoas podem causar estes erros |
| Assistente de voz | Alto (pode ser < 10% WER) | Baixa | Baixa | Médio, devido a títulos de música, nomes de produtos ou locais |
| Ditado | Alto (pode ser < 10% WER) | Baixa | Baixa | Alta |
| Legenda de vídeo fechada | Depende do tipo de vídeo (pode ser < 50% WER) | Baixa | Pode ser alto devido à música, ruídos, qualidade do microfone | Jargão pode causar estes erros |

Determinar os componentes do WER (número de erros de inserção, eliminação e substituição) ajuda a determinar que tipo de dados adicionar para melhorar o modelo. Utilize o [portal De Discurso Personalizado](https://speech.microsoft.com/customspeech) para ver a qualidade de um modelo de base. O portal reporta taxas de erro de inserção, substituição e eliminação combinadas na taxa de qualidade WER.

## <a name="improve-model-recognition"></a>Melhorar o reconhecimento de modelos

Pode reduzir os erros de reconhecimento adicionando dados de formação no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech). 

Planeie manter o seu modelo personalizado adicionando materiais de origem periodicamente. O seu modelo personalizado necessita de formação adicional para se manter atento às alterações às suas entidades. Por exemplo, pode necessitar de atualizações para nomes de produtos, nomes de músicas ou novos locais de serviço.

As seguintes secções descrevem como cada tipo de dados adicionais de treino podem reduzir erros.

### <a name="add-related-text-sentences"></a>Adicionar frases de texto relacionadas

As frases de texto relacionadas adicionais podem, em primeiro lugar, reduzir os erros de substituição relacionados com o reconhecimento indevido de palavras comuns e palavras específicas de domínio, mostrando-as em contexto. Palavras específicas do domínio podem ser palavras incomuns ou inventadas, mas a sua pronúncia deve ser simples de ser reconhecida.

> [!NOTE]
> Evite frases de texto relacionadas que incluam ruídos como caracteres irreconhecíveis ou palavras.

### <a name="add-audio-with-human-labeled-transcripts"></a>Adicione áudio com transcrições com rótulo humano

O áudio com transcrições com rótulo humano oferece as maiores melhorias de precisão se o áudio vier do caso de utilização do alvo. As amostras devem abranger todo o âmbito da fala. Por exemplo, um call center para uma loja de retalho receberia a maioria das chamadas sobre fatos de banho e óculos de sol durante os meses de verão. Certifique-se de que a sua amostra inclui todo o âmbito da fala que pretende detetar.

Considere estes detalhes:

* A Fala Personalizada só pode capturar o contexto de palavras para reduzir erros de substituição, não erros de inserção ou eliminação.
* Evite amostras que incluam erros de transcrição, mas incluem uma diversidade de qualidade áudio.
* Evite frases que não estejam relacionadas com o seu domínio problemático. Frases não relacionadas podem prejudicar o seu modelo.
* Quando a qualidade das transcrições varia, pode duplicar frases excepcionalmente boas (como excelentes transcrições que incluem frases-chave) para aumentar o seu peso.

### <a name="add-new-words-with-pronunciation"></a>Adicione novas palavras com pronúncia

Palavras que são inventadas ou altamente especializadas podem ter pronúncias únicas. Estas palavras podem ser reconhecidas se a palavra puder ser dividida em palavras menores para pronunciá-la. Por exemplo, reconhecer a **Xbox**, pronuncia-se como **X box**. Esta abordagem não aumentará a precisão global, mas poderá aumentar o reconhecimento destas palavras-chave.

> [!NOTE]
> Esta técnica só está disponível para algumas línguas neste momento. Consulte a personalização para pronúncia na [tabela Discurso-a-texto](language-support.md) para obter mais detalhes.

## <a name="sources-by-scenario"></a>Fontes por cenário

A tabela seguinte mostra cenários de reconhecimento de voz e lista materiais-fonte a considerar nas três categorias de conteúdos de formação acima listadas.

| Cenário | Frases de texto relacionadas | Audio + transcrições com rótulo humano | Novas palavras com pronúncia |
|----------|------------------------|------------------------------|------------------------------|
| Call-center             | documentos de marketing, website, avaliações de produtos relacionadas com atividade de call center | call center chama transcrito por humanos | termos que têm pronúncias ambíguas (ver Xbox acima) |
| Assistente de voz         | lista de frases usando todas as combinações de comandos e entidades | gravar vozes que falam comandos em dispositivo, e transcrever em texto | nomes (filmes, canções, produtos) que têm pronúncias únicas |
| Ditado               | entrada escrita, como mensagens instantâneas ou e-mails | semelhante ao acima | semelhante ao acima |
| Legenda de vídeo fechada | Scripts de séries de TV, filmes, conteúdos de marketing, resumos de vídeo | transcrições exatas de vídeos | semelhante ao acima |

## <a name="next-steps"></a>Próximos passos

- [Preparar o seu modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
- [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)