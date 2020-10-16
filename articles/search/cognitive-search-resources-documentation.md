---
title: Ligações documentais para enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Uma lista anotada de artigos, tutoriais, amostras e posts de blog relacionados com cargas de trabalho de enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935352"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Recursos documentativos para enriquecimento de IA em Pesquisa Cognitiva Azure

O enriquecimento de IA é um complemento à indexação baseada em indexante que encontra informações latentes em fontes não-texto e texto indiferenciado, transformando-o em conteúdo completo pescêntrico de texto na Pesquisa Cognitiva Azure. 

Para o processamento incorporado, os modelos de IA pré-treinados em Serviços Cognitivos são chamados internamente para realizar as análises. Também pode integrar modelos personalizados usando Azure Machine Learning, Funções Azure ou outras abordagens.

Segue-se uma lista consolidada da documentação para o enriquecimento de IA.

## <a name="concepts"></a>Conceitos

+ [Enriquecimentos de IA](cognitive-search-concept-intro.md)
+ [Conjuntos de competências](cognitive-search-working-with-skillsets.md)
+ [Sessões de depuração](cognitive-search-debug-session.md)
+ [Arquivos de conhecimento](knowledge-store-concept-intro.md)
+ [Projeções](knowledge-store-projection-overview.md)
+ [Enriquecimento incremental (reutilização de um documento enriquecido em cache)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Mãos em passeios

+ [Quickstart: Criar uma habilidade cognitiva no portal Azure](cognitive-search-quickstart-blob.md)
+ [Tutorial: Indexação enriquecida com IA](cognitive-search-tutorial-blob.md)
+ [Tutorial: Diagnosticar, reparar e comprometer alterações na sua habilidade com as Debug Sessions](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Arquivos de conhecimento

+ [Quickstart: Criar uma loja de conhecimento no portal Azure](knowledge-store-create-portal.md)
+ [Criar uma loja de conhecimentos usando REST e Carteiro](knowledge-store-create-rest.md)
+ [Ver uma loja de conhecimento com o Storage Explorer](knowledge-store-view-storage-explorer.md)
+ [Ligue uma loja de conhecimento com o Power BI](knowledge-store-connect-power-bi.md)
+ [Exemplos de projeção (como moldar e exportar enriquecimentos)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Competências personalizadas (avançadas)

+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Exemplo: Criar uma habilidade personalizada utilizando funções Azure (e Bing Entity Search APIs)](cognitive-search-create-custom-skill-example.md)
+ [Exemplo: Criar uma habilidade personalizada usando Python](cognitive-search-custom-skill-python.md)
+ [Exemplo: Criar uma habilidade personalizada usando o Reconhecimento de Formulários](cognitive-search-custom-skill-form.md) 
+ [Exemplo: Criar uma habilidade personalizada usando Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Como orientar

+ [Anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)
+ [Definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [anotações de referência em um skillset](cognitive-search-concept-annotations-syntax.md)
+ [Map fields para um índice](cognitive-search-output-field-mapping.md)
+ [Processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Configure caching para enriquecimento incremental](search-howto-incremental-index.md)
+ [Como reconstruir um índice de Pesquisa Cognitiva Azure](search-howto-reindex.md)
+ [Sugestões de design](cognitive-search-concept-troubleshooting.md)
+ [Erros e avisos comuns](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Referência das competências

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionskill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.text.mergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionskill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.text.splitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.text.translationskill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Competências personalizadas
  + [Microsoft.Skills.custom.AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.custom.webapiskill](cognitive-search-custom-skill-web-api.md)

+ [Competências preteridas](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.text.namedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>APIs

+ [API REST](/rest/api/searchservice/)
  + [Create Skillset (versão api=2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Criar Indexer (versão api=2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Consulte também

+ [API REST do Azure Cognitive Search](/rest/api/searchservice/)
+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [O que é Azure Cognitive Search?](search-what-is-azure-search.md)