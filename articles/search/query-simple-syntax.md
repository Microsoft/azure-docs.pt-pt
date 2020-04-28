---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a simples sintaxe de consulta usada para consultas completas de pesquisa de texto em Pesquisa Cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194946"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure implementa duas línguas de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [Lucene Query Parser.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)

O simples parser é mais flexível e tentará interpretar um pedido mesmo que não seja perfeitamente composto. Devido a esta flexibilidade, é o padrão para consultas na Pesquisa Cognitiva Azure. 

A simples sintaxe é utilizada para `search` as expressões de consulta passadas no parâmetro de um pedido de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)de Busca , para não confundir com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o parâmetro [de expressões $filter](search-filters.md) da mesma API de Documentos de Busca. Os `search` `$filter` parâmetros e parâmetros têm sintaxe diferente, com as suas próprias regras para construir consultas, escapar de cordas, e assim por diante.

Embora o simples parser seja baseado na classe [Apache Lucene Simple Query Parser,](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a implementação em Azure Cognitive Search exclui a pesquisa fuzzy. Se precisar de [pesquisa duvidosa](search-query-fuzzy.md) ou outros formulários de consulta avançada, considere a [sintaxe de consulta lucene alternativa completa.](query-lucene-syntax.md)

## <a name="invoke-simple-parsing"></a>Invocar simples parsing

A simples sintaxe é o padrão. A invocação só é necessária se estiver a repor a sintaxe de forma completa para simples. Para definir explicitamente a sintaxe, utilize o parâmetro de `queryType` pesquisa. Os valores válidos incluem `queryType=simple` ou, `queryType=full` `simple` onde é o padrão, e `full` invoca o parser de consulta [lucene completo](query-lucene-syntax.md) para consultas mais avançadas. 

## <a name="syntax-fundamentals"></a>Fundamentos da sintaxe

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução de consultas. A Pesquisa Cognitiva Azure corresponderá a documentos que contenham qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Por mais simples que isto pareça, há um aspeto da execução de consulta na Pesquisa Cognitiva azure que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à cadeia de entrada. Se esta expansão realmente ocorre depende da inclusão de um operador NOT, combinado com uma definição de parâmetro de modo de **busca** que determina como NÃO é interpretado em termos de comportamentos e ou ou ou. Para mais informações, consulte [NOT operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

Pode utilizar parênteses para criar subqueries, incluindo operadores dentro da declaração parêntestica. Por exemplo, `motel+(wifi|luxury)` procurará documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas traça o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi|pool))` procura no campo "hotelAmenities" para "ginásio" e "wifi", ou "ginásio" e "piscina".  

### <a name="escaping-search-operators"></a>Operadores de pesquisa escapando  

Na simples sintaxe, os operadores de pesquisa incluem estes caracteres:`+ | " ( ) ' \`  

Se algum destes caracteres fizer parte de um símbolo no índice, escape-o`\`preserndo-o com um único backslash ( ) na consulta. Por exemplo, suponha que usou um analisador personalizado para tokenization de todo o termo, e o seu índice contém a corda "Luxury+Hotel". Para obter uma correspondência exata neste símbolo, `search=luxury\+hotel`insira um personagem de fuga: . 

Para tornar as coisas simples para os casos mais típicos, existem duas exceções a esta regra em que não é necessária escapar:  

+ O operador `-` NÃO só precisa de ser escapado se for o primeiro personagem depois de um espaço branco. Se `-` aparecer no meio (por exemplo, dentro `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`), pode saltar a fuga.

+ O sufixo `*` só precisa de ser escapado se for o último personagem antes de um espaço branco. Se `*` o que aparece no meio `4*4=16`(por exemplo, em ), não é necessária fuga.

> [!NOTE]  
> Por padrão, o analisador padrão eliminará e quebrará palavras sobre hífenes, espaço branco, ampersands e outros caracteres durante a [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se precisar de caracteres especiais para permanecer na corda de consulta, pode precisar de um analisador que os preserve no índice. Algumas escolhas incluem [analisadores](index-add-language-analyzers.md)de linguagem natural da Microsoft, que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para mais informações, consulte [termos parciais, padrões e caracteres especiais.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificar caracteres inseguros e reservados em URLs

Certifique-se de que todos os caracteres inseguros e reservados estão codificados num URL. Por exemplo, '#' é um personagem inseguro porque é um identificador de fragmento/âncora num URL. O caracteres deve ser `%23` codificado se for utilizado num URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva Azure. Consulte [o RFC1738: Localizadores de recursos uniformes (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Personagens ``" ` < > # % { } | \ ^ ~ [ ]``inseguros são. Os caracteres `; / ? : @ = + &`reservados são.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limites de tamanho de consulta

 Há um limite para o tamanho de consultas que pode enviar para a Pesquisa Cognitiva Azure. Especificamente, pode ter no máximo 1024 cláusulas (expressões separadas por E, OR, e assim por diante). Há também um limite de aproximadamente 32 KB sobre o tamanho de qualquer termo individual numa consulta. Se a sua aplicação gerar consultas de pesquisa programáticamente, recomendamos que a desenhá-lo de forma a não gerar consultas de tamanho ilimitado.  

