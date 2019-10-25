---
title: Extração de dados interna, linguagem natural, processamento de imagens
titleSuffix: Azure Cognitive Search
description: Extração de dados, linguagem natural, habilidades cognitivas de processamento de imagens adicionam semântica e estrutura ao conteúdo bruto em um pipeline de Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 435635018dc25ed2af0aec3d542c0388af8ab885
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792087"
---
# <a name="built-in-cognitive-skills-for-content-enrichment-azure-cognitive-search"></a>Habilidades cognitivas internas para o enriquecimento de conteúdo (Pesquisa Cognitiva do Azure)

Neste artigo, você aprenderá sobre as habilidades cognitivas fornecidas com o Azure Pesquisa Cognitiva. Uma *habilidade cognitiva* é uma operação que transforma o conteúdo de alguma maneira. Geralmente, é um componente que extrai dados ou infere a estrutura e, portanto, aumenta nossa compreensão dos dados de entrada. Quase sempre, a saída é baseada em texto. Um *conjunto de qualificações* é uma coleção de habilidades que definem o pipeline de enriquecimento. 

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="built-in-skills"></a>Habilidades internas

Várias habilidades são flexíveis no que elas consomem ou produzem. Em geral, a maioria das habilidades baseia-se em modelos pré-treinados, o que significa que você não pode treinar o modelo usando seus próprios dados de treinamento. A tabela a seguir enumera e descreve as habilidades fornecidas pela Microsoft. 

| Técnico | Descrição |
|-------|-------------|
| [Microsoft. Skills. Text. KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Essa habilidade usa um modelo pretreinado para detectar frases importantes com base no posicionamento do termo, nas regras linguísticas, na proximidade com outros termos e no quão incomum o termo está dentro dos dados de origem. |
| [Microsoft. Skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Essa habilidade usa um modelo pretreinado para detectar qual idioma é usado (uma ID de idioma por documento). Quando vários idiomas são usados dentro dos mesmos segmentos de texto, a saída é o LCID da linguagem usada predominantemente.|
| [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md) | Consolida o texto de uma coleção de campos em um único campo.  |
| [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Essa habilidade usa um modelo pretreinado para estabelecer entidades para um conjunto fixo de categorias: pessoas, localização, organização, emails, URLs, campos de data e hora. |
| [Microsoft. Skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)  | Essa habilidade usa um modelo pretreinado para pontuar sentimentos positivos ou negativos em um registro por registro. A pontuação está entre 0 e 1. Pontuações neutras ocorrem tanto para o caso nulo quando não é possível detectar sentimentos e para texto que é considerado neutro.  |
| [Microsoft. Skills. Text. SplitSkill](cognitive-search-skill-textsplit.md) | Divide o texto em páginas para que você possa enriquecer ou aumentar o conteúdo de forma incremental. |
| [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Essa habilidade usa um modelo pretreinado para converter o texto de entrada em uma variedade de idiomas para casos de uso de normalização ou localização. |
| [Microsoft. Skills. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Essa habilidade usa um algoritmo de detecção de imagem para identificar o conteúdo de uma imagem e gerar uma descrição de texto. |
| [Microsoft. Skills. Vision. OcrSkill](cognitive-search-skill-ocr.md) | Reconhecimento óptico de caracteres. |
| [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Permite filtrar, atribuir um valor padrão e mesclar dados com base em uma condição.|
| [Microsoft. Skills. util. ShaperSkill](cognitive-search-skill-shaper.md) | Mapeia a saída para um tipo complexo (um tipo de dados de várias partes, que pode ser usado para um nome completo, um endereço de várias linhas ou uma combinação de Last Name e um identificador pessoal.) |
| [Microsoft. Skills. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permite a extensibilidade de um pipeline de enriquecimento de ia fazendo uma chamada HTTP para uma API Web personalizada |


Para obter orientação sobre como criar uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md), consulte [como definir uma interface personalizada](cognitive-search-custom-skill-interface.md) e um [exemplo: criando uma habilidade personalizada para enriquecimento de ia](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Ver também

+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Definição de interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Tutorial: indexação aprimorada com o ia](cognitive-search-tutorial-blob.md)
