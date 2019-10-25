---
title: Sintaxe de consultas simples
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta simples usada para consultas de pesquisa de texto completo no Azure Pesquisa Cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: fb98be9975de38ec9f65e723e078a1db8755b4ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792560"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva implementa duas linguagens de consulta baseadas em Lucene: [analisador de consulta simples](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [analisador de consulta Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). No Azure Pesquisa Cognitiva, a sintaxe de consulta simples exclui as opções difusas/inclinada.  

> [!NOTE]  
>  O Azure Pesquisa Cognitiva fornece uma [sintaxe de consulta Lucene](query-lucene-syntax.md) alternativa para consultas mais complexas. Para saber mais sobre a arquitetura de análise de consulta e os benefícios de cada sintaxe, consulte [como a pesquisa de texto completo funciona no Azure pesquisa cognitiva](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Como invocar a análise simples

A sintaxe simples é o padrão. A invocação só será necessária se você estiver redefinindo a sintaxe de completo para simples. Para definir explicitamente a sintaxe, use o parâmetro de pesquisa `queryType`. Os valores válidos incluem `simple|full`, com `simple` como o padrão e `full` para Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalias de comportamento de consulta

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. O Azure Pesquisa Cognitiva corresponderá a documentos que contêm qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto. 

Tão simples quanto esse som, há um aspecto da execução da consulta no Azure Pesquisa Cognitiva que *pode* produzir resultados inesperados, aumentando em vez de reduzir os resultados da pesquisa conforme mais termos e operadores são adicionados à cadeia de caracteres de entrada. Se essa expansão realmente ocorrer depende da inclusão de um operador NOT, combinada com uma configuração de parâmetro `searchMode` que determina como não é interpretado em termos de e ou ou comportamentos. Dado o padrão, `searchMode=Any`e um operador NOT, a operação é computada como uma ação ou, de modo que `"New York" NOT Seattle` retorna todas as cidades que não são Seattle.  

Normalmente, é mais provável que você veja esses comportamentos nos padrões de interação do usuário para aplicativos que pesquisam o conteúdo, em que os usuários têm mais probabilidade de incluir um operador em uma consulta, em oposição aos sites de comércio eletrônico que têm estruturas de navegação mais internas. Para obter mais informações, consulte [not Operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operadores boolianos (e, ou, não) 

Você pode inserir operadores em uma cadeia de caracteres de consulta para criar um conjunto avançado de critérios em relação aos quais os documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>Operador AND e `+`

O operador AND é um sinal de adição. Por exemplo, `wifi+luxury` irá procurar documentos que contenham `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador OR `|`

O operador OR é um caractere de barra vertical ou de pipe. Por exemplo, `wifi | luxury` irá procurar documentos que contenham `wifi` ou `luxury` ou ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operador NOT `-`

O operador NOT é um sinal de subtração. Por exemplo, `wifi –luxury` pesquisará documentos que têm a `wifi` termo e/ou não têm `luxury` (e/ou são controlados pelo `searchMode`).

> [!NOTE]  
>  A opção `searchMode` controla se um termo com o operador NOT é ANDed ou Orns com os outros termos na consulta na ausência de um operador `+` ou `|`. Lembre-se de que o `searchMode` pode ser definido como `any` (padrão) ou `all`. Se você usar `any`, ele aumentará a recall de consultas, incluindo mais resultados e, por padrão, `-` será interpretado como "ou não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` ou aqueles que não contêm o termo `luxury`. Se você usar `all`, ele aumentará a precisão das consultas, incluindo menos resultados e, por padrão, será interpretado como "e não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo "luxo". Isso é, sem dúvida, um comportamento mais intuitivo para o operador de `-`. Portanto, você deve considerar o uso de `searchMode=all` em vez de `searchMode=any` se quiser otimizar as pesquisas de precisão em vez de recall, *e* os usuários frequentemente usam o operador de `-` em pesquisas.

## <a name="suffix-operator"></a>Operador de sufixo

O operador de sufixo é um asterisco `*`. Por exemplo, `lux*` pesquisará documentos que têm um termo que começa com `lux`, ignorando maiúsculas e minúsculas.  

## <a name="phrase-search-operator"></a>Operador de pesquisa de frases

O operador de frase inclui uma frase entre aspas `" "`. Por exemplo, embora `Roach Motel` (sem aspas) pesquise documentos que contenham `Roach` e/ou `Motel` em qualquer lugar em qualquer ordem, `"Roach Motel"` (com aspas) só corresponderá a documentos que contenham essa frase inteira juntas e nessa ordem (a análise de texto ainda aplica-se).

## <a name="precedence-operator"></a>Operador de precedência

O operador de precedência inclui a cadeia de caracteres entre parênteses `( )`. Por exemplo, `motel+(wifi | luxury)` irá procurar documentos que contenham o termo motel e `wifi` ou `luxury` (ou ambos).  

## <a name="escaping-search-operators"></a>Operadores de pesquisa de saída  

 Para usar os símbolos acima como parte real do texto de pesquisa, eles devem ter um escape prefixando-os com uma barra invertida. Por exemplo, `luxury\+hotel` resultará no termo `luxury+hotel`. Para tornar as coisas simples para os casos mais comuns, há duas exceções a essa regra em que o escape não é necessário:  

- O operador NOT `-` só precisa ser escapado se for o primeiro caractere após o espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `wi-fi` é um único termo; enquanto os GUIDs (como `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) são tratados como um único token.
- O operador de sufixo `*` precisará ser ignorado somente se for o último caractere antes do espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `wi*fi` é tratado como um único token.

> [!NOTE]  
>  Embora o escape Mantenha os tokens juntos, a análise de texto pode dividi-los, dependendo do modo de análise. Consulte [suporte ao &#40;idioma Azure pesquisa cognitiva API&#41; REST](index-add-language-analyzers.md) para obter detalhes.  

## <a name="see-also"></a>Ver também  

+ [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
