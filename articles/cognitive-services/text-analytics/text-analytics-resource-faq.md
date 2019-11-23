---
title: Perguntas frequentes sobre o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao API de Análise de Texto para serviços cognitivas do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837128"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Perguntas frequentes sobre o Análise de Texto serviço cognitiva

 Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao API de Análise de Texto para serviços cognitivas da Microsoft no Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Análise de Texto pode identificar o sarcasmo?

A análise destina-se a sentimentos positivos e negativos em vez da detecção de humor.

Sempre há algum grau de imprecisão na análise de sentimentos, mas o modelo é mais útil quando não há nenhum significado ou subtexto oculto para o conteúdo. O ferro de passar, sarcasmo, humor e conteúdo com nuances semelhantes dependem do contexto cultural e das normas para transmitir a intenção. Esse tipo de conteúdo está entre os mais desafiadores a serem analisados. Normalmente, a maior discrepância entre uma determinada pontuação produzida pelo analisador e uma avaliação subjetiva por um humano é para o conteúdo com significado nuancedo.

## <a name="can-i-add-my-own-training-data-or-models"></a>Posso adicionar meus próprios modelos ou dados de treinamento?

Não, os modelos são pretreinados. As únicas operações disponíveis nos dados carregados são pontuação, extração de frases-chave e detecção de idioma. Não hospedamos modelos personalizados. Se você quiser criar e hospedar modelos de aprendizado de máquina personalizados, considere os [recursos de aprendizado de máquina no Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Posso solicitar idiomas adicionais?

Análise de sentimentos e extração de frases-chave estão disponíveis para um [número de idiomas selecionado](text-analytics-supported-languages.md). O processamento de idioma natural é complexo e requer testes substanciais para que a nova funcionalidade possa ser lançada. Por esse motivo, evitamos o suporte prévio para que ninguém tenha uma dependência da funcionalidade que precisa de mais tempo para amadurecer. 

Para nos ajudar a priorizar quais idiomas trabalhar em seguida, vote em idiomas específicos na [voz do usuário](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Por que a extração de frases-chave retorna algumas palavras, mas não outras?

A extração de frases-chave elimina palavras não essenciais e adjetivos autônomos. As combinações de adjetivo-substantivo, como "exibições espetaculares" ou "Foggy Weather" são retornadas juntas.

Geralmente, a saída consiste em substantivos e objetos da frase. A saída é listada em ordem de importância, com a primeira frase sendo a mais importante. A importância é medida pelo número de vezes que um determinado conceito é mencionado, ou a relação desse elemento com outros elementos no texto.

## <a name="why-does-output-vary-given-identical-inputs"></a>Por que a saída varia, dadas entradas idênticas?

Melhorias nos modelos e algoritmos serão anunciadas se a alteração for principal, ou integrada silenciosa ao serviço se a atualização for secundária. Ao longo do tempo, você pode achar que a mesma entrada de texto resulta em uma pontuação de sentimentos diferente ou saída de frase-chave. Essa é uma consequência comum e intencional do uso de recursos de aprendizado de máquina gerenciados na nuvem.

## <a name="next-steps"></a>Passos seguintes

Sua pergunta sobre um recurso ou uma funcionalidade ausente? Considere solicitar ou votar nele em nosso [site UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Consulte também

 [StackOverflow: API de Análise de Texto](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: serviços cognitivas](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
