---
title: Sintaxe de consulta simples-Azure Search
description: Referência para a sintaxe de consulta simples usada para consultas de pesquisa de texto completo no Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: e6c5ea86534001e0e5de2b02c4151af70631e4ef
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650020"
---
# <a name="simple-query-syntax-in-azure-search"></a>Sintaxe de consulta simples no Azure Search
Azure Search implementa duas linguagens de consulta baseadas em Lucene: [Analisador de consulta simples](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o analisador de [consulta Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). No Azure Search, a sintaxe de consulta simples exclui as opções difusas/inclinada.  

> [!NOTE]  
>  Azure Search fornece uma [sintaxe de consulta Lucene](query-lucene-syntax.md) alternativa para consultas mais complexas. Para saber mais sobre a arquitetura de análise de consulta e os benefícios de cada sintaxe, consulte [como a pesquisa de texto completo funciona em Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Como invocar a análise simples

A sintaxe simples é o padrão. A invocação só será necessária se você estiver redefinindo a sintaxe de completo para simples. Para definir explicitamente a sintaxe, use o `queryType` parâmetro de pesquisa. Os valores válidos `simple|full`incluem, `simple` com como o padrão, `full` e para Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalias de comportamento de consulta

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. Azure Search corresponderá a documentos que contêm qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto. 

Tão simples quanto esse som, há um aspecto da execução da consulta em Azure Search que *pode* produzir resultados inesperados, aumentando em vez de reduzir os resultados da pesquisa conforme mais termos e operadores são adicionados à cadeia de caracteres de entrada. Se essa expansão realmente ocorrer depende da inclusão de um operador NOT, combinada com uma `searchMode` configuração de parâmetro que determina como não é interpretado em termos de e ou ou comportamentos. Considerando o padrão, `searchMode=Any`, e um operador NOT, a operação é computada como uma ação ou, de `"New York" NOT Seattle` modo que retorna todas as cidades que não são Seattle.  

Normalmente, é mais provável que você veja esses comportamentos nos padrões de interação do usuário para aplicativos que pesquisam o conteúdo, em que os usuários têm mais probabilidade de incluir um operador em uma consulta, em oposição aos sites de comércio eletrônico que têm estruturas de navegação mais internas. Para obter mais informações, consulte [not Operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operadores boolianos (e, ou, não) 

Você pode inserir operadores em uma cadeia de caracteres de consulta para criar um conjunto avançado de critérios em relação aos quais os documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>Operador AND`+`

O operador AND é um sinal de adição. Por exemplo, `wifi+luxury` o pesquisará documentos que contenham o e `luxury`o `wifi` .

### <a name="or-operator-"></a>Operador OR`|`

O operador OR é um caractere de barra vertical ou de pipe. Por exemplo, `wifi | luxury` o pesquisará documentos que contenham um `wifi` ou `luxury` ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operador NOT`-`

O operador NOT é um sinal de subtração. Por exemplo, `wifi –luxury` o pesquisará documentos que têm o `wifi` termo e/ou que não têm `luxury` (e/ou são controlados `searchMode`pelo).

> [!NOTE]  
>  A `searchMode` opção controla se um termo com o operador NOT é ANDed ou orns com os outros termos na consulta na ausência de um `+` operador or `|` . Lembre- `searchMode` se de que o `any` pode ser definido como ( `all`padrão) ou. Se você usar `any`o, ele aumentará a recall de consultas, incluindo mais resultados, e `-` , por padrão, será interpretado como "ou não". Por exemplo, `wifi -luxury` o corresponderá a documentos que contêm `wifi` o termo ou aqueles que não contêm o `luxury`termo. Se você usar `all`o, ele aumentará a precisão das consultas, incluindo menos resultados e, por padrão, será interpretado como "e não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm `wifi` o termo e não contêm o termo "luxo". Isso é, sem dúvida, um comportamento mais `-` intuitivo para o operador. Portanto, você deve considerar o `searchMode=all` uso do `searchMode=any` em vez de se deseja otimizar as pesquisas de precisão em vez de recall, *e* os `-` usuários frequentemente usam o operador em pesquisas.

## <a name="suffix-operator"></a>Operador de sufixo

O operador de sufixo é um `*`asterisco. Por exemplo, `lux*` o pesquisará documentos que têm um termo que começa com `lux`, ignorando maiúsculas e minúsculas.  

## <a name="phrase-search-operator"></a>Operador de pesquisa de frases

O operador de frase fecha uma frase entre aspas `" "`. Por exemplo, while `Roach Motel` (sem aspas) pesquisaria documentos que contenham `Roach` e `Motel` /ou em qualquer lugar `"Roach Motel"` em qualquer ordem, (com aspas) só corresponderão a documentos que contenham essa frase inteira e, nesse caso, ordem (a análise de texto ainda se aplica).

## <a name="precedence-operator"></a>Operador de precedência

O operador de precedência inclui a cadeia de caracteres entre `( )`parênteses. Por exemplo, `motel+(wifi | luxury)` o `wifi` pesquisará documentos que contenham o termo Motel `luxury` e ou (ou ambos).  

## <a name="escaping-search-operators"></a>Operadores de pesquisa de saída  

 Para usar os símbolos acima como parte real do texto de pesquisa, eles devem ter um escape prefixando-os com uma barra invertida. Por exemplo, `luxury\+hotel` resultará no termo `luxury+hotel`. Para tornar as coisas simples para os casos mais comuns, há duas exceções a essa regra em que o escape não é necessário:  

- O operador `-` not só precisa ser escapado se for o primeiro caractere após o espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `wi-fi` é um único termo; enquanto os `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`GUIDs (como) são tratados como um único token.
- O operador `*` de sufixo precisará ser ignorado somente se for o último caractere antes do espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `wi*fi` é tratado como um único token.

> [!NOTE]  
>  Embora o escape Mantenha os tokens juntos, a análise de texto pode dividi-los, dependendo do modo de análise. Consulte [suporte &#40;a idiomas Azure Search&#41; API REST do serviço](index-add-language-analyzers.md) para obter detalhes.  

## <a name="see-also"></a>Consulte também  

+ [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