## <a name="boolean-search"></a>Pesquisa booleana

Você pode incorporar operadores boolean (E, OU, NÃO) em uma corda de consulta para construir um conjunto rico de critérios contra os quais documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>E operador`+`

O e o operador é um sinal positivo. Por exemplo, `wifi + luxury` procurará documentos que contenham ambos `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador ou`|`

O operador OR é uma barra vertical ou um caracteres de tubo. Por exemplo, `wifi | luxury` procurará documentos `luxury` que contenham ou `wifi` ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NÃO operador`-`

O operador NOT é um sinal negativo. Por exemplo, `wifi –luxury` procurará documentos `wifi` que tenham o `luxury`termo e/ou não tenham .

O parâmetro modo de **pesquisa** num pedido de consulta controla se um termo com o operador NOT é ANDed ou ORed com outros termos na consulta (assumindo que não existe nenhum `+` ou `|` operador nos outros termos). Os valores válidos incluem `any` ou `all`.

`searchMode=any`aumenta a recolha de consultas, incluindo mais `-` resultados, e por padrão será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` combinará documentos `wifi` que contenham o termo `luxury`ou os que não contenham o termo .

`searchMode=all`aumenta a precisão das consultas, incluindo menos resultados, e por padrão - será interpretado como "AND NOT". Por exemplo, `wifi -luxury` combinará documentos `wifi` que contenham o termo e não contenham o termo "luxo". Este é, sem dúvida, `-` um comportamento mais intuitivo para o operador. Por isso, deve `searchMode=all` considerar `searchMode=any` a utilização em vez de se pretender otimizar as pesquisas `-` de precisão em vez de se recordar, *e* os utilizadores usam frequentemente o operador nas pesquisas.

Ao decidir sobre uma definição de modo de **pesquisa,** considere os padrões de interação do utilizador para consultas em várias aplicações. Os utilizadores que procuram informações são mais propensos a incluir um operador numa consulta, em oposição a sites de e-commerce que têm mais estruturas de navegação incorporadas.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Pesquisa prefixo

O sufixo é um `*`asterisco. Por exemplo, `lingui*` vai encontrar "linguini linguini" ou "linguini", ignorando o caso. 

Semelhante aos filtros, uma consulta de prefixo procura uma correspondência exata. Como tal, não há pontuação de relevância (todos os resultados recebem uma pontuação de pesquisa de 1.0). As consultas de prefixo podem ser lentas, especialmente se o índice for grande e o prefixo consistir num pequeno número de caracteres. 

Se quiser executar uma consulta de sufixo, combinando na última parte da corda, use uma [pesquisa de wildcard](query-lucene-syntax.md#bkmk_wildcard) e a sintaxe lucene completa.

## <a name="phrase-search-"></a>Pesquisa de frases`"`

Uma pesquisa de prazo é uma consulta para um ou mais termos, onde qualquer um dos termos são considerados compatíveis. Uma procura de fraseé uma frase `" "`exata em anexo em aspas. Por exemplo, `Roach Motel` enquanto (sem citações) procuraria `Motel` documentos que `"Roach Motel"` contenham `Roach` e/ou em qualquer lugar em qualquer ordem, (com citações) apenas combinará documentos que contenham toda esta frase em conjunto e nessa ordem (a análise de texto ainda se aplica).

## <a name="see-also"></a>Consulte também  

+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa lucene](search-query-lucene-examples.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Pesquisar Documentos com a API REST)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
