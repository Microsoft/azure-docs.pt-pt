---
title: Perguntas mais frequentes sobre a API de análise de texto
titleSuffix: Azure Cognitive Services
description: Obtenha respostas a perguntas comuns sobre a API de análise de texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: a85fa543a6b26a5ea6452ce99fb91dc1ce465db7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545299"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Perguntas mais frequentes (FAQ) sobre o serviço cognitivos de análise de texto

 Encontre respostas para perguntas freqüentes sobre conceitos, código e cenários relacionados com a API de análise de texto para os serviços cognitivos da Microsoft no Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Análise de texto pode identificar a sarcasmo?

A análise é para sentimento positivo negativo em vez de deteção de comportamento.

Há sempre algum grau de imprecision na análise de sentimentos, mas o modelo é mais útil quando não existe significado oculto ou subtexto ao conteúdo. Ironia, sarcasmo, humor e conteúdo da mesma forma sutis baseiam-se no contexto cultural e normas para transmitir a intenção. Este tipo de conteúdo está entre as mais desafiadoras para analisar. Normalmente, a maior discrepância entre uma pontuação de determinado produzida pelo analisador e uma avaliação subjetiva por uma pessoa é para o conteúdo com significado sutis.

## <a name="can-i-add-my-own-training-data-or-models"></a>Posso adicionar meus próprios modelos de dados de treinamento ou?

Não, os modelos são pré-preparadas com. As únicas operações disponíveis em dados carregados são de classificação, extração de expressões-chave e deteção de idioma. Nós não hospedam modelos personalizados. Se pretender criar e alojar modelos de aprendizagem automática personalizada, considere a [de machine learning capacidades no Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Posso pedir idiomas adicionais?

Análise de sentimentos e extração de expressões-chave estão disponíveis para um [selecione o número de idiomas](text-analytics-supported-languages.md). Processamento de linguagem natural é complexo e requer testes substancial antes da nova funcionalidade pode ser lançada. Por esse motivo, podemos evitar previamente Anunciamos o suporte para que ninguém utiliza uma dependência na funcionalidade de que precisa de mais tempo a amadurecer. 

Para nos ajudar a priorizar que idiomas para trabalhar em Avançar, vote para idiomas específicos [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Por que motivo a extração de expressões-chave return algumas palavras, mas não para outros?

Extração de expressões-chave elimina palavras não essencial e os adjetivos de autónomo. Combinações de adjetivo-substantivo, como "vistas espetacular" ou "Meteorologia incerta" são devolvidas em conjunto.

Em geral, saída consiste substantivos e objetos da sentença. Saída é listada na ordem de importância, com a primeira expressão a ser o mais importante. Importância é medida pelo número de vezes que um determinado conceito é mencionado, ou a relação desse elemento para outros elementos no texto.

## <a name="why-does-output-vary-given-identical-inputs"></a>Por que motivo saída variar, devido a entradas idênticas?

Melhorias para modelos e algoritmos são anunciadas se a alteração for grande ou no modo silencioso rápida para o serviço se a atualização for menor. Ao longo do tempo, pode achar que os mesmos resultados de entrada de texto numa classificação de sentimento diferente ou de saída de expressões-chave. Esta é uma conseqüência normal e intencional de utilização de recursos de aprendizado de máquina gerida na cloud.

## <a name="next-steps"></a>Passos Seguintes

É sua pergunta sobre um recurso ou uma funcionalidade em falta? Considere solicitar ou Vote para ele em nosso [web site do UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Ver também

 [StackOverflow: API de Análise de Texto](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Serviços cognitivos](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
