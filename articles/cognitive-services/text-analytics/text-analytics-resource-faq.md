---
title: Perguntas frequentes sobre a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Encontre respostas a perguntas comumente feitas sobre conceitos, códigos e cenários relacionados com a API de Análise de Texto para Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837128"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Perguntas frequentes (FAQ) sobre o Serviço Cognitivo de Análise de Texto

 Encontre respostas a perguntas comumente feitas sobre conceitos, códigos e cenários relacionados com a API text analytics para os Serviços Cognitivos da Microsoft em Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>O Text Analytics pode identificar o sarcasmo?

A análise é para sentimentos positivo-negativos em vez de deteção de humor.

Há sempre algum grau de imprecisão na análise de sentimentos, mas o modelo é mais útil quando não há nenhum significado ou subtexto oculto para o conteúdo. A ironia, o sarcasmo, o humor e o conteúdo igualmente nuances dependem do contexto cultural e das normas para transmitir intenção. Este tipo de conteúdo está entre os mais desafiantes a analisar. Tipicamente, a maior discrepância entre uma determinada pontuação produzida pelo analisador e uma avaliação subjetiva por um humano é para conteúdo com significado nuances.

## <a name="can-i-add-my-own-training-data-or-models"></a>Posso adicionar os meus próprios dados ou modelos de treino?

Não, as modelos estão pré-treinadas. As únicas operações disponíveis nos dados enviados são a pontuação, a extração de frases-chave e a deteção de linguagem. Não acolhemos modelos personalizados. Se quiser criar e hospedar modelos personalizados de aprendizagem automática, considere as capacidades de [aprendizagem automática no Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Posso pedir línguas adicionais?

A análise do sentimento e a extração de frases-chave estão disponíveis para um [número selecionado de idiomas](text-analytics-supported-languages.md). O processamento de linguagem natural é complexo e requer testes substanciais antes de ser lançada uma nova funcionalidade. Por esta razão, evitamos o apoio pré-anúncio para que ninguém se ressuma na funcionalidade que precisa de mais tempo para amadurecer. 

Para nos ajudar a priorizar quais as línguas a trabalhar em seguida, vote em idiomas específicos na [Voz do Utilizador](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Por que a extração de frases-chave devolve algumas palavras, mas não outras?

A extração de frases-chave elimina palavras não essenciais e adjetivos autónomos. Combinações de substantivos de adjetivos, tais como "vistas espetaculares" ou "nevoeiro" são retornadas juntas.

Geralmente, a saída consiste em substantivos e objetos da frase. A produção está listada por ordem de importância, sendo a primeira frase a mais importante. A importância é medida pelo número de vezes que é mencionado um determinado conceito, ou a relação desse elemento com outros elementos do texto.

## <a name="why-does-output-vary-given-identical-inputs"></a>Por que a saída varia, dadas as inputs idênticas?

As melhorias nos modelos e algoritmos são anunciadas se a mudança for maior, ou silenciosamente deslizada para o serviço se a atualização for menor. Com o tempo, pode descobrir que a mesma entrada de texto resulta numa pontuação de sentimento diferente ou na saída de frases-chave. Esta é uma consequência normal e intencional da utilização de recursos de aprendizagem automática geridos na nuvem.

## <a name="next-steps"></a>Passos seguintes

A sua pergunta sobre uma funcionalidade ou funcionalidade em falta? Considere solicitar ou votar no nosso [web site userVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Consulte também

 [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Serviços Cognitivos](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
