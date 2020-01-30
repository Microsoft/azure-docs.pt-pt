---
title: Links de documentação para enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Uma lista anotada de artigos, tutoriais, amostras e posts de blog relacionados com cargas de trabalho de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: e73e69f90b1228154d7f209c54c6b52cc03d5eb4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837795"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Recursos de documentação para enriquecimento de IA em Pesquisa Cognitiva Azure

O enriquecimento de IA é uma capacidade de indexação de Pesquisa Cognitiva Azure que encontra informação latente em fontes não-texto e texto indiferenciado, transformando-a em conteúdo pesquisável de texto completo em Pesquisa Cognitiva Azure.

Os seguintes artigos são a documentação completa para o enriquecimento de IA.

## <a name="getting-started"></a>Introdução
+ [Introdução à IA na Pesquisa Cognitiva Azure](cognitive-search-concept-intro.md)
+ [Quickstart: Criar uma habilidade cognitiva no portal Azure](cognitive-search-quickstart-blob.md)
+ [Tutorial: Indexação enriquecida com IA](cognitive-search-tutorial-blob.md)
+ [Exemplo: criando uma habilidade personalizada para o enriquecimento de ia](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Como orientar
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como referenciar anotações num conjunto de competências](cognitive-search-concept-annotations-syntax.md)
+ [Como mapear campos para um índice](cognitive-search-output-field-mapping.md)
+ [Como processar e extrair informações a partir de imagens](cognitive-search-concept-image-scenarios.md)
+ [Como reconstruir um índice de pesquisa cognitiva Azure](search-howto-reindex.md)
+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Sugestões de resolução de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referência

+ [Habilidades internas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Competências personalizadas
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Competências depreciadas](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Criar Qualificable (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar indexador (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Ver também

+ [API de pesquisa cognitiva azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [O que é a Pesquisa Cognitiva Azure?](search-what-is-azure-search.md)
