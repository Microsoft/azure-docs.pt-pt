---
title: Processamento de texto e imagem incorporado durante a indexação
titleSuffix: Azure Cognitive Search
description: A extração de dados, a linguagem natural, as habilidades cognitivas de processamento de imagem adicionam semântica e estrutura ao conteúdo bruto num oleoduto de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4f0975aceeb55c8f184ebe4e6a44d819b4a8bf38
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837865"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Competências cognitivas incorporadas para processamento de texto e imagem durante a indexação (Pesquisa Cognitiva Azure)

Neste artigo, você aprende sobre as habilidades cognitivas fornecidas com Pesquisa Cognitiva Azure que você pode incluir em um skillset para extrair conteúdo e estrutura. Uma *habilidade cognitiva* é um módulo ou operação que transforma o conteúdo de alguma forma. Muitas vezes, é um componente que extrai dados ou infere a estrutura, e por isso aumenta a nossa compreensão dos dados de entrada. Quase sempre, a saída é baseada em texto. Uma *habilidade* é a coleção de habilidades que definem o gasoduto de enriquecimento. 

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="built-in-skills"></a>Competências incorporadas

Várias competências são flexíveis naquilo que consomem ou produzem. Em geral, a maioria das competências baseia-se em modelos pré-treinados, o que significa que não pode treinar o modelo usando os seus próprios dados de treino. A tabela seguinte enumera e descreve as competências fornecidas pela Microsoft. 

| Habilidade | Descrição |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Esta habilidade usa um modelo pré-treinado para detetar frases importantes baseadas na colocação de termo, regras linguísticas, proximidade a outros termos, e quão incomum o termo é dentro dos dados de origem. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Esta habilidade utiliza um modelo pré-treinado para detetar que linguagem é usada (um ID de idioma por documento). Quando várias línguas são usadas nos mesmos segmentos de texto, a saída é o LCID da língua predominantemente utilizada.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolida o texto de uma coleção de campos num único campo.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Esta habilidade utiliza um modelo pré-treinado para estabelecer entidades para um conjunto fixo de categorias: pessoas, localização, organização, e-mails, URLs, campos de data. |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Esta habilidade utiliza um modelo pré-treinado para extrair informações pessoalmente identificáveis de um determinado texto. A habilidade também dá várias opções para mascarar as entidades PII detetadas no texto.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Esta habilidade usa um modelo pré-treinado para obter um sentimento positivo ou negativo num registo por registo. O resultado é entre 0 e 1. Pontuações neutras ocorrem tanto para o caso nulo quando o sentimento não pode ser detetado, como para texto que é considerado neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide o texto em páginas para que possa enriquecer ou aumentar o conteúdo de forma incremental. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Esta habilidade usa um modelo pré-treinado para traduzir o texto de entrada numa variedade de idiomas para casos de normalização ou de utilização de localização. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Esta habilidade usa um algoritmo de deteção de imagem para identificar o conteúdo de uma imagem e gerar uma descrição de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconhecimento ótico de carácter. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Permite filtrar, atribuir um valor predefinido e fundir dados com base numa condição.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrai conteúdo de um ficheiro dentro do gasoduto de enriquecimento. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mapeie a saída para um tipo complexo (um tipo de dados multi-partes, que pode ser usado para um nome completo, um endereço multi-linhas, ou uma combinação de apelido e um identificador pessoal.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permite a extebilidade de um oleoduto de enriquecimento de IA fazendo uma chamada http para uma API web personalizada |


Para obter orientações sobre a criação de uma [habilidade personalizada,](cognitive-search-custom-skill-web-api.md)consulte [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md) e exemplo: Criar uma habilidade personalizada para enriquecimento de [IA](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Ver também

+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Definição de interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Tutorial: Indexação enriquecida com IA](cognitive-search-tutorial-blob.md)
