---
title: Perguntas frequentes sobre a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Encontre respostas para perguntas comumente feitas sobre conceitos, código e cenários relacionados com a API text Analytics para serviços cognitivos azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73837128"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Perguntas frequentes (FAQ) sobre o Serviço Cognitivo de Análise de Texto

 Encontre respostas para perguntas comumente feitas sobre conceitos, código e cenários relacionados com a API de Análise de Texto para serviços cognitivos da Microsoft em Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>O Text Analytics pode identificar o sarcasmo?

A análise é para sentimento positivo-negativo em vez de deteção de humor.

Há sempre algum grau de imprecisão na análise de sentimentos, mas o modelo é mais útil quando não há nenhum significado ou subtexto escondido para o conteúdo. Ironia, sarcasmo, humor e conteúdo igualmente nuances dependem do contexto cultural e das normas para transmitir intenção. Este tipo de conteúdo é um dos mais desafiantes a analisar. Tipicamente, a maior discrepância entre uma determinada partitura produzida pelo analisador e uma avaliação subjetiva por um ser humano é para conteúdo com significado nuances.

## <a name="can-i-add-my-own-training-data-or-models"></a>Posso adicionar os meus próprios dados de treino ou modelos?

Não, os modelos estão pré-treinados. As únicas operações disponíveis em dados carregados são a pontuação, extração de frases-chave e deteção de linguagem. Não acolhemos modelos personalizados. Se pretender criar e hospedar modelos de aprendizagem automática personalizados, considere as [capacidades de aprendizagem](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)automática no Microsoft R Server .

## <a name="can-i-request-additional-languages"></a>Posso pedir línguas adicionais?

A análise de sentimentos e a extração de frases-chave estão disponíveis para um [número selecionado de idiomas](text-analytics-supported-languages.md). O processamento de linguagem natural é complexo e requer testes substanciais antes de uma nova funcionalidade poder ser lançada. Por esta razão, evitamos o pré-anúncio de apoio para que ninguém tenha uma dependência da funcionalidade que precisa de mais tempo para amadurecer. 

Para nos ajudar a priorizar quais as línguas a trabalhar a seguir, vote em línguas específicas na [Voz do Utilizador.](https://cognitive.uservoice.com/forums/555922-text-analytics) 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Porque é que a extração de frases-chave devolve algumas palavras, mas não outras?

A extração de frases-chave elimina palavras não essenciais e adjetivos autónomos. Combinações adjetivos-substantivos, tais como "vistas espetaculares" ou "tempo nebuloso" são devolvidas juntas.

Geralmente, a saída consiste em substantivos e objetos da frase. A produção é listada por ordem de importância, sendo a primeira frase a mais importante. A importância é medida pelo número de vezes que um determinado conceito é mencionado, ou a relação desse elemento com outros elementos do texto.

## <a name="why-does-output-vary-given-identical-inputs"></a>Por que a saída varia, dadas as entradas idênticas?

As melhorias nos modelos e algoritmos são anunciadas se a mudança for maior, ou se a atualização for menor. Com o tempo, poderá descobrir que a mesma entrada de texto resulta numa pontuação de sentimento diferente ou na saída de frase-chave. Esta é uma consequência normal e intencional da utilização de recursos de aprendizagem automática geridos na nuvem.

## <a name="next-steps"></a>Passos seguintes

A sua pergunta é sobre uma funcionalidade ou funcionalidade em falta? Considere solicitar ou votar no nosso [site userVoice.](https://cognitive.uservoice.com/forums/555922-text-analytics)

## <a name="see-also"></a>Consulte também

 [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Serviços Cognitivos](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
