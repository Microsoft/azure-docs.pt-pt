---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a simples sintaxe de consulta usada para consultas completas de pesquisa de texto em Pesquisa Cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152674"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure implementa duas línguas de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [Lucene Query Parser.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) Na Pesquisa Cognitiva Azure, a simples sintaxe de consulta exclui as opções fuzzy/slop.  

> [!NOTE]
> A simples sintaxe de consulta é usada para expressões de consulta passadas no parâmetro de pesquisa da API de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) de **Pesquisa,** não confundir com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o parâmetro [$filter](search-filters.md) daquela API. Estas diferentes sintaxes têm as suas próprias regras para construir consultas, fugir de cordas, e assim por diante.
>
> A Azure Cognitive Search fornece uma [sintaxe de consulta lucene completa](query-lucene-syntax.md) alternativa para consultas mais complexas no parâmetro de **pesquisa.** Para saber mais sobre a consulta de arquitetura e benefícios de cada sintaxe, veja como funciona a pesquisa completa de [texto em Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Como invocar simples parsing

A simples sintaxe é o padrão. A invocação só é necessária se estiver a repor a sintaxe de forma completa para simples. Para definir explicitamente a sintaxe, utilize o parâmetro de `queryType` pesquisa. Os valores válidos incluem, `simple|full`como `simple` padrão, e `full` para Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalias de comportamento de consulta

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução de consultas. A Pesquisa Cognitiva Azure corresponderá a documentos que contenham qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto. 

Por mais simples que isto pareça, há um aspeto da execução de consulta na Pesquisa Cognitiva azure que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à cadeia de entrada. Se esta expansão realmente ocorre depende da inclusão de um `searchMode` operador NOT, combinado com uma definição de parâmetro que determina como NÃO é interpretado em termos de comportamentos E ou OU. Dado o `searchMode=Any`padrão, e um operador NÃO, a operação é `"New York" NOT Seattle` calculada como uma ação OR, de tal forma que devolve todas as cidades que não são Seattle.  

Normalmente, é mais provável que veja estes comportamentos em padrões de interação do utilizador para aplicações que pesquisam sobre conteúdo, onde os utilizadores são mais propensos a incluir um operador numa consulta, em oposição a sites de e-commerce que têm mais estruturas de navegação incorporadas. Para mais informações, consulte [NOT operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operadores booleanos (E, OU, NÃO) 

Você pode incorporar os operadores em uma corda de consulta para construir um conjunto rico de critérios contra os quais documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>E operador`+`

O e o operador é um sinal positivo. Por exemplo, `wifi+luxury` procurará documentos que contenham ambos `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador ou`|`

O operador OR é uma barra vertical ou um caracteres de tubo. Por exemplo, `wifi | luxury` procurará documentos `luxury` que contenham ou `wifi` ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NÃO operador`-`

O operador NOT é um sinal negativo. Por `wifi –luxury` exemplo, procurará documentos `wifi` que tenham o `luxury` termo e/ou não `searchMode`tenham (e/ou sejam controlados por).

> [!NOTE]  
>  A `searchMode` opção controla se um termo com o operador NOT é ANDed ou ORed com os outros termos na consulta na ausência de um `+` ou `|` operador. Lembre-se que `searchMode` pode `any` ser definido `all`para qualquer (padrão) ou . Se utilizar, `any`aumentará a recolha de consultas, incluindo mais `-` resultados, e por padrão será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` combinará documentos `wifi` que contenham o termo `luxury`ou os que não contenham o termo . Se utilizar `all`, aumentará a precisão das consultas, incluindo menos resultados, e por padrão – será interpretado como "AND NOT". Por exemplo, `wifi -luxury` combinará documentos `wifi` que contenham o termo e não contenham o termo "luxo". Este é, sem dúvida, `-` um comportamento mais intuitivo para o operador. Por isso, deve `searchMode=all` considerar `searchMode=any` a utilização em vez de se pretender otimizar as pesquisas `-` de precisão em vez de se recordar, *e* os utilizadores usam frequentemente o operador nas pesquisas.

## <a name="suffix-operator"></a>Operador de sufixo

O sufixo é um `*`asterisco. Por exemplo, `lux*` procurará documentos que tenham `lux`um termo que comece com , ignorando o caso.  

## <a name="phrase-search-operator"></a>Operador de pesquisa de frases

A frase operadora encerra uma frase `" "`em aspas. Por exemplo, `Roach Motel` enquanto (sem citações) procuraria `Motel` documentos que `"Roach Motel"` contenham `Roach` e/ou em qualquer lugar em qualquer ordem, (com citações) apenas combinará documentos que contenham toda esta frase em conjunto e nessa ordem (a análise de texto ainda se aplica).

## <a name="precedence-operator"></a>Operador de precedência

O operador de precedência encerra a corda `( )`em parênteses . Por exemplo, `motel+(wifi | luxury)` procurará documentos que `wifi` contenham o termo motel e ou (ou `luxury` ambos).  

## <a name="escaping-search-operators"></a>Operadores de pesquisa escapando  

 Para utilizar os símbolos acima como parte real do texto de pesquisa, devem ser escapados prefixando-os com um backslash. Por exemplo, `luxury\+hotel` resultará `luxury+hotel`no termo . Para tornar as coisas simples para os casos mais típicos, existem duas exceções a esta regra em que não é necessária uma fuga:  

- O operador `-` NÃO só precisa de ser escapado se for o primeiro personagem depois do espaço branco, não se estiver a meio de um termo. Por exemplo, `wi-fi` é um termo único; considerando que os GUIDs (como) `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`são tratados como um único símbolo.
- O operador `*` de sufixo só precisa de ser escapado se for o último personagem antes do espaço branco, não se estiver a meio de um termo. Por exemplo, `wi*fi` é tratado como um único símbolo.

> [!NOTE]  
>  Embora escapar mantém os tokens juntos, a análise de texto pode dividi-las, dependendo do modo de análise. Consulte [o suporte linguístico &#40;Azure Cognitive Search REST API&#41;](index-add-language-analyzers.md) para obter detalhes.  

## <a name="see-also"></a>Consulte também  

+ [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
