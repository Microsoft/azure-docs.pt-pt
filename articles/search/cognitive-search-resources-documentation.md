---
title: Recursos de documentação de pesquisa cognitiva-Azure Search
description: Uma lista anotada de artigos, tutoriais, exemplos e postagens de blog relacionadas a cargas de trabalho de pesquisa cognitiva em Azure Search.
services: search
manager: nitinme
author: HeidiSteen
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.subservice: cognitive-search
ms.openlocfilehash: 88e9db6526733960045e9a7f49e1f73fcaca8cf6
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639110"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Recursos de documentação para cargas de trabalho de pesquisa cognitiva

A pesquisa cognitiva, agora disponível para o público geral, é uma nova camada de enriquecimento na indexação de Azure Search que localiza informações latentes em fontes sem texto e texto não diferenciado, transformando-o em conteúdo pesquisável de texto completo no Azure Search.

Os artigos a seguir são a documentação completa para pesquisa cognitiva.

## <a name="getting-started"></a>Introdução
+ [O que é pesquisa cognitiva?](cognitive-search-concept-intro.md)
+ [Quickstart: Experimente a pesquisa cognitiva no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Exemplo: Criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Diretrizes
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como fazer referência a anotações em um congrau de habilidade](cognitive-search-concept-annotations-syntax.md)
+ [Como mapear campos para um índice](cognitive-search-output-field-mapping.md)
+ [Como processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Como recriar um índice de Azure Search](search-howto-reindex.md)
+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Sugestões de resolução de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referência

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Competências personalizadas
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Habilidades preteridas](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Criar Qualificable (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar indexador (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Consulte também

+ [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [O que é o Azure Search?](search-what-is-azure-search.md)
