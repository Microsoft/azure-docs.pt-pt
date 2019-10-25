---
title: Links de documentação para enriquecimento de ia
titleSuffix: Azure Cognitive Search
description: Uma lista anotada de artigos, tutoriais, exemplos e postagens de blog relacionadas a cargas de trabalho de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 5fb1050fed2ab7318ad5b4ecafec7a96a9324575
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792061"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Recursos de documentação para enriquecimento de ia no Azure Pesquisa Cognitiva

O enriquecimento de AI é um recurso da indexação de Pesquisa Cognitiva do Azure que localiza informações latentes em fontes sem texto e texto não diferenciado, transformando-o em conteúdo pesquisável de texto completo no Azure Pesquisa Cognitiva.

Os artigos a seguir são a documentação completa para o enriquecimento do ia.

## <a name="getting-started"></a>Introdução
+ [Introdução ao enriquecimento de ia no Azure Pesquisa Cognitiva](cognitive-search-concept-intro.md)
+ [Início rápido: Experimente a enriquecimento do ia no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: indexação aprimorada com o ia](cognitive-search-tutorial-blob.md)
+ [Exemplo: criando uma habilidade personalizada para o enriquecimento de ia](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Diretrizes
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como fazer referência a anotações em um congrau de habilidade](cognitive-search-concept-annotations-syntax.md)
+ [Como mapear campos para um índice](cognitive-search-output-field-mapping.md)
+ [Como processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Como recriar um índice de Pesquisa Cognitiva do Azure](search-howto-reindex.md)
+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Sugestões de resolução de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referência

+ [Habilidades internas](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. Skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. Skills. Text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. Skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. Skills. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. Skills. Vision. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. Skills. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Competências personalizadas
  + [Microsoft. Skills. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Habilidades preteridas](cognitive-search-skill-deprecated.md)
  + [Microsoft. Skills. Text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Criar Qualificable (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar indexador (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Ver também

+ [API REST do Azure Pesquisa Cognitiva](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores no Azure Pesquisa Cognitiva](search-indexer-overview.md)
+ [O que é o Azure Pesquisa Cognitiva?](search-what-is-azure-search.md)
