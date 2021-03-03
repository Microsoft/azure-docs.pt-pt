---
title: Use a sintaxe de consulta lucene completa
titleSuffix: Azure Cognitive Search
description: Exemplos de consulta que demonstram a sintaxe de consulta lucene para pesquisa de fuzzy, pesquisa de proximidade, aumento de termo, pesquisa regular de expressão e pesquisas de wildcard em um índice de pesquisa cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693565"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Utilize a sintaxe de pesquisa lucene "completa" (consultas avançadas em Pesquisa Cognitiva Azure)

Ao construir consultas para Azure Cognitive Search, pode substituir o [parser de consulta simples](query-simple-syntax.md) padrão com o mais poderoso [parser de consulta lucene](query-lucene-syntax.md) para formular expressões de consulta especializadas e avançadas.

O Lucene parser suporta formatos de consulta complexos, tais como consultas de campo, pesquisa de fossa, pesquisa de sfix e sufixo wildcard, pesquisa de proximidade, aumento de termos e pesquisa regular de expressão. A potência adicional vem com requisitos adicionais de processamento, pelo que deverá esperar um tempo de execução um pouco mais longo. Neste artigo, pode passar por exemplos demonstrando operações de consulta com base em sintaxe completa.

> [!Note]
> Muitas das construções de consulta especializadas possibilitadas através da sintaxe de consulta lucene completa não são [analisadas por texto](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente se você espera stemming ou lematização. A análise lexical só é realizada em termos completos (consulta de termo ou consulta de frase). Os tipos de consulta com termos incompletos (consulta de prefixo, consulta wildcard, consulta regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta parcial é a redução. 
>

## <a name="hotels-sample-index"></a>Índice de amostra de hotéis

As seguintes consultas baseiam-se no índice de amostras de hotéis, que pode criar seguindo as instruções neste [arranque rápido.](search-get-started-portal.md)

As consultas de exemplo são articuladas utilizando os pedidos REST API e POST. Pode colar e executá-los no [Carteiro](search-get-started-rest.md) ou no [Código do Estúdio Visual com a extensão de Pesquisa Cognitiva.](search-get-started-vs-code.md)

Os cabeçalhos de pedido devem ter os seguintes valores:

| Chave | Valor |
|-----|-------|
| Content-Type | application/json|
| api-chave  | `<your-search-service-api-key>`, quer a consulta ou a chave de administração |

Os parâmetros URI devem incluir o seu ponto final do serviço de pesquisa com o nome de índice, coleções de docs, comando de pesquisa e versão API, semelhante ao seguinte exemplo:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

O organismo de pedido deve ser formado como JSON válido:

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Pesquisa" definida `*` para uma consulta não especificada, equivalente a uma busca nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que pode fazer, e mostra todos os campos recuperáveis no índice, com todos os valores.

+ "consultaType" definido para "full" invoca o parser de consulta lucene completo e é necessário para esta sintaxe.

+ É utilizado um conjunto "selecionado" para uma lista de campos delimitada por vírgulas para a composição dos resultados de pesquisa, incluindo apenas os campos que são úteis no contexto dos resultados da pesquisa.

+ "contagem" devolve o número de documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, a contagem será de todos os documentos do índice (50 no caso de hotéis-índice de amostra).

## <a name="example-1-fielded-search"></a>Exemplo 1: Pesquisa de campo

Indivíduo de âmbito de pesquisa em campo, incorporado expressões de pesquisa para um campo específico. Este exemplo procura nomes de hotéis com o termo "hotel" neles, mas não "motel". Pode especificar vários campos utilizando E. 

Quando utilizar esta sintaxe de consulta, pode omitir o parâmetro "searchFields" quando os campos que pretende consultar estão na própria expressão de pesquisa. Se incluir "searchFields" com pesquisa de campo, `fieldName:searchExpression` tem sempre precedência sobre "searchFields".

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

A resposta a esta consulta deve ser semelhante ao exemplo seguinte, filtrado em "Resort and Spa", retornando hotéis que incluem "hotel" ou "motel" no nome.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

A expressão de pesquisa pode ser um único termo ou uma frase, ou uma expressão mais complexa em parênteses, opcionalmente com operadores Boolean. Alguns exemplos incluem:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Certifique-se de colocar uma frase dentro das aspas se quiser que ambas as cordas sejam avaliadas como uma única entidade, como neste caso procurando dois locais distintos no campo Address/StateProvince. Dependendo do cliente, poderá ter de escapar às `\` aspas.

O campo especificado `fieldName:searchExpression` deve ser um campo pesmável. Consulte [o Índice de Criação (REST API)](/rest/api/searchservice/create-index) para obter mais informações sobre a forma como as definições de campo são atribuídas.

## <a name="example-2-fuzzy-search"></a>Exemplo 2: Pesquisa felpuda

A pesquisa fuzzy corresponde em termos semelhantes, incluindo palavras mal escritas. Para fazer uma pesquisa difusa, apedte o `~` símbolo de azulejos no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` voltaria azul, azul e cola.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

A resposta a esta consulta resolve-se a "concierge" nos documentos correspondentes, aparados para a brevidade:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

As frases não são suportadas diretamente, mas pode especificar uma correspondência difusa em cada termo de uma frase multi-partes, como `search=Tags:landy~ AND sevic~` .  Esta expressão de consulta encontra 15 correspondências em "serviço de lavandaria".

> [!Note]
> As consultas fuzzy não são [analisadas.](search-lucene-query-architecture.md#stage-2-lexical-analysis) Os tipos de consulta com termos incompletos (consulta de prefixo, consulta wildcard, consulta regex, consulta fuzzy) são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta parcial é o invólucro mais baixo.
>

## <a name="example-3-proximity-search"></a>Exemplo 3: Pesquisa de proximidade

A pesquisa de proximidade encontra termos que estão próximos uns dos outros em um documento. Insira um símbolo de azulejos "~" no final de uma frase seguida do número de palavras que criam o limite de proximidade.

Esta consulta procura os termos "hotel" e "aeroporto" dentro de 5 palavras um do outro num documento. As aspas escapam para `\"` preservar a frase:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

A resposta a esta consulta deve ser semelhante ao seguinte exemplo:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Exemplo 4: Aumento de prazos

O aumento de prazos refere-se à classificação de um documento mais elevado se contiver o termo aumentado, em relação a documentos que não contêm o termo. Para impulsionar um termo, use o caret, `^` símbolo com um fator de impulso (um número) no final do termo que você está procurando. O padrão do fator de impulso é 1, e embora tenha de ser positivo, pode ser inferior a 1 (por exemplo, 0.2). O aumento de prazos difere dos perfis de pontuação em que os perfis de pontuação impulsionam certos campos, em vez de termos específicos.

Nesta consulta "antes", procure "acesso à praia" e note que existem sete documentos que correspondem em um ou ambos os termos.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

Na verdade, há apenas um documento que corresponde ao "acesso", e como é o único jogo, a sua colocação é alta (segunda posição) mesmo que o documento esteja faltando o termo "praia".

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

Na consulta "depois", repita a procura, desta vez impulsionando os resultados com o termo "praia" ao longo do termo "acesso". Uma versão legível humana da consulta é `search=Description:beach^2 access` . Dependendo do seu cliente, poderá ter de expressar `^2` como `%5E2` .

Depois de impulsionar o termo "praia", a partida no Old Carrabelle Hotel desce para o sexto lugar.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Exemplo 5: Regex

Uma pesquisa regular de expressão encontra uma correspondência com base no conteúdo entre barras para a frente "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

A resposta a esta consulta deve ser semelhante ao seguinte exemplo:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> As consultas de Regex não são [analisadas.](./search-lucene-query-architecture.md#stage-2-lexical-analysis) A única transformação realizada em termos de consulta parcial é o invólucro mais baixo.
>

## <a name="example-6-wildcard-search"></a>Exemplo 6: Pesquisa wildcard

Pode utilizar sintaxe geralmente reconhecida para pesquisas de caracteres múltiplos `*` () ou simples `?` () de caracteres wildcard. Note que o parser de consulta Lucene suporta o uso destes símbolos com um único termo, e não uma frase.

Nesta consulta, procure nomes de hotéis que contenham o prefixo 'sc'. Não é possível utilizar um `*` ou símbolo como o primeiro `?` personagem de uma pesquisa.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

A resposta a esta consulta deve ser semelhante ao seguinte exemplo:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> As consultas wildcard não são [analisadas.](./search-lucene-query-architecture.md#stage-2-lexical-analysis) A única transformação realizada em termos de consulta parcial é o invólucro mais baixo.
>

## <a name="next-steps"></a>Passos seguintes

Tente especificar consultas em código. Os seguintes links explicam como configurar consultas de pesquisa usando os Azure SDKs.

+ [Consultar o seu índice utilizando o .NET SDK](search-get-started-dotnet.md)
+ [Consultar o seu índice usando o Python SDK](search-get-started-python.md)
+ [Consultar o seu índice utilizando o JavaScript SDK](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta Lucene completa](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)