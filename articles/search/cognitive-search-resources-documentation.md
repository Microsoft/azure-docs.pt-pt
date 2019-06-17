---
title: Recursos de documentação de pesquisa cognitiva - Azure Search
description: Uma lista anotada de artigos, tutoriais, exemplos e blog publica pesquisa relacionada cognitivos para cargas de trabalho no Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 225ac86efba8a32b085a5fb7fc41bed48c499eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65022171"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Recursos de documentação para cargas de trabalho de pesquisa cognitiva

Pesquisa cognitiva, agora disponível em geral, é uma nova camada de melhoria na indexação de pesquisa do Azure, que encontra informações latentes em origens que não sejam de texto e texto indiferenciado, transformá-los em conteúdo pesquisável de texto completo no Azure Search.

Os seguintes artigos são a documentação completa de pesquisa cognitiva.

## <a name="getting-started"></a>Introdução
+ [O que é a pesquisa cognitiva?](cognitive-search-concept-intro.md)
+ [Quickstart: Experimentar a pesquisa cognitiva no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Saiba a APIs de pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Exemplo: criar uma competência personalizada](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Documentação com procedimentos
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Como fazer anotações num conjunto de capacidades de referência](cognitive-search-concept-annotations-syntax.md)
+ [Como mapear campos para um índice](cognitive-search-output-field-mapping.md)
+ [Como processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Como reconstruir um índice da Azure Search](search-howto-reindex.md)
+ [Como definir uma interface de competências personalizadas](cognitive-search-custom-skill-interface.md)
+ [Sugestões de resolução de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referência

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Criar conjunto de capacidades (api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar indexador (api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Consulte também

+ [API REST da Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores na Azure Search](search-indexer-overview.md)
+ [O que é o Azure Search?](search-what-is-azure-search.md)
