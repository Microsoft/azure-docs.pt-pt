---
title: Criar uma consulta semântica
titleSuffix: Azure Cognitive Search
description: Desafie um tipo de consulta semântica para anexar os modelos de aprendizagem profunda ao processamento de consultas, inferindo a intenção e o contexto como parte do posto de pesquisa e relevância.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0af868f62f9bc62ee6b4b2a10d16f8eed632b6d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680365"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Criar uma consulta semântica na Pesquisa Cognitiva

> [!IMPORTANT]
> O tipo de consulta semântica está em pré-visualização pública, disponível através do portal REST API e Azure de pré-visualização. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Durante o lançamento inicial da pré-visualização, não há qualquer custo para a procura semântica. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

Neste artigo, aprenda a formular um pedido de pesquisa que usa ranking semântico, e produz legendas e respostas semânticas.

## <a name="prerequisites"></a>Pré-requisitos

+ Um serviço de pesquisa em um nível Standard (S1, S2, S3), localizado em uma dessas regiões: North Central US, West US, West US 2, East US 2, North Europe, West Europe. Se tiver um serviço S1 ou maior existente numa destas regiões, pode solicitar acesso sem ter de criar um novo serviço.

+ Acesso à pré-visualização semântica: [inscreva-se](https://aka.ms/SemanticSearchPreviewSignup)

+ Um índice de pesquisa existente, contendo conteúdo inglês

+ Um cliente de pesquisa para enviar consultas

  O cliente de pesquisa deve suportar pré-visualização REST APIs no pedido de consulta. Pode utilizar [o Carteiro](search-get-started-rest.md), [Código de Estúdio Visual](search-get-started-vs-code.md)ou código que modificou para fazer chamadas REST para as APIs de pré-visualização. Também pode utilizar [o Explorador de Pesquisa](search-explorer.md) no portal Azure para submeter uma consulta semântica.

+ Um pedido [de Documentos de Busca](/rest/api/searchservice/preview-api/search-documents) com a opção semântica e outros parâmetros descritos neste artigo.

## <a name="whats-a-semantic-query"></a>O que é uma consulta semântica?

Na Pesquisa Cognitiva, uma consulta é um pedido parametrizado que determina o processamento de consulta e a forma da resposta. Uma *consulta semântica* adiciona parâmetros que invocam o algoritmo semântico de reclasse que pode avaliar o contexto e significado de resultados correspondentes, e promover jogos mais relevantes para o topo.

O seguinte pedido é representativo de uma consulta semântica básica (sem respostas).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Tal como acontece com todas as consultas na Pesquisa Cognitiva, o pedido visa a recolha de documentos de um único índice. Além disso, uma consulta semântica sofre a mesma sequência de análise, análise e digitalização como uma consulta não semântica. A diferença reside na relevância que é calculada. Tal como definido nesta versão de pré-visualização, uma consulta semântica é aquela cujos *resultados* são re-processados usando algoritmos avançados, fornecendo uma forma de emergir os jogos considerados mais relevantes pelo ranker semântico, em vez das pontuações atribuídas pelo algoritmo de classificação de semelhança padrão. 

Apenas os 50 melhores jogos dos resultados iniciais podem ser classificados semânticamente, e todos incluem legendas na resposta. Opcionalmente, pode especificar um **`answer`** parâmetro sobre o pedido para extrair uma resposta potencial. Este modelo formula até cinco respostas potenciais para a consulta, que pode optar por renderizar no topo da página de pesquisa.

## <a name="query-using-rest-apis"></a>Consulta usando APIs REST

A especificação completa da API REST pode ser encontrada em [Documentos de Busca (pré-visualização DE REST)](/rest/api/searchservice/preview-api/search-documents).

As consultas semânticas destinam-se a perguntas abertas como "qual é a melhor planta para polinizadores" ou "como fritar um ovo". Se quiser que a resposta inclua respostas, pode adicionar um parâmetro opcional **`answer`** sobre o pedido.

O exemplo a seguir utiliza o índice de amostras de hotéis para criar um pedido de consulta semântica com respostas e legendas semânticas:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>Formular o pedido

1. Desaperte "consultaType" a "semântica" e "queryLanguage" para "en-us". Ambos os parâmetros são necessários.

   A queryLanguage deve ser consistente com quaisquer [analisadores de linguagem](index-add-language-analyzers.md) atribuídos às definições de campo no esquema de índice. Se a queryLanguage for "en-us", então qualquer analisador de idiomas também deve ser uma variante inglesa ("en.microsoft" ou "en.lucene"). Quaisquer analisadores linguísticos agnósticos, tais como palavra-chave ou simples, não têm qualquer conflito com valores de dúvida.

   Num pedido de consulta, se também estiver a utilizar [a correção ortográfica,](speller-how-to-add.md)a consultalanguage que definiu aplica-se igualmente a soletradores, respostas e legendas. Não há substituição de peças individuais. 

   Embora o conteúdo de um índice de pesquisa possa ser composto em vários idiomas, a entrada de consulta é mais provável em um. O motor de busca não verifica a compatibilidade da queryLanguage, do analisador de línguas e do idioma em que o conteúdo é composto, por isso certifique-se de que as consultas de âmbito em conformidade para evitar produzir resultados incorretos.

1. Opcional, mas recomendado, desconte "searchFields".

   Numa consulta semântica, a ordem dos campos em "searchFields" reflete a prioridade ou a importância relativa do campo em rankings semânticos. Apenas serão utilizados campos de cordas de nível superior (autónomos ou numa coleção). Como searchFields tem outros comportamentos em consultas de Lucene simples e completas (onde não há ordem de prioridade implícita), quaisquer campos e subcampos não-cordas não resultarão em erro, mas também não serão usados no ranking semântico.

   Ao especificar searchFields, siga estas diretrizes:

   + Campos concisos, como o HotelName ou um título, devem preceder campos verbosos como a Descrição.

   + Se o seu índice tiver um campo URL que seja textual (legível humano como `www.domain.com/name-of-the-document-and-other-details` e não máquina focada `www.domain.com/?id=23463&param=eis` como), coloque-o em segundo lugar na lista (coloque-o em primeiro lugar se não houver um campo de título conciso).

   + Se houver apenas um campo especificado, então será considerado como um campo descritivo para classificação semântica de documentos.  

   + Se não houver campos especificados, todos os campos pescáveis serão considerados para classificação semântica de documentos. No entanto, isto não é recomendado, uma vez que pode não produzir os resultados mais ideais do seu índice de pesquisa.

1. Remova as cláusulas "orderBy", se existirem num pedido existente. A pontuação semântica é usada para encomendar resultados, e se incluir lógica de classificação explícita, um erro HTTP 400 é devolvido.

1. Opcionalmente, adicione "respostas" definidas como "extrativas" e especifique o número de respostas se quiser mais de 1.

1. Opcionalmente, personalize o estilo de destaque aplicado às legendas. As legendas aplicam-se a formatação de destaque sobre as passagens-chave no documento que resumem a resposta. A predefinição é `<em>`. Se quiser especificar o tipo de formatação (por exemplo, fundo amarelo), pode definir o destaquePreTag e realçar oPostTag.

1. Especifique quaisquer outros parâmetros que pretenda no pedido. Parâmetros como [soletrador](speller-how-to-add.md), [selecione](search-query-odata-select.md), e contam melhorar a qualidade do pedido e a legibilidade da resposta.

### <a name="review-the-response"></a>Reveja a resposta

A resposta para a consulta acima devolve o seguinte jogo como a escolha principal. As legendas são devolvidas automaticamente, com texto simples e versões realçadas. Para obter mais informações sobre respostas semânticas, consulte [o ranking semântico e as respostas.](semantic-how-to-query-response.md)

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Parâmetros utilizados numa consulta semântica

A tabela seguinte resume os parâmetros de consulta utilizados numa consulta semântica para que possa vê-los holisticamente. Para obter uma lista de todos os parâmetros, consulte [Documentos de Pesquisa (pré-visualização REST)](/rest/api/searchservice/preview-api/search-documents)

| Parâmetro | Tipo | Descrição |
|-----------|-------|-------------|
| consultaType | String | Valores válidos incluem simples, completo e semântico. É necessário um valor de "semântica" para consultas semânticas. |
| queryLanguage | String | Necessário para consultas semânticas. Atualmente, apenas "en-us" é implementado. |
| searchFields | String | Uma lista delimitada por vírgulas de campos pesjáveis. Opcional, mas recomendado. Especifica os campos sobre os quais ocorre uma classificação semântica. </br></br>Em contraste com os tipos de consulta simples e completa, a ordem em que os campos estão listados determina a precedência.|
| respostas |String | Campo opcional para especificar se as respostas semânticas estão incluídas no resultado. Atualmente, apenas é implementada a "extração". As respostas podem ser configuradas para devolver um máximo de cinco. Este exemplo "extrativo|contagem3'' mostra uma contagem de três respostas. A predefinição é 1.|

## <a name="query-with-search-explorer"></a>Consultar com o Explorador de procura

A seguinte consulta visa o índice de amostra de Hotéis incorporados, utilizando a versão API 2020-06-30-Preview, e corre no Explorador de Pesquisa. A `$select` cláusula limita os resultados a apenas alguns campos, facilitando a digitalização no verboso JSON no explorador de pesquisa.

### <a name="with-querytypesemantic"></a>Com consultaType=semântica

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

Os primeiros resultados são os seguintes.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>Com consultaType (padrão)

Para comparação, executar a mesma consulta que acima, removendo `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Note que não há `"@search.rerankerScore"` nenhum nestes resultados, e que diferentes hotéis aparecem nas três primeiras posições.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Passos seguintes

Lembre-se que o ranking semântico e as respostas são construídas sobre um conjunto de resultados iniciais. Qualquer lógica que melhore a qualidade dos resultados iniciais irá levar para a procura semântica. Como passo seguinte, reveja as funcionalidades que contribuem para os resultados iniciais, incluindo os analisadores que afetam a forma como as cordas são tokenizadas, perfis de pontuação que podem afinar resultados, e o algoritmo de relevância padrão.

+ [Analisadores para processamento de texto](search-analyzers.md)
+ [Semelhança e pontuação na Pesquisa Cognitiva](index-similarity-and-scoring.md)
+ [Adicionar perfis de classificação](index-add-scoring-profiles.md)
+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Adicionar verificação ortográfica aos termos de consulta](speller-how-to-add.md)
