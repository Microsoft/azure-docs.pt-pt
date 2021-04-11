---
title: Indexação em várias línguas para consultas de pesquisa não inglesas
titleSuffix: Azure Cognitive Search
description: Crie um índice que suporte conteúdo multi-linguístico e, em seguida, crie consultas a esse conteúdo.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801609"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Como criar um índice para várias línguas na Pesquisa Cognitiva Azure

Um requisito chave numa aplicação de pesquisa multilíngue é a capacidade de pesquisar e recuperar resultados no próprio idioma do utilizador. Na Pesquisa Cognitiva Azure, uma maneira de satisfazer os requisitos linguísticos de uma aplicação multilíngue é criar campos dedicados para armazenar cordas numa língua específica, e, em seguida, limitar a pesquisa completa de texto a esses campos apenas no momento da consulta.

+ Nas definições de campo, estabeleça um analisador de linguagem que invoca as regras linguísticas da linguagem-alvo. Para ver a lista completa de analisadores suportados, consulte [os analisadores de linguagem Add](index-add-language-analyzers.md).

+ No pedido de consulta, defina os parâmetros para a procura completa de texto em campos específicos e, em seguida, apara os resultados de quaisquer campos que não forneçam conteúdo compatível com a experiência de pesquisa que pretende fornecer.

O sucesso desta técnica depende da integridade dos conteúdos de campo. A Azure Cognitive Search não traduz cordas nem executa a deteção de linguagem como parte da execução de consultas. Cabe-lhe a si certificar-se de que os campos contêm as cordas que se esperam.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para conteúdo em diferentes línguas

Na Pesquisa Cognitiva Azure, as consultas têm como alvo um único índice. Os desenvolvedores que querem fornecer cordas específicas da linguagem numa única experiência de pesquisa normalmente definem campos dedicados para armazenar os valores: um campo para cordas inglesas, um para francês, e assim por diante.

A propriedade "analisador" numa definição de campo é usada para definir o [analisador de idiomas](index-add-language-analyzers.md). Será utilizado tanto para a indexação como para a execução de consultas.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Construa e carregue um índice

Um passo intermédio (e talvez óbvio) é que é preciso [construir e preencher o índice](search-get-started-dotnet.md) antes de formular uma consulta. Mencionamos este passo aqui para a completude. Uma forma de determinar a disponibilidade do índice é verificando a lista de índices no [portal](https://portal.azure.com).

> [!TIP]
> A deteção de idiomas e a tradução de texto são suportadas durante a ingestão de dados através [do enriquecimento](cognitive-search-concept-intro.md) e [das competências da IA.](cognitive-search-working-with-skillsets.md) Se tiver uma fonte de dados Azure com conteúdo de idiomas mistos, pode experimentar as funcionalidades de deteção e tradução de idiomas utilizando o [assistente de dados De importação](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Conter os resultados da consulta e do corte

Os parâmetros da consulta são usados para limitar a pesquisa a campos específicos e, em seguida, aparar os resultados de quaisquer campos que não sejam úteis ao seu cenário. 

| Parâmetros | Objetivo |
|-----------|--------------|
| **searchFields** | Limite a pesquisa por texto na lista de campos nomeados. |
| **$select** | Apara a resposta para incluir apenas os campos especificados. Por predefinição, todos os campos recuperáveis são devolvidos. O **parâmetro $select** permite-lhe escolher quais os que deveresar. |

Tendo em conta o objetivo de restringir a procura a campos que contenham cordas francesas, você usaria **searchFields** para direcionar a consulta em campos que contêm cordas nessa língua.

Especificar o analisador num pedido de consulta não é necessário. Um analisador de idiomas na definição de campo será sempre utilizado durante o processamento de consultas. Para consultas que especifiquem vários campos invocando diferentes analisadores de linguagem, os termos ou frases serão processados independentemente pelos analisadores atribuídos para cada campo.

Por predefinição, uma pesquisa devolve todos os campos que são marcados como recuperáveis. Como tal, pode querer excluir campos que não estejam em conformidade com a experiência de pesquisa específica da linguagem que pretende proporcionar. Especificamente, se você limitou a pesquisa a um campo com cordas francesas, provavelmente quer excluir campos com cordas inglesas dos seus resultados. A utilização do parâmetro de consulta **$select** dá-lhe controlo sobre quais os campos que são devolvidos à aplicação de chamada.

#### <a name="example-in-rest"></a>Exemplo em REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Exemplo em C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos da linguagem

Por vezes, a linguagem do agente que emite uma consulta não é conhecida, caso em que a consulta pode ser emitida contra todos os campos simultaneamente. A preferência da IA pelos resultados numa determinada língua pode ser definida utilizando [perfis de pontuação](index-add-scoring-profiles.md). No exemplo abaixo, os jogos encontrados na descrição em inglês serão pontuados mais altos em relação aos jogos de outras línguas:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Em seguida, incluiria o perfil de pontuação no pedido de pesquisa:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Passos seguintes

+ [Analisadores de idiomas](index-add-language-analyzers.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)
+ [Visão geral do enriquecimento da IA](cognitive-search-concept-intro.md)
+ [Visão geral de skillsets](cognitive-search-working-with-skillsets.md)