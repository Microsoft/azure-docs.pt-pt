---
title: Suporte linguístico - QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma lista de cultura, línguas naturais apoiadas pela QnA Maker para a sua base de conhecimento. Não misture línguas na mesma base de conhecimento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650895"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Suporte linguístico para um recurso e bases de conhecimento do QnA Maker

O idioma para o serviço é selecionado quando cria a primeira base de conhecimento no recurso. Todas as bases de conhecimento adicionais no recurso devem estar na mesma língua. 

O idioma determina a relevância dos resultados que o QnA Maker fornece em resposta às consultas do utilizador. O recurso QnA Maker, e todas as bases de conhecimento dentro desse recurso, suportam uma única língua. A única língua é necessária para fornecer os melhores resultados de resposta para uma consulta.

## <a name="single-language-per-resource"></a>Língua única por recurso

Considere o seguinte:

* Um serviço QnA Maker, e todas as suas bases de conhecimento, suportam apenas uma língua.
* A linguagem é explicitamente definida quando a primeira base de conhecimento do serviço é criada
* A linguagem é determinada a partir dos ficheiros e URLs adicionados quando a base de conhecimento é criada
* A linguagem não pode ser alterada para qualquer outra base de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (ranker #1) e pelo serviço QnA Maker (ranker #2) para gerar a melhor resposta a uma consulta

## <a name="supporting-multiple-languages"></a>Apoio a várias línguas

Se precisar de suportar um sistema de base de conhecimento, que inclui vários idiomas, pode:

* Utilize o [serviço de Tradução](../../translator/translator-info-overview.md) para traduzir uma pergunta numa única língua antes de enviar a pergunta para a sua base de conhecimentos. Isto permite-lhe focar-se na qualidade de uma única língua e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker, e uma base de conhecimento dentro desse recurso, para cada idioma. Isto permite-lhe gerir perguntas alternativas separadas e responder a textos mais matizados para cada idioma. Isto dá-lhe muito mais flexibilidade, mas requer um custo de manutenção muito mais elevado quando as perguntas ou respostas mudam em todas as línguas.

Rever [línguas suportadas](../overview/language-support.md) para o QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Apoie cada idioma com um recurso QnA Maker

* Crie um recurso QnA Maker para cada idioma
* Adicione apenas ficheiros e URLs para esse idioma
* Use uma convenção de nomeação para o recurso para identificar o idioma. Um exemplo é `qna-maker-fr` para todas as bases de conhecimento para documentos franceses


## <a name="languages-supported"></a>Idiomas suportados

A lista a seguir contém os idiomas suportados para um recurso QnA Maker. 

|Idioma|
|--|
|Árabe|
|Arménio|
|Bangla|
|Basco|
|Búlgaro|
|Catalão|
|Chinese_Simplified|
|Chinese_Traditional|
|Croata|
|Checo|
|Dinamarquês|
|Neerlandês|
|Inglês|
|Estónio|
|Finlandês|
|Francês|
|Galego|
|Alemão|
|Grego|
|Guzerate|
|Hebraico|
|Hindi|
|Húngaro|
|Islandês|
|Indonésio|
|Irlandês|
|Italiano|
|Japonês|
|Canarês|
|Coreano|
|Letão|
|Lituano|
|Malaiala|
|Malaio|
|Norueguês|
|Polaco|
|Português|
|Punjabi|
|Romeno|
|Russo|
|Serbian_Cyrillic|
|Serbian_Latin|
|Eslovaco|
|Esloveno|
|Espanhol|
|Sueco|
|Tâmil|
|Telugu|
|Tailandês|
|Turco|
|Ucraniano|
|Urdu|
|Vietnamita|

## <a name="query-matching-and-relevance"></a>Combinação de consulta e relevância
O QnA Maker depende dos [analisadores de linguagem Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/language-support) para fornecer resultados.

Enquanto as capacidades de Pesquisa Cognitiva Azure estão a par para línguas suportadas, o QnA Maker tem um ranker adicional que se situa acima dos resultados de pesquisa do Azure. Neste modelo ranker, usamos algumas características semânticas e baseadas em palavras nas seguintes línguas.

|Línguas com ranker adicional|
|--|
|Chinês|
|Checo|
|Neerlandês|
|Inglês|
|Francês|
|Alemão|
|Húngaro|
|Italiano|
|Japonês|
|Coreano|
|Polaco|
|Português|
|Espanhol|
|Sueco|

Este ranking adicional é um trabalho interno do ranker do QnA Maker.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Seleção de idioma](../how-to/language-knowledge-base.md)
