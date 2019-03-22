---
title: Sintaxe de consulta Lucene - Azure Search
description: Referência para a sintaxe de Lucene completa, utilizado com o Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/31/2019
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
ms.openlocfilehash: a2576a0489ad62aba0a85a45f110acb8ac220847
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107190"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Sintaxe de consulta Lucene no Azure Search
Pode escrever consultas no Azure Search com base na avançada [analisador de consultas de Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) sintaxe para formulários de consulta especializadas: carateres universais, a pesquisa difusa, pesquisa de proximidade, expressões regulares são alguns exemplos. Grande parte da sintaxe do analisador de consultas de Lucene é [implementado intacto no Azure Search](search-lucene-query-architecture.md), com exceção do *pesquisas de intervalo* que é construído no Azure Search através de `$filter` expressões. 

## <a name="how-to-invoke-full-parsing"></a>Como invocar o serviço de análise completa

Definir o `queryType` parâmetro para especificar qual analisador utilizar de pesquisa. Valores válidos incluem `simple|full`, com `simple` como o padrão, e `full` para Lucene. 

<a name="bkmk_example"></a> 

## <a name="example-showing-full-syntax"></a>Sintaxe completa do exemplo que mostra

O exemplo a seguir encontra documentos no índice utilizando a sintaxe de consulta Lucene, evidente na `queryType=full` parâmetro. Esta consulta devolve hotéis, em que o campo de categoria contém o termo "orçamento" e todos os campos pesquisáveis, que contém a frase "recentemente renovated". Documentos que contêm a frase "recentemente renovated" estão ordenados superior como resultado o valor de aumento de termo (3).  

