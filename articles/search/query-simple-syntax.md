---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta simples usada para consultas completas de pesquisa de texto em Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516588"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples na Pesquisa Cognitiva Azure

A Azure Cognitive Search implementa duas línguas de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e [o Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). O simples parser é mais flexível e tentará interpretar um pedido mesmo que não seja perfeitamente composto. Devido a esta flexibilidade, é o padrão para consultas na Pesquisa Cognitiva Azure.

A sintaxe simples é utilizada para expressões de consulta passadas no **`search`** parâmetro de um pedido de Documentos de Busca [(REST API),](/rest/api/searchservice/search-documents) não devendo ser confundida com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para as [**`$filter`**](search-filters.md) expressões e [**`$orderby`**](search-query-odata-orderby.md) expressões no mesmo pedido. Os parâmetros OData têm diferentes sintaxe e regras para a construção de consultas, cordas de fuga, e assim por diante.

Embora o simples parser seja baseado na classe [Apache Lucene Simple Query Parser,](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a implementação em Cognitive Search exclui a pesquisa fuzzy. Se precisar de [uma pesquisa fuzzy](search-query-fuzzy.md), considere a sintaxe de [consulta completa de Lucene](query-lucene-syntax.md) alternativa.

## <a name="example-simple-syntax"></a>Exemplo (sintaxe simples)

Embora **`queryType`** esteja definido abaixo, é o padrão e pode ser omitido a menos que você esteja revertendo de um tipo alternativo. O exemplo a seguir é uma pesquisa sobre termos independentes, com a exigência de que todos os documentos correspondentes incluam "pool".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

O **`searchMode`** parâmetro é relevante neste exemplo. Sempre que os operadores booleanos estiverem em consulta, deve geralmente definir `searchMode=all` para garantir que todos *os* critérios são compatíveis. Caso contrário, pode utilizar o padrão que favorece a `searchMode=any` recuperação em vez da precisão.

Para outros exemplos, consulte [exemplos simples de sintaxe de consulta](search-query-simple-examples.md). Para obter mais informações sobre o pedido de consulta e os parâmetros, consulte [Documentos de Pesquisa (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Pesquisa de palavras-chave em termos e frases

As cordas passadas para o **`search`** parâmetro podem incluir termos ou frases em qualquer idioma suportada, operadores booleanos, operadores de precedência, caracteres wildcard ou prefixo para consultas "começa com" consultas, caracteres de fuga e caracteres codificadores de URL. O **`search`** parâmetro é opcional. Não especificado, pesquisar `search=*` `search=" "` (ou) devolve os 50 melhores documentos em ordem arbitrária (não classificada).

+ Uma *pesquisa de termo* é uma consulta de um ou mais termos, onde qualquer um dos termos são considerados compatíveis.

+ Uma *pesquisa de frases* é uma frase exata incluída em aspas `" "` . Por exemplo, enquanto ```Roach Motel``` (sem cotações) procuraria documentos que contenham ```Roach``` e/ou ```Motel``` em qualquer lugar em qualquer ordem, ```"Roach Motel"``` (com aspas) apenas corresponderá a documentos que contenham toda essa frase em conjunto e nessa ordem (a análise lexical ainda se aplica). 

  Dependendo do seu cliente de pesquisa, poderá ter de escapar às aspas numa pesquisa de frases. Por exemplo, num pedido do Postman, uma pesquisa de `"Roach Motel"` frases no corpo de pedido seria especificada como `"\"Roach Motel\""` .

Por padrão, todos os termos ou frases passados no **`search`** parâmetro são submetidos a uma análise lexical. Certifique-se de compreender o comportamento de tokenização do analisador que está a usar. Muitas vezes, quando os resultados da consulta são inesperados, a razão pode ser rastreada para como os termos são tokenizados no tempo de consulta.

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução de consultas. A Azure Cognitive Search corresponderá a documentos que contenham qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Por mais simples que isto pareça, existe um aspeto de execução de consulta na Pesquisa Cognitiva Azure que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à cadeia de entrada. Se esta expansão realmente ocorre depende da inclusão de um operador NÃO, combinado com uma definição de **`searchMode`** parâmetro que determina como NÃO é interpretado em termos de e ou ou ou comportamentos. Para obter mais informações, consulte o operador NÃO sob [os operadores Boolean.](#boolean-operators)

## <a name="boolean-operators"></a>Operadores booleanos

Pode incorporar os operadores Boolean numa cadeia de consulta para melhorar a precisão de uma correspondência. Na simples sintaxe, os operadores booleanos são baseados em caracteres. Os operadores de texto, como a palavra E, não são apoiados.

| Caráter | Exemplo | Utilização |
|----------- |--------|-------|
| `+` | `pool + ocean` | Uma operação e uma operação. Por exemplo, `pool + ocean` estipula que um documento deve conter ambos os termos.|
| `|` | `pool | ocean` | Uma operação OR encontra uma correspondência quando qualquer um dos termos é encontrado. No exemplo, o motor de consulta voltará a corresponder em documentos que contenham `pool` ou `ocean` ambos. Como o OR é o operador de conjunção padrão, também pode deixá-lo de fora, tal que `pool ocean` é o equivalente a  `pool | ocean` .|
| `-` | `pool – ocean` | Uma operação NÃO devolve correspondências em documentos que excluem o termo. <br/><br/>Para obter o comportamento esperado numa expressão NÃO, considere definir **`searchMode=all`** o pedido. Caso contrário, sob o padrão de **`searchMode=any`** , você receberá fósforos em , mais `pool` fósforos em todos os documentos que não contêm `ocean` - o que pode ser um monte de documentos. O **`searchMode`** parâmetro de um pedido de consulta controla se um termo com o operador NÃO é ANDed ou ORed com outros termos na consulta (assumindo que não existe nenhum `+` ou operador nos `|` outros termos). A utilização **`searchMode=all`** aumenta a precisão das consultas, incluindo menos resultados, e por defeito - será interpretada como "E NÃO". <br/><br/>Ao decidir sobre uma **`searchMode`** definição, considere os padrões de interação do utilizador para consultas em várias aplicações. Os utilizadores que procuram informações são mais propensos a incluir um operador numa consulta, ao contrário dos sites de e-commerce que têm mais estruturas de navegação incorporadas. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Consultas de prefixo

Para consultas de "começa" , adicione um operador de sufixo ( `*` ) como espaço reservado para o resto de um período. Uma consulta de prefixo deve começar com pelo menos um carácter alfanumérico antes de poder adicionar o operador de sufixo.

| Caráter | Exemplo | Utilização |
|----------- |--------|-------|
| `*` | `lingui*` corresponderá a "linguística" ou "linguini" | O asterisco `*` representa um ou mais caracteres de comprimento arbitrário, ignorando o caso.  |

Semelhante aos filtros, uma consulta de prefixo procura uma correspondência exata. Como tal, não há pontuação de relevância (todos os resultados recebem uma pontuação de pesquisa de 1.0). Esteja ciente de que as consultas de prefixo podem ser lentas, especialmente se o índice é grande e o prefixo consiste em um pequeno número de caracteres. Uma metodologia alternativa, como a tokenização edge n-gram, pode funcionar mais rapidamente.

A sintaxe simples suporta apenas a correspondência do prefixo. Para sufixo ou infixo correspondentes com o final ou a meio de um termo, utilize a [sintaxe lucene completa para pesquisa de wildcard](query-lucene-syntax.md#bkmk_wildcard).

## <a name="escaping-search-operators"></a>Operadores de busca em fuga  

Na sintaxe simples, os operadores de pesquisa incluem estes caracteres: `+ | " ( ) ' \`  

Se algum destes caracteres fizer parte de um símbolo no índice, escape-o prefixando-o com uma única pestana `\` () na consulta. Por exemplo, suponha que usou um analisador personalizado para toda a tokenização do termo, e o seu índice contém a cadeia "Luxury+Hotel". Para obter uma correspondência exata neste token, insira um personagem de fuga: `search=luxury\+hotel` .

Para tornar as coisas simples para os casos mais típicos, existem duas exceções a esta regra onde não é necessário escapar:  

+ O operador do NOT `-` só precisa de ser escapado se for o primeiro personagem depois de um espaço em branco. Se o `-` aparecer no meio (por exemplo, `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` em), pode saltar para escapar.

+ O operador de sufixo `*` só precisa de ser escapado se for o último personagem antes de um espaço em branco. Se o `*` aparecer no meio (por exemplo, `4*4=16` em), não é necessário escapar.

> [!NOTE]  
> Por padrão, o analisador padrão irá apagar e quebrar palavras sobre hífens, espaços brancos, ampersands e outros caracteres durante [a análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se necessitar de caracteres especiais para permanecer na cadeia de consulta, pode precisar de um analisador que os preserve no índice. Algumas escolhas incluem [analisadores de linguagem](index-add-language-analyzers.md)natural da Microsoft , que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para mais informações, consulte [termos, padrões e caracteres especiais.](search-query-partial-matching.md)

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres inseguros e reservados em URLs

Certifique-se de que todos os caracteres inseguros e reservados estão codificados num URL. Por exemplo, '#' é um personagem inseguro porque é um identificador de fragmento/âncora num URL. O carácter deve ser codificado `%23` se for utilizado num URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva Azure. Para obter mais informações, consulte [RFC1738: Localizadores de Recursos Uniformes (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Personagens inseguros ``" ` < > # % { } | \ ^ ~ [ ]`` são. Os caracteres reservados `; / ? : @ = + &` são.

## <a name="special-characters"></a>Carateres especiais

Em algumas circunstâncias, você pode querer procurar um personagem especial, como um emoji '❤' ou o sinal '€'. Nesses casos, certifique-se de que o analisador que utiliza não filtra esses caracteres. O analisador padrão contorna muitos caracteres especiais, excluindo-os do seu índice.

Os analisadores que irão tokenizar caracteres especiais incluem o analisador "whitespace", que leva em consideração quaisquer sequências de caracteres separadas por espaços brancos como símbolos (de modo que a corda "❤" seria considerada um símbolo). Além disso, um analisador de idiomas como o analisador de inglês da Microsoft ("en.microsoft"), levaria a cadeia "€" como um símbolo. Pode [testar um analisador](/rest/api/searchservice/test-analyzer) para ver que fichas gera para uma determinada consulta.

Ao utilizar caracteres Unicode, certifique-se de que os símbolos são corretamente escapados no url de consulta (por exemplo, para "❤" utilizaria a sequência de `%E2%9D%A4+` fuga). O carteiro faz esta tradução automaticamente.  

## <a name="precedence-grouping"></a>Precedência (agrupamento)

Você pode usar parênteses para criar subqueries, incluindo operadores dentro da declaração parêntesis. Por exemplo, `motel+(wifi|luxury)` procurará documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

## <a name="query-size-limits"></a>Limites de tamanho de consulta

Se a sua aplicação gerar consultas de pesquisa programáticamente, recomendamos que a desenhe de forma a não gerar consultas de tamanho ilimitado. 

+ Para GET, o comprimento do URL não pode exceder 8 KB.

+ Para POST (e qualquer outro pedido), onde o corpo do pedido inclui `search` e outros parâmetros como `filter` `orderby` e, o tamanho máximo é de 16 MB, onde o número máximo de cláusulas em `search` (expressões separadas por E, OR, e assim por diante) é de 1024. Existe também um limite de aproximadamente 32 KB sobre o tamanho de qualquer termo individual numa consulta. Para mais informações, consulte [os limites de pedido da API.](search-limits-quotas-capacity.md#api-request-limits)

## <a name="next-steps"></a>Passos seguintes

Se você estiver construindo consultas programáticas, reveja [a pesquisa completa de texto na Pesquisa Cognitiva Azure](search-lucene-query-architecture.md) para entender as fases do processamento de consultas e as implicações da análise de texto.

Também pode rever os seguintes artigos para saber mais sobre a construção de consultas:

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa de Lucene](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents) (Pesquisar Documentos com a API REST)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Filtrar e selecionar sintaxe de expressão (OData)](query-odata-filter-orderby-syntax.md)