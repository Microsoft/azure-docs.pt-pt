---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta simples usada para consultas completas de pesquisa de texto em Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923386"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples na Pesquisa Cognitiva Azure

A Azure Cognitive Search implementa duas línguas de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e [o Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

O simples parser é mais flexível e tentará interpretar um pedido mesmo que não seja perfeitamente composto. Devido a esta flexibilidade, é o padrão para consultas na Pesquisa Cognitiva Azure. 

A sintaxe simples é utilizada para expressões de consulta passadas no `search` parâmetro de um pedido de Documentos de [Busca,](/rest/api/searchservice/search-documents)não confundir com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o parâmetro de expressão $filter do mesmo API de [Documentos](search-filters.md) de Busca. Os `search` `$filter` parâmetros têm diferentes sintaxes, com as suas próprias regras para construir consultas, escapar de cordas, e assim por diante.

Embora o simples parser seja baseado na classe [Apache Lucene Simple Query Parser,](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a implementação em Azure Cognitive Search exclui a pesquisa difusa. Se precisar de [pesquisa duvidosa](search-query-fuzzy.md) ou outros formulários de consulta avançada, considere a sintaxe de consulta completa de [Lucene](query-lucene-syntax.md) alternativa.

## <a name="invoke-simple-parsing"></a>Invocar uma simples análise

Sintaxe simples é o padrão. A invocação só é necessária se estiver a repor a sintaxe de pleno para simples. Para definir explicitamente a sintaxe, utilize o `queryType` parâmetro de pesquisa. Valores válidos incluem `queryType=simple` ou , onde é o `queryType=full` `simple` padrão, e `full` invoca o [parser de consulta lucene completo](query-lucene-syntax.md) para consultas mais avançadas. 

## <a name="syntax-fundamentals"></a>Fundamentos da sintaxe

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução de consultas. A Azure Cognitive Search corresponderá a documentos que contenham qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Por mais simples que isto pareça, existe um aspeto de execução de consulta na Pesquisa Cognitiva Azure que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à cadeia de entrada. Se esta expansão realmente ocorre depende da inclusão de um operador NÃO, combinado com uma definição de parâmetro **searchMode** que determina como NÃO é interpretado em termos de e ou ou comportamentos. Para mais informações, consulte [NÃO O operador.](#not-operator)

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

Você pode usar parênteses para criar subqueries, incluindo operadores dentro da declaração parêntesis. Por exemplo, `motel+(wifi|luxury)` procurará documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas coloca o agrupamento num único campo. Por exemplo, `hotelAmenities:(gym+(wifi|pool))` procura no campo "hotelAmenities" para "ginásio" e "wifi", ou "ginásio" e "piscina".  

### <a name="escaping-search-operators"></a>Operadores de busca em fuga  

Na sintaxe simples, os operadores de pesquisa incluem estes caracteres: `+ | " ( ) ' \`  

Se algum destes caracteres fizer parte de um símbolo no índice, escape-o prefixando-o com uma única pestana `\` () na consulta. Por exemplo, suponha que usou um analisador personalizado para toda a tokenização do termo, e o seu índice contém a cadeia "Luxury+Hotel". Para obter uma correspondência exata neste token, insira um personagem de fuga:  `search=luxury\+hotel` . 

Para tornar as coisas simples para os casos mais típicos, existem duas exceções a esta regra onde não é necessário escapar:  

+ O operador do NOT `-` só precisa de ser escapado se for o primeiro personagem depois de um espaço em branco. Se o `-` aparecer no meio (por exemplo, `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` em), pode saltar para escapar.

+ O operador de sufixo `*` só precisa de ser escapado se for o último personagem antes de um espaço em branco. Se o `*` aparecer no meio (por exemplo, `4*4=16` em), não é necessário escapar.

> [!NOTE]  
> Por padrão, o analisador padrão irá apagar e quebrar palavras sobre hífens, espaços brancos, ampersands e outros caracteres durante [a análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se necessitar de caracteres especiais para permanecer na cadeia de consulta, pode precisar de um analisador que os preserve no índice. Algumas escolhas incluem [analisadores de linguagem](index-add-language-analyzers.md)natural da Microsoft , que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para mais informações, consulte [termos, padrões e caracteres especiais.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres inseguros e reservados em URLs

Certifique-se de que todos os caracteres inseguros e reservados estão codificados num URL. Por exemplo, '#' é um personagem inseguro porque é um identificador de fragmento/âncora num URL. O carácter deve ser codificado `%23` se for utilizado num URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva Azure. Consulte [RFC1738: Localizadores de recursos uniformes (URL)](https://www.ietf.org/rfc/rfc1738.txt) para mais detalhes.

Personagens inseguros ``" ` < > # % { } | \ ^ ~ [ ]`` são. Os caracteres reservados `; / ? : @ = + &` são.

### <a name="querying-for-special-characters"></a>Consulta para caracteres especiais

Em algumas circunstâncias, você pode querer procurar um personagem especial, como o emoji '❤' ou o sinal '€'. Nesses casos, certifique-se de que o analisador que utiliza não filtra esses caracteres.  O analisador padrão ignora muitos dos caracteres especiais para que não se tornem símbolos no seu índice.

Assim, o primeiro passo é certificar-se de que usa um analisador que irá considerar esses símbolos de elementos. Por exemplo, o analisador "whitespace" leva em consideração quaisquer sequências de caracteres separadas por espaços brancos como símbolos, de modo que a cadeia "❤" seria considerada um símbolo. Além disso, um analisador como o analisador microsoft english ("en.microsoft"), teria em consideração a cadeia "€" como um símbolo. Pode [testar um analisador](/rest/api/searchservice/test-analyzer) para ver que fichas gera para uma determinada consulta.

Ao utilizar caracteres Unicode, certifique-se de que os símbolos são corretamente escapados no url de consulta (por exemplo, para "❤" utilizaria a sequência de `%E2%9D%A4+` fuga). O carteiro faz esta tradução automaticamente.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Limites de tamanho de consulta

 Há um limite para o tamanho das consultas que pode enviar para a Pesquisa Cognitiva Azure. Especificamente, pode ter no máximo 1024 cláusulas (expressões separadas por E, OR, e assim por diante). Existe também um limite de aproximadamente 32 KB sobre o tamanho de qualquer termo individual numa consulta. Se a sua aplicação gerar consultas de pesquisa programáticamente, recomendamos que a desenhe de forma a não gerar consultas de tamanho ilimitado.  

## <a name="boolean-search"></a>Pesquisa booleana

Pode incorporar operadores Boolean (E, OU, NÃO) numa cadeia de consultas para construir um conjunto rico de critérios contra os quais são encontrados documentos correspondentes. 

### <a name="and-operator-"></a>E operador `+`

O operador e é um sinal de mais. Por exemplo, `wifi + luxury` procurará documentos que contenham ambos `wifi` e `luxury` .

### <a name="or-operator-"></a>Operador de OR `|`

O operador OR é uma barra vertical ou um carácter de tubo. Por exemplo, `wifi | luxury` procurará documentos que contenham `wifi` ou `luxury` ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NÃO operador `-`

O operador NÃO é um sinal de menos. Por exemplo, `wifi –luxury` procurará documentos que tenham o `wifi` termo e/ou não tenham `luxury` .

O parâmetro **searchMode** sobre um pedido de consulta controla se um termo com o operador NÃO é ANDed ou ORed com outros termos na consulta (assumindo que não existe nenhum `+` ou operador nos `|` outros termos). Valores válidos incluem `any` ou `all` .

`searchMode=any` aumenta a recolha de consultas através da inclusão de mais resultados, e por defeito `-` será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contenham o termo `wifi` ou os que não contenham o termo `luxury` .

`searchMode=all` aumenta a precisão das consultas, incluindo menos resultados, e por defeito - será interpretado como "E NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contenham o termo `wifi` e não contenham o termo "luxo". Este é indiscutivelmente um comportamento mais intuitivo para o `-` operador. Portanto, deve considerar a utilização `searchMode=all` em vez de se `searchMode=any` pretender otimizar as pesquisas de precisão em vez de se lembrar, *e* os seus utilizadores usam frequentemente o operador `-` nas pesquisas.

Ao decidir sobre uma **definição de searchMode,** considere os padrões de interação do utilizador para consultas em várias aplicações. Os utilizadores que procuram informações são mais propensos a incluir um operador numa consulta, ao contrário dos sites de e-commerce que têm mais estruturas de navegação incorporadas.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Correspondência de prefixo wildcard (*, ?)

Para consultas de "começa", adicione um operador de sufixo como espaço reservado para o resto de um período. Utilize um asterisco `*` para vários caracteres ou `?` para caracteres individuais. Por exemplo, `lingui*` corresponderá a "linguística" ou "linguini", ignorando o caso. 

Semelhante aos filtros, uma consulta de prefixo procura uma correspondência exata. Como tal, não há pontuação de relevância (todos os resultados recebem uma pontuação de pesquisa de 1.0). Esteja ciente de que as consultas de prefixo podem ser lentas, especialmente se o índice é grande e o prefixo consiste em um pequeno número de caracteres. Uma metodologia alternativa, como a tokenização edge n-gram, pode funcionar mais rapidamente.

Para outras variantes de consulta wildcard, tais como sufixo ou infixo correspondentes com o final ou meio de um termo, utilize a [sintaxe lucene completa para pesquisa de wildcard](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Pesquisa de frases `"`

Uma pesquisa de termo é uma consulta para um ou mais termos, onde qualquer um dos termos são considerados compatíveis. Uma pesquisa de frases é uma frase exata incluída em aspas `" "` . Por exemplo, enquanto `Roach Motel` (sem cotações) procuraria documentos que contenham `Roach` e/ou `Motel` em qualquer lugar em qualquer ordem, `"Roach Motel"` (com aspas) apenas corresponderá a documentos que contenham toda essa frase em conjunto e nessa ordem (a análise lexical ainda se aplica).

## <a name="see-also"></a>Consulte também  

+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa de Lucene](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents) (Pesquisar Documentos com a API REST)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md)