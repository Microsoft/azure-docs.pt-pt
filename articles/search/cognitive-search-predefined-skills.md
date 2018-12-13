---
title: Extração de dados incorporada, linguagem natural, imagem processamento - Azure Search
description: Extração de dados, linguagem natural, capacidades cognitivas de processamento de imagens adicionar semântica e a estrutura a conteúdos não processados num pipeline do Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bc1353ffb4514622ce0ef6e5c3ced76adc7f999f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314793"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Competências predefinidas para melhoria do conteúdo (Azure Search)

Neste artigo, ficará a conhecer as capacidades cognitivas fornecidas com o Azure Search. R *competências cognitivas* é uma operação que transforma o conteúdo de alguma forma. Muitas vezes, é um componente que extrai dados ou infere a estrutura e, portanto, aumenta a nossa compreensão dos dados de entrada. Quase sempre, a saída é baseado em texto. R *conjunto de capacidades* é o conjunto de habilidades que definem o pipeline de melhoria. 

> [!NOTE]
> A partir de 21 de Dezembro de 2018, poderá associar um recurso dos serviços cognitivos com um conjunto de capacidades do Azure Search. Isso permitirá que comecem a cobrança de consumo para a execução do conjunto de capacidades. Esta data, podemos também começar a cobrar extração de imagem como parte da fase de aberturas de documentos. Continuaremos a oferecer a extração de texto de documentos sem custos adicionais.
>
> A execução das habilidades internas será cobrada existente [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services/) . Preços de extração de imagem são cobrados ao preço de pré-visualização e está descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba mais [mais](cognitive-search-attach-cognitive-services.md).

## <a name="predefined-skills"></a>Competências predefinidas

Várias habilidades são flexíveis o que consomem ou produzir. Em geral, a maioria das habilidades são baseadas em modelos com formação prévia, o que significa que não é possível preparar o modelo de utilizar os seus próprios dados de treinamento. Para obter orientações sobre como criar uma habilidade personalizada, veja [como definir uma interface personalizada](cognitive-search-custom-skill-interface.md) e [exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md). A tabela seguinte enumera e descreve as capacidades fornecidas pela Microsoft. 

| Habilidades | Descrição |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Essa habilidade utiliza um modelo de pretrained para detetar as frases importantes com base na colocação de termo, regras lingüísticas, proximidade outros termos e é o termo como invulgar na origem de dados. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Este utiliza de habilidade um modelo pretrained para detetar o idioma que é utilizada (um ID de idioma por documento). Quando vários idiomas forem utilizados dentro dos mesmo segmentos de texto, a saída é o LCID do idioma predominantemente utilizado.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Consolida texto de uma coleção de campos num único campo.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Essa habilidade utiliza um modelo de pretrained para estabelecer entidades para um conjunto fixo de categorias: as pessoas, a localização, a organização. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Essa habilidade utiliza um modelo de pretrained a pontuação de sentimento positivo ou negativo numa base de Registro por Registro. A pontuação é entre 0 e 1. As pontuações neutras ocorrerem caso nulo quando não é possível detetar o sentimento e para o texto que é considerado neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide texto numa páginas para que possa melhorar ou aumentar o conteúdo de forma incremental. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Essa habilidade usa um algoritmo de deteção de imagem para identificar o conteúdo de uma imagem e gerar uma descrição de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconhecimento ótico de carateres. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mapas de saída para um tipo complexo (com várias partes tipo de dados, que pode ser utilizado para um nome completo, um endereço com várias linha ou uma combinação de apelido e um identificador pessoal.) |

## <a name="see-also"></a>Consulte também

+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Definição de interface de competências personalizada](cognitive-search-custom-skill-interface.md)
+ [Tutorial: Indexação com pesquisa cognitiva enriquecida](cognitive-search-tutorial-blob.md)