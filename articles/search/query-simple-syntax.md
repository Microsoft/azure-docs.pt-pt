---
title: Sintaxe de consulta simples - Azure Search
description: Referência para a sintaxe de consulta simples usada para consultas de pesquisa de texto completo no Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 99729141e5e1478f45ad385cf671c44a8e08f21a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437497"
---
# <a name="simple-query-syntax-in-azure-search"></a>Sintaxe de consulta simples no Azure Search
O Azure Search implementa duas linguagens de consulta Lucene com base em: [Analisador de consultas simples](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [analisador de consultas de Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). No Azure Search, a sintaxe de consulta simples exclui as opções de difusa/slop.  

> [!NOTE]  
>  O Azure Search oferece uma alternativa [sintaxe de consulta Lucene](query-lucene-syntax.md) para consultas mais complexas. Para saber mais sobre análise de arquitetura e os benefícios de cada sintaxe de consulta, consulte [completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Como invocar o serviço de análise simples

Sintaxe simples é a predefinição. Invocação será necessária apenas se estão a redefinir a sintaxe de completa para simples. Para definir explicitamente a sintaxe, utilize o `queryType` parâmetro de pesquisa. Valores válidos incluem `simple|full`, com `simple` como o padrão, e `full` para Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalias de comportamento de consulta

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. O Azure Search corresponderá ao documentos que contenham qualquer um ou todos os termos, incluindo qualquer variações encontradas durante a análise de texto. 

Simples que pareça, há um aspecto de execução da consulta no Azure Search que *poderá* produzir resultados inesperados, aumento em vez de diminuir a pesquisa resulta conforme mais termos e operadores são adicionados à entrada da cadeia de caracteres. Esta expansão ocorre realmente depende da inclusão de um operador NOT, combinado com um `searchMode` definição de parâmetro que determina como não é interpretada em termos de e ou comportamentos OR. Tendo em conta a predefinição `searchMode=Any`, e um operador não, a operação é calculado como uma ação de OR, que `"New York" NOT Seattle` devolve todas as cidades que não são Seattle.  

Normalmente, está mais probabilidades de ver esses comportamentos em padrões de interação do usuário para aplicativos que procurar em conteúdo, onde os utilizadores têm maior probabilidade de incluir um operador numa consulta, em vez de sites de comércio eletrônico que tenham mais internas estruturas de navegação. Para obter mais informações, consulte [operador NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operadores booleanos (AND, OR, NOT) 

Pode incorporar operadores numa cadeia de consulta para criar um conjunto avançado de critérios em relação aos quais se encontram documentos correspondentes. 

### <a name="and-operator-"></a>E o operador `+`

O operador AND é um sinal de adição. Por exemplo, `wifi+luxury` irá procurar documentos que contenham ambos `wifi` e `luxury`.

### <a name="or-operator-"></a>OU o operador `|`

O operador OR é uma barra vertical ou caráter de pipe. Por exemplo, `wifi | luxury` irá procurar documentos contendo `wifi` ou `luxury` ou ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operador NOT `-`

O operador não é um sinal de subtração. Por exemplo, `wifi –luxury` irá procurar documentos com o `wifi` prazo e/ou não tem `luxury` (e/ou é controlado pelo `searchMode`).

> [!NOTE]  
>  O `searchMode` opção controles seja um termo Holmes ANDed ou ORed com os outros termos da consulta na ausência de um `+` ou `|` operador. Lembre-se de que `searchMode` pode ser definida para o `any` (predefinição) ou `all`. Se usar `any`, que irá aumentar a solicitação de recolhimento de consultas, incluindo mais resultados e, por predefinição `-` será interpretado como "Ou não". Por exemplo, `wifi -luxury` corresponderão aos documentos que contenham qualquer um o termo `wifi` ou que não contêm o termo `luxury`. Se usar `all`, ele irá aumentar a precisão de consultas, incluindo menos resultados e, por padrão – será interpretado como "E não". Por exemplo, `wifi -luxury` corresponderão aos documentos que contenham o termo `wifi` e não contêm o termo "luxo". Isto é, indiscutivelmente, um comportamento mais intuitivo para a `-` operador. Portanto, deve considerar o uso `searchMode=all` em vez de `searchMode=any` se pretender otimizar a procura de precisão em vez de recolhimento, *e* os utilizadores utilizam com frequência a `-` operador em pesquisas.

## <a name="suffix-operator"></a>Operador de sufixo

O operador de sufixo é um asterisco `*`. Por exemplo, `lux*` irá procurar documentos que têm um prazo que começa com `lux`, ignorando maiúsculas e minúsculas.  

## <a name="phrase-search-operator"></a>Operador de pesquisa de expressão

O operador de frase engloba uma frase aspas `" "`. Por exemplo, apesar `Roach Motel` (sem aspas) seria procurar documentos que contenham `Roach` e/ou `Motel` em qualquer lugar em qualquer ordem, `"Roach Motel"` (com as aspas) só irá corresponder a documentos que contenham essa frase inteira, em conjunto e, em que ordem (análise de texto ainda aplica-se).

## <a name="precedence-operator"></a>Operador de precedência

O operador de precedência engloba a cadeia de caracteres em parênteses `( )`. Por exemplo, `motel+(wifi | luxury)` irá procurar documentos que contenham o termo de hotel e um `wifi` ou `luxury` (ou ambos).  

## <a name="escaping-search-operators"></a>Efetuando escape dos operadores de pesquisa  

 Para usar os símbolos acima como parte real do texto de pesquisa, eles devem ser escritos colocando-os com uma barra invertida. Por exemplo, `luxury\+hotel` resultará no termo `luxury+hotel`. Para tornar as coisas simples para os casos mais típicos, há duas exceções a esta regra em que a carateres de escape não é necessária:  

- O operador não `-` apenas tem de ser escritos se for o primeiro caráter depois de espaço em branco, não se encontrem no meio de um termo. Por exemplo, `wi-fi` é um termo único; enquanto GUIDs (como `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) são tratados como um único token.
- O operador de sufixo `*` tem de ser escritos apenas se for o último caráter antes de espaço em branco, não se encontrem no meio de um termo. Por exemplo, `wi*fi` é tratado como um único token.

> [!NOTE]  
>  Embora efetuando escape dos tokens mantém em conjunto, análise de texto poderá dividi-las, consoante o modo de análise. Ver [suporte de idioma &#40;API de REST do serviço de pesquisa do Azure&#41; ](index-add-language-analyzers.md) para obter detalhes.  

## <a name="see-also"></a>Consulte também  

+ [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe da expressão OData](query-odata-filter-orderby-syntax.md) 
