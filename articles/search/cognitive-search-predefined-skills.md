---
title: Processamento de texto e imagem incorporado durante a indexação
titleSuffix: Azure Cognitive Search
description: A extração de dados, a linguagem natural, as capacidades cognitivas de processamento de imagem adicionam semântica e estrutura ao conteúdo bruto num pipeline de Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 417cc13c4a201a0dd86a846c347cb395b8d81c43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535530"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Competências cognitivas incorporadas para processamento de texto e imagem durante a indexação (Pesquisa Cognitiva Azure)

Neste artigo, você aprende sobre as habilidades cognitivas fornecidas com a Azure Cognitive Search que você pode incluir em um skillset para extrair conteúdo e estrutura. Uma *habilidade cognitiva* é um módulo ou operação que transforma o conteúdo de alguma forma. Muitas vezes, é um componente que extrai dados ou infere a estrutura, e, portanto, aumenta a nossa compreensão dos dados de entrada. Quase sempre, a saída é baseada em texto. Um *skillset* é a coleção de competências que definem o oleoduto de enriquecimento. 

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).
>
> A funcionalidade [de enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) permite-lhe fornecer uma cache que permite ao indexante ser mais eficiente na execução apenas das capacidades cognitivas necessárias se modificar o seu skillset no futuro, poupando-lhe tempo e dinheiro.


## <a name="built-in-skills"></a>Competências incorporadas

Várias competências são flexíveis naquilo que consomem ou produzem. Em geral, a maioria das habilidades baseia-se em modelos pré-treinados, o que significa que não se pode treinar o modelo usando os seus próprios dados de treino. A tabela a seguir enumera e descreve as competências fornecidas pela Microsoft. 

| Habilidade | Descrição |
|-------|-------------|
|[Microsoft.Skills.text.customentityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Procura texto a partir de uma lista personalizada de palavras e frases definida pelo utilizador.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Esta habilidade usa um modelo pré-treinado para detetar frases importantes com base na colocação de termo, regras linguísticas, proximidade a outros termos, e quão incomum o termo é dentro dos dados de origem. |
| [Microsoft.Skills.Text.LanguageDetectionskill](cognitive-search-skill-language-detection.md)  | Esta habilidade utiliza um modelo pré-treinado para detetar qual a língua utilizada (um ID de idioma por documento). Quando várias línguas são usadas dentro dos mesmos segmentos de texto, a saída é o LCID da língua predominantemente usada.|
| [Microsoft.Skills.text.mergeSkill](cognitive-search-skill-textmerger.md) | Consolida o texto de uma coleção de campos num único campo.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Esta habilidade utiliza um modelo pré-treinado para estabelecer entidades para um conjunto fixo de categorias: pessoas, localização, organização, e-mails, URLs, campos de datas. |
| [Microsoft.Skills.Text.PIIDetectionskill](cognitive-search-skill-pii-detection.md)  | Esta habilidade utiliza um modelo pré-treinado para extrair informações pessoais de um determinado texto. A habilidade também dá várias opções para mascarar as entidades de informação pessoal detetadas no texto.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Esta habilidade usa um modelo pré-treinado para obter sentimentos positivos ou negativos numa base recorde. A pontuação é entre 0 e 1. As pontuações neutras ocorrem tanto para o caso nulo quando o sentimento não pode ser detetado, como para texto que é considerado neutro.  |
| [Microsoft.Skills.text.splitSkill](cognitive-search-skill-textsplit.md) | Divide o texto em páginas para que possa enriquecer ou aumentar o conteúdo gradualmente. |
| [Microsoft.Skills.text.translationskill](cognitive-search-skill-text-translation.md) | Esta habilidade utiliza um modelo pré-treinado para traduzir o texto de entrada numa variedade de idiomas para casos de normalização ou localização. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Esta habilidade utiliza um algoritmo de deteção de imagem para identificar o conteúdo de uma imagem e gerar uma descrição de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconhecimento de caracteres óticos. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Permite filtrar, atribuir um valor predefinido e fundir dados com base numa condição.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrai conteúdo de um ficheiro dentro do gasoduto de enriquecimento. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mapeia a saída para um tipo complexo (um tipo de dados multi-partes, que pode ser usado para um nome completo, um endereço multi-linha, ou uma combinação de apelido e um identificador pessoal.) |
| [Microsoft.Skills.custom.webapiskill](cognitive-search-custom-skill-web-api.md) | Permite a extensibilidade de um oleoduto de enriquecimento de IA fazendo uma chamada HTTP para uma API web personalizada |
| [Microsoft.Skills.custom.AmlSkill](cognitive-search-aml-skill.md) | Permite a extensibilidade de um oleoduto de enriquecimento de IA com um modelo de Aprendizagem automática Azure |


Para obter orientações sobre a criação de uma [habilidade personalizada,](cognitive-search-custom-skill-web-api.md)consulte [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md) e [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Consulte também

+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Definição de interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Tutorial: Indexação enriquecida com IA](cognitive-search-tutorial-blob.md)
