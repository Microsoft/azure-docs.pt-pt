---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a simples sintaxe de consulta usada para consultas completas de pesquisa de texto em Pesquisa Cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258869"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure implementa duas línguas de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [Lucene Query Parser.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 

Na Pesquisa Cognitiva Azure, a simples sintaxe de consulta exclui operações de pesquisa difusas. Em vez disso, use a sintaxe lucene completa para [pesquisa fuzzy](search-query-fuzzy.md).

> [!NOTE]
> A simples sintaxe de consulta é usada para expressões de consulta passadas no parâmetro de pesquisa da API de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) de **Pesquisa,** não confundir com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o parâmetro [$filter](search-filters.md) daquela API. Estas diferentes sintaxes têm as suas próprias regras para construir consultas, fugir de cordas, e assim por diante.
>
> A Azure Cognitive Search fornece uma [sintaxe de consulta lucene completa](query-lucene-syntax.md) alternativa para consultas mais complexas no parâmetro de **pesquisa.** Para saber mais sobre a consulta de arquitetura e benefícios de cada sintaxe, veja como funciona a pesquisa completa de [texto em Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="invoke-simple-parsing"></a>Invocar simples parsing

A simples sintaxe é o padrão. A invocação só é necessária se estiver a repor a sintaxe de forma completa para simples. Para definir explicitamente a sintaxe, utilize o parâmetro de `queryType` pesquisa. Os valores válidos incluem `queryType=simple` ou, `queryType=full` `simple` onde é o padrão, e `full` invoca o parser de consulta [lucene completo](query-lucene-syntax.md) para consultas mais avançadas. 

## <a name="syntax-fundamentals"></a>Fundamentos da sintaxe

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução de consultas. A Pesquisa Cognitiva Azure corresponderá a documentos que contenham qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Por mais simples que isto pareça, há um aspeto da execução de consulta na Pesquisa Cognitiva azure que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à cadeia de entrada. Se esta expansão realmente ocorre depende da inclusão de um operador NOT, combinado com uma definição de parâmetro de modo de **busca** que determina como NÃO é interpretado em termos de comportamentos e ou ou ou. Para mais informações, consulte [NOT operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Operadores de precedência (agrupamento)

Pode utilizar parênteses para criar subqueries, incluindo operadores dentro da declaração parêntestica. Por exemplo, `motel+(wifi||luxury)` procurará documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas traça o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` procura no campo "hotelAmenities" para "ginásio" e "wifi", ou "ginásio" e "piscina".  

### <a name="escaping-search-operators"></a>Operadores de pesquisa escapando  

Para utilizar qualquer um dos operadores de pesquisa como parte do texto de pesquisa,`\`escape ao personagem preserndo-o com um único backslash ( ). Por exemplo, para uma `https://`pesquisa `://` de wildcard em , onde `search=https\:\/\/*`faz parte da corda de consulta, você especificaria . Da mesma forma, um padrão de `\+1 \(800\) 642\-7676`número de telefone escapado pode parecer este .

Os caracteres especiais que requerem a fuga incluem:`- * ? \ /`  

Para tornar as coisas simples para os casos mais típicos, existem duas exceções a esta regra em que não é necessária uma fuga:  

+ O operador `-` NÃO só precisa de ser escapado se for o primeiro personagem depois do espaço branco, não se estiver a meio de um termo. Por exemplo, o seguinte GUID é `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`válido sem o carácter de fuga: .

+ O operador `*` de sufixo só precisa de ser escapado se for o último personagem antes do espaço branco, não se estiver a meio de um termo. Por exemplo, `4*4=16` não requer um corte nas costas.

> [!NOTE]  
> Embora escapar mantém os tokens juntos, [a análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante a indexação pode desmontá-las. Por exemplo, o analisador lucene padrão irá apagar e quebrar palavras sobre hífens, whitespace e outros caracteres. Se precisar de caracteres especiais na corda de consulta, pode precisar de um analisador que os preserve no índice. Algumas escolhas incluem [analisadores](index-add-language-analyzers.md)de linguagem natural da Microsoft, que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para mais informações, consulte [termos parciais, padrões e caracteres especiais.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificar caracteres inseguros e reservados em URLs

Certifique-se de que todos os caracteres inseguros e reservados estão codificados num URL. Por exemplo, '#' é um personagem inseguro porque é um identificador de fragmento/âncora num URL. O caracteres deve ser `%23` codificado se for utilizado num URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva Azure. Consulte [o RFC1738: Localizadores de recursos uniformes (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

Personagens ``" ` < > # % { } | \ ^ ~ [ ]``inseguros são. Os caracteres `; / ? : @ = + &`reservados são.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limites de tamanho de consulta

 Há um limite para o tamanho de consultas que pode enviar para a Pesquisa Cognitiva Azure. Especificamente, pode ter no máximo 1024 cláusulas (expressões separadas por E, OR, e assim por diante). Há também um limite de aproximadamente 32 KB sobre o tamanho de qualquer termo individual numa consulta. Se a sua aplicação gerar consultas de pesquisa programáticamente, recomendamos que a desenhá-lo de forma a não gerar consultas de tamanho ilimitado.  

## <a name="boolean-search"></a>Pesquisa booleana

Você pode incorporar operadores boolean (E, OU, NÃO) em uma corda de consulta para construir um conjunto rico de critérios contra os quais documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>E operador`+`

O e o operador é um sinal positivo. Por exemplo, `wifi+luxury` procurará documentos que contenham ambos `wifi` e `luxury`.

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

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa lucene](search-query-lucene-examples.md)
+ [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
