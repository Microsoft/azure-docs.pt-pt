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
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152674"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples em Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure implementa duas línguas de consulta baseadas em Lucene: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [Lucene Query Parser.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) Na Pesquisa Cognitiva Azure, a simples sintaxe de consulta exclui as opções fuzzy/slop.  

> [!NOTE]
> A simples sintaxe de consulta é usada para expressões de consulta passadas no parâmetro de pesquisa da API de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) de **Pesquisa,** não confundir com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o parâmetro [$filter](search-filters.md) daquela API. Estas diferentes sintaxes têm as suas próprias regras para construir consultas, fugir de cordas, e assim por diante.
>
> A Azure Cognitive Search fornece uma [sintaxe de consulta lucene completa](query-lucene-syntax.md) alternativa para consultas mais complexas no parâmetro de **pesquisa.** Para saber mais sobre a consulta de arquitetura e benefícios de cada sintaxe, veja como funciona a pesquisa completa de [texto em Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Como invocar simples parsing

A simples sintaxe é o padrão. A invocação só é necessária se estiver a repor a sintaxe de forma completa para simples. Para definir explicitamente a sintaxe, utilize o parâmetro de pesquisa `queryType`. Os valores válidos incluem `simple|full`, com `simple` como padrão, e `full` para lucene. 

## <a name="query-behavior-anomalies"></a>Anomalias de comportamento de consulta

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução de consultas. A Pesquisa Cognitiva Azure corresponderá a documentos que contenham qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto. 

Por mais simples que isto pareça, há um aspeto da execução de consulta na Pesquisa Cognitiva azure que *pode* produzir resultados inesperados, aumentando em vez de diminuir os resultados de pesquisa à medida que mais termos e operadores são adicionados à cadeia de entrada. Se esta expansão realmente ocorre depende da inclusão de um operador NOT, combinado com uma definição de parâmetro `searchMode` que determina como NÃO é interpretado em termos de comportamentos e ou ou ou. Dado o padrão, `searchMode=Any`, e um operador NÃO, a operação é calculada como uma ação OR, de modo que `"New York" NOT Seattle` retorna todas as cidades que não são Seattle.  

Normalmente, é mais provável que veja estes comportamentos em padrões de interação do utilizador para aplicações que pesquisam sobre conteúdo, onde os utilizadores são mais propensos a incluir um operador numa consulta, em oposição a sites de e-commerce que têm mais estruturas de navegação incorporadas. Para mais informações, consulte [NOT operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operadores booleanos (E, OU, NÃO) 

Você pode incorporar os operadores em uma corda de consulta para construir um conjunto rico de critérios contra os quais documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>E operador `+`

O e o operador é um sinal positivo. Por exemplo, `wifi+luxury` procurará documentos que contenham `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador `|`

O operador OR é uma barra vertical ou um caracteres de tubo. Por exemplo, `wifi | luxury` procurará documentos que contenham `wifi` ou `luxury` ou ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NÃO `-` do operador

O operador NOT é um sinal negativo. Por exemplo, `wifi –luxury` procurará documentos que tenham o termo `wifi` e/ou não tenham `luxury` (e/ou sejam controlados por `searchMode`).

> [!NOTE]  
>  A opção `searchMode` controla se um termo com o operador NOT é ANDed ou ORed com os outros termos na consulta na ausência de um operador de `+` ou `|`. Lembre-se de que `searchMode` pode ser definida para `any` (padrão) ou `all`. Se utilizar `any`, aumentará a recolha de consultas, incluindo mais resultados, e por padrão `-` serão interpretados como "OU NÃO". Por exemplo, `wifi -luxury` corresponderão a documentos que contenham o termo `wifi` ou aqueles que não contenham o termo `luxury`. Se utilizar `all`, aumentará a precisão das consultas, incluindo menos resultados, e por padrão – será interpretado como "AND NOT". Por exemplo, `wifi -luxury` corresponderão a documentos que contenham o termo `wifi` e não contenham o termo "luxo". Este é, sem dúvida, um comportamento mais intuitivo para o operador `-`. Por isso, deve considerar utilizar `searchMode=all` em vez de `searchMode=any` se pretender otimizar as pesquisas de precisão em vez de se lembrar, *e* os utilizadores usam frequentemente o operador de `-` em pesquisas.

## <a name="suffix-operator"></a>Operador de sufixo

O sufixo é um asterisco `*`. Por exemplo, `lux*` procurará documentos que tenham um termo que comece com `lux`, ignorando o caso.  

## <a name="phrase-search-operator"></a>Operador de pesquisa de frases

A frase operadora encerra uma frase em aspas `" "`. Por exemplo, embora `Roach Motel` (sem citações) procure documentos que contenham `Roach` e/ou `Motel` em qualquer lugar em qualquer ordem, `"Roach Motel"` (com aspas) apenas corresponderão a documentos que contenham toda essa frase em conjunto e nessa ordem (a análise de texto ainda se aplica).

## <a name="precedence-operator"></a>Operador de precedência

O operador de precedência encerra a cadeia em parênteses `( )`. Por exemplo, `motel+(wifi | luxury)` procurará documentos que contenham o termo motel e `wifi` ou `luxury` (ou ambos).  

## <a name="escaping-search-operators"></a>Operadores de pesquisa escapando  

 Para utilizar os símbolos acima como parte real do texto de pesquisa, devem ser escapados prefixando-os com um backslash. Por exemplo, `luxury\+hotel` resultará no termo `luxury+hotel`. Para tornar as coisas simples para os casos mais típicos, existem duas exceções a esta regra em que não é necessária uma fuga:  

- O operador do NOT `-` só precisa de ser escapado se for o primeiro personagem depois do espaço branco, não se estiver a meio de um termo. Por exemplo, `wi-fi` é um termo único; considerando que os GUIDs (como `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) são tratados como um único símbolo.
- O operador de sufixo `*` só precisa de ser escapado se for o último personagem antes do espaço branco, não se estiver a meio de um termo. Por exemplo, `wi*fi` é tratado como um único símbolo.

> [!NOTE]  
>  Embora escapar mantém os tokens juntos, a análise de texto pode dividi-las, dependendo do modo de análise. Consulte o [suporte &#40;linguístico&#41; Azure Cognitive Search REST API](index-add-language-analyzers.md) para obter detalhes.  

## <a name="see-also"></a>Veja também  

+ [Documentos &#40;de pesquisa Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
