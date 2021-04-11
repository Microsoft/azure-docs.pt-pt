---
title: Adicionar verificação ortográfica
titleSuffix: Azure Cognitive Search
description: Fixe a correção ortográfica ao pipeline de consulta, para fixar tipografias em termos de consulta antes de executar a consulta.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625913"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Adicione verificação ortográfica a consultas na Pesquisa Cognitiva

> [!IMPORTANT]
> A correção ortográfica está em visualização pública, disponível apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Durante o lançamento inicial de pré-visualização, não há qualquer custo para soletrar. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

Pode melhorar a recuperação corrigindo os termos de consulta individual de pesquisa antes de chegar ao motor de busca. O parâmetro **soletrador** é suportado para todos os tipos de consulta: [simples,](query-simple-syntax.md) [completo,](query-lucene-syntax.md)e a nova opção [semântica](semantic-how-to-query-request.md) atualmente em visualização pública.

## <a name="prerequisites"></a>Pré-requisitos

+ Um índice de pesquisa existente, contendo conteúdo inglês. Atualmente, a correção do feitiço não funciona com [sinónimos](search-synonyms.md). Evite usá-lo em índices que especifiquem um mapa de sinónimo em qualquer definição de campo.

+ Um cliente de pesquisa para enviar consultas

  O cliente de pesquisa deve suportar pré-visualização REST APIs no pedido de consulta. Pode utilizar [o Carteiro](search-get-started-rest.md), [Código de Estúdio Visual](search-get-started-vs-code.md)ou código que modificou para fazer chamadas REST para as APIs de pré-visualização.

+ [Um pedido de consulta](/rest/api/searchservice/preview-api/search-documents) que utiliza correção ortográfica tem "api-versão=2020-06-30-Preview", "speller=lexicon", e "queryLanguage=en-us".

  A queryLanguage é necessária para soletrar, e atualmente "en-us" é o único valor válido.

> [!Note]
> O parâmetro soletrador está disponível em todos os níveis, nas mesmas regiões que proporcionam pesquisa semântica. Não precisa de se inscrever para aceder a esta funcionalidade de pré-visualização. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

## <a name="spell-correction-with-simple-search"></a>Correção ortográfica com pesquisa simples

O exemplo a seguir utiliza o índice de amostra de hotéis incorporado para demonstrar a correção do feitiço numa simples consulta de texto de formulário livre. Sem correção ortográfica, a consulta retorna zero resultados. Com a correção, a consulta devolve um resultado para o resort familiar de Johnson.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Correção ortográfica com Lucene completo

A correção ortográfica ocorre em termos de consulta individual que são submetidos a análise, e é por isso que você pode usar o parâmetro soletrador com algumas consultas lucene, mas não outros.

+ Formulários de consulta incompatíveis que contornam a análise de texto incluem: wildcard, regex, fuzzy
+ Formulários de consulta compatíveis incluem: pesquisa de campo, proximidade, aumento de prazos

Este exemplo utiliza a pesquisa de campo sobre o campo categoria, com sintaxe Lucene completa, e um termo de consulta mal escrito. Ao incluir soletrador, a tipografia em "Suiite" é corrigida e a consulta tem sucesso.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Correção ortográfica com pesquisa semântica

Esta consulta, com tipografias em todos os termos, exceto um, passa por correções ortográficas para devolver resultados relevantes. Para saber mais, consulte [Criar uma consulta semântica.](semantic-how-to-query-request.md)

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Considerações linguísticas

O parâmetro de queryLanguage necessário para soletrar deve ser consistente com quaisquer [analisadores de linguagem](index-add-language-analyzers.md) atribuídos às definições de campo no esquema de índice. 

+ queryLanguage determina quais os léxicos que são usados para verificação ortográfica, e também é usado como uma entrada para o [algoritmo de classificação semântica](semantic-answers.md) se estiver a usar "queriaType=semântica".

+ Os analisadores linguísticos são utilizados durante a indexação e execução de consultas para encontrar documentos correspondentes no índice de pesquisa. Um exemplo de uma definição de campo que usa um analisador de linguagem é `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Para obter melhores resultados ao utilizar soletrador, se a queryLanguage for "en-us", qualquer analisador de idiomas também deve ser uma variante em inglês ("en.microsoft" ou "en.lucene").

> [!NOTE]
> Os analisadores linguísticos agnósticos (tais como palavra-chave, simples, padrão, stop, whitespace, ou `standardasciifolding.lucene` ) não entram em conflito com as definições de dúvida.

Num pedido de consulta, a consultaLanguage que definiu aplica-se igualmente ao soletrador, respostas e legendas. Não há substituição de peças individuais.

Embora o conteúdo de um índice de pesquisa possa ser composto em vários idiomas, a entrada de consulta é mais provável em um. O motor de busca não verifica a compatibilidade da queryLanguage, do analisador de línguas e do idioma em que o conteúdo é composto, por isso certifique-se de que as consultas de âmbito em conformidade para evitar produzir resultados incorretos.

## <a name="next-steps"></a>Passos seguintes

+ [Criar uma consulta semântica](semantic-how-to-query-request.md)
+ [Criar uma consulta básica](search-query-create.md)
+ [Use a sintaxe de consulta lucene completa](query-Lucene-syntax.md)
+ [Use sintaxe de consulta simples](query-simple-syntax.md)
+ [Visão geral da pesquisa semântica](semantic-search-overview.md)