O `searchMode=all` parâmetro é relevante neste exemplo. Sempre que são operadores de consulta, em geral, deve definir `searchMode=all` para se certificar de que *todos os* dos critérios é correspondido.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full
```

 Em alternativa, utilize POST:  

```
POST /indexes/hotels/docs/search?api-version=2015-02-28
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Para obter exemplos adicionais, consulte [exemplos de sintaxe de consulta Lucene para a criação de consultas na Azure Search](search-query-lucene-examples.md). Para obter detalhes sobre como especificar o contingent completa dos parâmetros de consulta, consulte [documentos sobre pesquisa &#40;API de REST do serviço de pesquisa do Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  O Azure Search também suporta [sintaxe de consulta simples](query-simple-syntax.md), uma linguagem que pode ser utilizada para pesquisa de palavra-chave simples de consulta de simples e robusto.  


##  <a name="bkmk_fields"></a> Consultas de âmbito de campo  
 Pode especificar um `fieldname:searchterm` construção para definir uma operação de consulta respondidas, onde o campo é uma única palavra, e o termo de pesquisa também é uma única palavra ou frase, opcionalmente, com operadores booleanos. Alguns exemplos incluem o seguinte:  

- genre: jazz não histórico  

- artists:("Miles Davis" "John Coltrane")

  Certifique-se de que colocar várias cadeias de caracteres entre aspas, se pretender que ambas as cadeias de caracteres a ser avaliada como uma única entidade, neste caso a procurar dois artistas distintas no `artists` campo.  

  O campo especificado no `fieldname:searchterm` tem de ser um `searchable` campo.  Ver [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados nas definições de campo.  

##  <a name="bkmk_fuzzy"></a> pesquisa difusa  
 A pesquisa difusa localiza correspondências em termos que têm uma construção semelhante. Por [documentação de Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), pesquisas difusas baseiam-se nos [Damerau Levenshtein distância](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance).  

 Para fazer uma pesquisa difusa, use o til "~" símbolo no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (predefinição), que especifica a distância de edição. Por exemplo, "azul ~" ou "azul ~ 1" retornaria "azul", "blues" e "cola".

 Só pode ser aplicada a pesquisa difusa termos, não frases. Pesquisas difusas podem expandir um termo até o máximo de 50 termos que cumprem os critérios de distância.

##  <a name="bkmk_proximity"></a> pesquisa de proximidade  
 Pesquisas de proximidades são usadas para encontrar os termos que estejam perto uns dos outros num documento. Inserir um til "~" símbolo no final de uma frase seguido do número de palavras que criar o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrará o hotel"termos" e "aeroporto" dentro de 5 palavras da outra num documento.  


##  <a name="bkmk_termboost"></a> prazo adaptativo  
 Aumentos de termos refere-se a classificação de um documento superior se contiver o termo elevado, em relação ao documentos que contenham o termo. Isto é diferente de perfis de classificação em que aumentam a perfis de classificação determinados campos, em vez de termos específicos.  

O exemplo a seguir ajuda a ilustrar as diferenças. Suponha que o que há uma classificação de perfil que aumenta a corresponde num determinado campo, digamos *gênero* no [exemplo musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Aumentos de termos poderiam ser usado para melhorar ainda mais determinada pesquisa termos mais do que outras pessoas. Por exemplo, `rock^2 electronic` irá aumentar os documentos que contêm os termos de pesquisa no campo de gênero maior do que outros campos pesquisáveis no índice. Além disso, documentos que contenham o termo de pesquisa *rock* será classificado superior do que o termo de pesquisa *eletrônicos* como resultado o valor de aumento de termo (2).  

 Para aumentar um termo de usar o sinal de interpolação, "^", símbolo com um fator de aumento (um número) no final do período de vigência esteja a pesquisar. Também pode aumentar o frases. Quanto maior for o fator de aumento, o mais relevante o termo serão relativas a outros termos de pesquisa. Por predefinição, o fator de aumento é 1. Embora o fator de boost tem de ser positivo, ele pode ser inferior a 1 (por exemplo, 0.20).  

##  <a name="bkmk_regex"></a> pesquisa de expressão regular  
 Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre barras "/", como documentado na [RegExp classe](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).  

 Por exemplo, para encontrar os documentos que contenham "motel" ou "hotel", especifique `/[mh]otel/`.  Pesquisas de expressão regular são comparadas com palavras individuais.   

##  <a name="bkmk_wildcard"></a> pesquisa com carateres universais  
 Pode usar geralmente reconhecido sintaxe para múltiplos (*) ou único (?) caráter pesquisas com carateres universais. Tenha em atenção de que o analisador de consultas de Lucene suporta a utilização desses símbolos com um único termo e não uma frase.  

 Por exemplo, para encontrar os documentos que contêm as palavras com o prefixo "note", como "Bloco de notas" ou "Bloco de notas", especifique "nota *".  

> [!NOTE]  
>  Não é possível utilizar um * ou? símbolo de como o primeiro caráter de uma pesquisa.  
>  Nenhuma análise de texto é executada em consultas de pesquisa com carateres universais. No momento da consulta, termos de consulta de caráter universal são comparados aos termos analisados no índice de pesquisa e expandidos.

##  <a name="bkmk_syntax"></a> Noções básicas de sintaxe  
 Os seguintes conceitos básicos da sintaxe se aplicam a todas as consultas que utilizam a sintaxe Lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação de operador no contexto

A colocação determina se um símbolo é interpretado como um operador ou apenas outro caractere numa cadeia de caracteres.

Por exemplo, na sintaxe de Lucene completa, til (~) é utilizado para a pesquisa difusa e pesquisa de proximidade. Quando colocado após uma frase com aspas simples, ~ invoca a pesquisa de proximidade. Quando colocado no final de um período, ~ invoca a pesquisa difusa.

Dentro de um termo, tais como "business ~ analista", o caráter não é avaliado como um operador. Neste caso, supondo que a consulta é uma consulta de termo ou frase, [pesquisa em texto completo](search-lucene-query-architecture.md) com [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) separe a ~ e divide o termo "business ~ analista" em duas: empresa analista OR.

O exemplo acima é til (~), mas o mesmo princípio aplica-se a cada operador.

### <a name="escaping-special-characters"></a>Efetuando escape dos carateres especiais

 Carateres especiais devem ser escritos a ser utilizado como parte do texto de pesquisa. Pode escapar colocando-os com barra invertida (\\). Carateres especiais que precisam de caracteres de escape incluem o seguinte:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Por exemplo, para o escape um caráter universal, utilize \\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de carateres não seguros e reservados de URLs

 Certifique-se de que todos os carateres não seguros e reservados são codificados numa URL. Por exemplo, '#' é um caráter não seguro porque é um identificador de fragement/âncora num URL. O caráter tem de ser codificado para `%23` se for utilizado num URL. ' &' e '=' são exemplos de carateres reservados, tal como delimitar os parâmetros e especifique os valores no Azure Search. Consulte [RFC1738: Uniforme localizadores (URL de recurso)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

 Carateres não seguros são ``" ` < > # % { } | \ ^ ~ [ ] ``. Os carateres reservados são `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operadores de precedência: agrupamento e o agrupamento de campo  
 Pode utilizar parênteses para criar as subconsultas, incluindo os operadores dentro da instrução aninhamentos. Por exemplo, `motel+(wifi||luxury)` irá procurar documentos que contenham o termo "motel" e "Wi-Fi" ou "luxo" (ou ambos).

Agrupamento de campo é semelhante mas âmbitos de agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` procura o campo "hotelAmenities", "gym" e "Wi-Fi," ou "gym" e "agrupamento".  

### <a name="searchmode-parameter-considerations"></a>Considerações sobre o parâmetro SearchMode  
 O impacto da `searchMode` em consultas, conforme descrito na [sintaxe de consulta simples no Azure Search](query-simple-syntax.md), aplica-se igualmente a sintaxe de consulta Lucene. Ou seja, `searchMode` em conjunto com não operadores podem resultar em resultados de consulta que podem parecer diferente, se não fica claro sobre as implicações de forma que define o parâmetro. Se mantiver a predefinição, `searchMode=any`e usar um operador não, a operação é calculado como uma ação de ou, de modo a que o "New York" não "Seattle" devolve todas as cidades que não são Seattle.  

##  <a name="bkmk_boolean"></a> Operadores booleanos  
 Sempre Especifica operadores booleanos de texto (AND, OR, NOT) em maiúsculas.  

#### <a name="or-operator-or-or-"></a>Operador OR `OR` ou `||`

O operador OR é uma barra vertical ou caráter de pipe. Por exemplo: `wifi || luxury` irá procurar documentos que contenham "Wi-Fi" ou "luxo" ou ambos. Porque ou é o operador de conjunto padrão, poderia deixá-lo, também, de modo que `wifi luxury` é o equivalente de `wifi || luxuery`.

#### <a name="and-operator-and--or-"></a>E o operador `AND`, `&&` ou `+`

O operador AND fica um e comercial ou um sinal de adição. Por exemplo: `wifi && luxury` irá procurar documentos que contenham "Wi-Fi" e "luxo". O caráter de adição (+) é utilizado para termos necessários. Por exemplo, `+wifi +luxury` estipula que ambos os termos tem de aparecer algures no campo de um único documento.


#### <a name="not-operator-not--or--"></a>Operador NOT `NOT`, `!` ou `-`

O operador não é um ponto de exclamação ou o sinal de subtração. Por exemplo: `wifi !luxury` pesquisará para documentos que tenham "Wi-Fi" prazo e/ou não tem "luxo". O `searchMode` opção controles seja um termo Holmes ANDed ou ORed com os outros termos da consulta na ausência de um + ou | | operador. Lembre-se de que `searchMode` pode ser definida para o `any`(predefinição) ou `all`.

Usando `searchMode=any` aumenta a solicitação de recolhimento de consultas, incluindo mais resultados e, por predefinição – será interpretado como "Ou não". Por exemplo, `wifi -luxury` corresponderão aos documentos que contenham qualquer um o termo *Wi-Fi* ou que não contêm o termo *luxo.*

Usando `searchMode=all` aumenta a precisão de consultas, incluindo menos resultados e, por predefinição – será interpretado como "E não". Por exemplo, `wifi -luxury` corresponderão aos documentos que contenham o termo `wifi` e não contêm o termo `luxury`. Isto é, indiscutivelmente, um comportamento mais intuitivo para o opção - operador. Por isso, deve considerar escolher `searchMode=all` ao longo `searchMode=any` se pretender otimizar a procura de precisão em vez de recolhimento *e* os utilizadores utilizam com frequência a `-` operador nas pesquisas.

##  <a name="bkmk_querysizelimits"></a> Limitações de tamanho de consulta  
 Existe um limite para o tamanho de consultas que pode enviar para o Azure Search. Especificamente, pode ter no máximo cláusulas de 1024 (expressões separadas por AND, OR e assim por diante). Também existe um limite de aproximadamente 32 KB no tamanho de qualquer termo individual numa consulta. Se a sua aplicação gerar consultas de pesquisa por meio de programação, recomendamos que criá-lo de forma que ele não gera consultas de tamanho não vinculado.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Consultas de regex e caracteres curinga de classificação
 O Azure Search utiliza a classificação com base na frequência ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) para consultas de texto. No entanto, para consultas de regex e caracteres curinga em que o âmbito dos termos pode ser ampla, o fator de frequência é ignorado para impedir que a classificação de biasing (desvio) para correspondências de termos mais raros. Todas as correspondências são tratadas igualmente de caráter universal e regex pesquisas.

## <a name="see-also"></a>Consulte também  

+ [Documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe da expressão OData para filtros e classificação](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples no Azure Search](query-simple-syntax.md)   
