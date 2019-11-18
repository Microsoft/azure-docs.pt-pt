---
title: Sintaxe de consulta Lucene
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta Lucene completa, conforme usada no Azure Pesquisa Cognitiva para curinga, pesquisa difusa, RegEx e outras construções de consulta avançadas.
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
ms.openlocfilehash: 0bb8474b30c05e21a62ded1fa2cb8a6df8e4e321
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112179"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta Lucene no Azure Pesquisa Cognitiva

Você pode escrever consultas no Azure Pesquisa Cognitiva com base na sintaxe do [analisador de consulta do Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) avançada para formulários de consulta especializados: curinga, pesquisa difusa, pesquisa por proximidade, expressões regulares são alguns exemplos. Grande parte da sintaxe do analisador de consulta Lucene é [implementada intacta no azure pesquisa cognitiva](search-lucene-query-architecture.md), com exceção das *pesquisas de intervalo* que são construídas no Azure pesquisa cognitiva por meio de expressões `$filter`. 

## <a name="how-to-invoke-full-parsing"></a>Como invocar a análise completa

Defina o parâmetro de pesquisa `queryType` para especificar qual analisador usar. Os valores válidos incluem `simple|full`, com `simple` como o padrão e `full` para Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Exemplo mostrando a sintaxe completa

O exemplo a seguir localiza documentos no índice usando a sintaxe de consulta Lucene, evidente no parâmetro `queryType=full`. Essa consulta retorna Hotéis em que o campo categoria contém o termo "orçamento" e todos os campos pesquisáveis que contêm a frase "recentemente renovados". Os documentos que contêm a frase "recentemente renovados" têm classificação mais alta como resultado do valor de aumento de termo (3).  

O parâmetro `searchMode=all` é relevante neste exemplo. Sempre que os operadores estiverem na consulta, você geralmente deve definir `searchMode=all` para garantir que *todos* os critérios sejam correspondidos.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Como alternativa, use POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Para obter exemplos adicionais, consulte [exemplos de sintaxe de consulta Lucene para criar consultas no Azure pesquisa cognitiva](search-query-lucene-examples.md). Para obter detalhes sobre como especificar o contingente total dos parâmetros de consulta, consulte [Pesquisar &#40;documentos&#41;Azure pesquisa cognitiva API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  O Azure Pesquisa Cognitiva também dá suporte à [sintaxe de consulta simples](query-simple-syntax.md), uma linguagem de consulta simples e robusta que pode ser usada para pesquisa de palavra-chave direta.  

##  <a name="bkmk_syntax"></a>Conceitos básicos de sintaxe  
 Os conceitos básicos de sintaxe a seguir se aplicam a todas as consultas que usam a sintaxe Lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação do operador no contexto

O posicionamento determina se um símbolo é interpretado como um operador ou apenas outro caractere em uma cadeia de caracteres.

Por exemplo, na sintaxe completa do Lucene, o til (~) é usado para pesquisa difusa e pesquisa de proximidade. Quando colocado após uma frase entre aspas, ~ invoca a pesquisa de proximidade. Quando colocado no final de um termo, ~ invoca a pesquisa difusa.

Em um termo, como "analista de negócios ~", o caractere não é avaliado como um operador. Nesse caso, supondo que a consulta seja uma consulta de termo ou frase, a [pesquisa de texto completo](search-lucene-query-architecture.md) com [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) retira o ~ e interrompe o termo "Business ~ analista" em dois: Business ou analista.

O exemplo acima é o til (~), mas o mesmo princípio se aplica a todos os operadores.

### <a name="escaping-special-characters"></a>Caracteres especiais de escape

 Caracteres especiais devem ter escape para serem usados como parte do texto de pesquisa. Você pode escapar deles prefixando-os com barra invertida (\\). Os caracteres especiais que precisam ser ignorados incluem o seguinte:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Por exemplo, para escapar de um caractere curinga, use \\\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificando caracteres inseguros e reservados em URLs

 Verifique se todos os caracteres não seguros e reservados estão codificados em uma URL. Por exemplo, ' # ' é um caractere não seguro porque é um identificador de fragement/âncora em uma URL. O caractere deve ser codificado para `%23` se usado em uma URL. ' & ' e ' = ' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Consulte [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

 Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operadores de precedência: agrupamento e agrupamento de campos  
 Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi||luxury)` irá procurar documentos que contenham o termo "Motel" e "WiFi" ou "luxo" (ou ambos).

O agrupamento de campos é semelhante, mas o agrupamento é definido em um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` pesquisa o campo "hotelAmenities" para "Gym" e "WiFi", ou "Gym" e "pool".  

### <a name="searchmode-parameter-considerations"></a>Considerações sobre o parâmetro searchmode  
 O impacto de `searchMode` em consultas, conforme descrito em [sintaxe de consulta simples no Azure pesquisa cognitiva](query-simple-syntax.md), aplica-se igualmente à sintaxe de consulta Lucene. Ou seja, `searchMode` em conjunto com NOT Operators pode resultar em resultados de consulta que podem parecer incomum se você não estiver claro sobre as implicações de como você define o parâmetro. Se você mantiver o padrão, `searchMode=any`e usar um operador NOT, a operação será computada como uma ação ou, de modo que "Nova York" não "Seattle" retorna todas as cidades que não são Seattle.  

##  <a name="bkmk_boolean"></a>Operadores boolianos (e, ou, não) 
 Sempre especifique operadores boolianos de texto (e, ou, não) em maiúsculas.  

### <a name="or-operator-or-or-"></a>Operador OR `OR` ou `||`

O operador OR é um caractere de barra vertical ou de pipe. Por exemplo: `wifi || luxury` irá procurar documentos que contenham "WiFi" ou "luxo" ou ambos. Como ou é o operador de conjunção padrão, você também pode deixá-lo fora, de modo que `wifi luxury` é o equivalente de `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>Operador AND `AND`, `&&` ou `+`

O operador AND é um e comercial ou um sinal de adição. Por exemplo: `wifi && luxury` irá procurar documentos que contenham "WiFi" e "luxo". O caractere de mais (+) é usado para os termos necessários. Por exemplo, `+wifi +luxury` estipula que ambos os termos devem aparecer em algum lugar no campo de um único documento.


### <a name="not-operator-not--or--"></a>Operador NOT `NOT`, `!` ou `-`

O operador NOT é um ponto de exclamação ou sinal de subtração. Por exemplo: `wifi !luxury` pesquisará documentos que tenham o termo "WiFi" e/ou que não tenham "luxo". A opção `searchMode` controla se um termo com o operador NOT é ANDed ou Orns com os outros termos na consulta na ausência de um + ou | | operador. Lembre-se de que o `searchMode` pode ser definido como `any`(padrão) ou `all`.

O uso de `searchMode=any` aumenta a recall de consultas, incluindo mais resultados e, por padrão, será interpretado como "ou não". Por exemplo, `wifi -luxury` corresponderá a documentos que contenham o termo *WiFi* ou aqueles que não contenham o termo *luxo.*

O uso de `searchMode=all` aumenta a precisão das consultas, incluindo menos resultados e, por padrão, será interpretado como "e não". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo `luxury`. Isso é, sem dúvida, um comportamento mais intuitivo para o operador-. Portanto, você deve considerar a escolha de `searchMode=all` sobre `searchMode=any` se desejar otimizar as pesquisas de precisão em vez de recall *e* os usuários usarem com frequência o operador de `-` em pesquisas.

##  <a name="bkmk_querysizelimits"></a>Limitações de tamanho de consulta  
 Há um limite para o tamanho das consultas que você pode enviar para o Azure Pesquisa Cognitiva. Especificamente, você pode ter no máximo 1024 cláusulas (expressões separadas por AND, OR e assim por diante). Também há um limite de aproximadamente 32 KB no tamanho de qualquer termo individual em uma consulta. Se seu aplicativo gerar consultas de pesquisa programaticamente, é recomendável criá-la de forma que ela não gere consultas de tamanho não associado.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Consultas curinga e Regex de Pontuação
 O Azure Pesquisa Cognitiva usa a pontuação baseada em frequência ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) para consultas de texto. No entanto, para consultas de caractere curinga e Regex em que o escopo de termos pode potencialmente ser amplo, o fator de frequência é ignorado para impedir que a classificação se deformasse com as correspondências dos termos mais raros. Todas as correspondências são tratadas igualmente para pesquisas de curinga e Regex.

##  <a name="bkmk_fields"></a>Pesquisa em campo  
Você pode definir uma operação de pesquisa em campo com a sintaxe `fieldName:searchExpression`, em que a expressão de pesquisa pode ser uma única palavra ou frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:  

- Gênero: Jazz não histórico  

- artistas:("milhas Davis" "John Coltrane")

Certifique-se de colocar várias cadeias de caracteres entre aspas se desejar que ambas as cadeias de caracteres sejam avaliadas como uma única entidade, nesse caso, pesquisando dois artistas distintos no campo `artists`.  

O campo especificado em `fieldName:searchExpression` deve ser um campo `searchable`.  Consulte [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.  

> [!NOTE]
> Ao usar expressões de pesquisa em campo, você não precisa usar o parâmetro `searchFields` porque cada expressão de pesquisa em campo tem um nome de campo especificado explicitamente. No entanto, você ainda poderá usar o parâmetro `searchFields` se quiser executar uma consulta em que algumas partes têm o escopo de um campo específico e o restante pode se aplicar a vários campos. Por exemplo, o `search=genre:jazz NOT history&searchFields=description` de consulta corresponderia `jazz` apenas ao campo `genre`, enquanto ele corresponderia `NOT history` com o campo `description`. O nome do campo fornecido em `fieldName:searchExpression` sempre tem precedência sobre o parâmetro `searchFields`, que é o motivo neste exemplo, não precisamos incluir `genre` no parâmetro `searchFields`.

##  <a name="bkmk_fuzzy"></a>Pesquisa difusa  
 Uma pesquisa difusa localiza correspondências em termos que têm uma construção semelhante. Por [documentação do Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), as pesquisas difusas se baseiam na [distância Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Pesquisas difusas podem expandir um termo até o máximo de 50 termos que atendem aos critérios de distância. 

 Para fazer uma pesquisa difusa, use o símbolo de til "~" no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (padrão), que especifica a distância de edição. Por exemplo, "Blue ~" ou "Blue ~ 1" retornariam "Blue", "azuis" e "Glue".

 A pesquisa difusa só pode ser aplicada a termos, não a frases, mas você pode acrescentar o til a cada termo individualmente em um nome ou frase de várias partes. Por exemplo, "Unviersty ~ de ~" Wshington ~ "corresponderia em" University de Washington ".
 

##  <a name="bkmk_proximity"></a>Pesquisa de proximidade  
 As pesquisas de proximidade são usadas para encontrar os termos próximos uns dos outros em um documento. Insere um símbolo de til "~" no final de uma frase, seguido pelo número de palavras que criam o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrará os termos "Hotel" e "aeroporto" dentro de 5 palavras entre si em um documento.  


##  <a name="bkmk_termboost"></a>Aumento de termo  
 O aumento do termo refere-se à classificação de um documento mais alto se ele contiver o termo aumentado, em relação a documentos que não contêm o termo. Isso difere dos perfis de pontuação, pois os perfis de Pontuação impulsionam determinados campos, em vez de termos específicos.  

O exemplo a seguir ajuda a ilustrar as diferenças. Suponha que haja um perfil de pontuação que aumente as correspondências em um determinado campo, digamos *gênero* no [exemplo musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). O aumento de termo pode ser usado para aumentar ainda mais alguns termos de pesquisa mais altos do que outros. Por exemplo, `rock^2 electronic` aumentará os documentos que contêm os termos de pesquisa no campo gênero mais alto do que outros campos pesquisáveis no índice. Além disso, os documentos que contêm a *pedra* do termo de pesquisa serão classificados como mais altos do que o outro termo de pesquisa *eletrônico* como resultado do valor de aumento de termo (2).  

 Para aumentar um termo, use o sinal de interpolação "^", símbolo com um fator de aumento (um número) no final do termo que você está pesquisando. Você também pode aumentar as frases. Quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor que 1 (por exemplo, 0,20).  

##  <a name="bkmk_regex"></a>Pesquisa de expressão regular  
 Uma pesquisa de expressão regular localiza uma correspondência com base no conteúdo entre barras "/", conforme documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Por exemplo, para localizar documentos que contenham "Motel" ou "Hotel", especifique `/[mh]otel/`.  As pesquisas de expressões regulares são comparadas com palavras únicas.   

##  <a name="bkmk_wildcard"></a>Pesquisa de curinga  
 Você pode usar a sintaxe geralmente reconhecida para várias pesquisas de curinga de caractere (*) ou única (?). Observe que o analisador de consulta Lucene dá suporte ao uso desses símbolos com um único termo, e não uma frase.  

 Por exemplo, para localizar documentos que contenham as palavras com o prefixo "observação", como "bloco de anotações" ou "bloco de notas", especifique "observação *".  

> [!NOTE]  
>  Você não pode usar um * ou? símbolo como o primeiro caractere de uma pesquisa.  
>  Nenhuma análise de texto é executada em consultas de pesquisa de caractere curinga. No momento da consulta, os termos de consulta curinga são comparados com os termos analisados no índice de pesquisa e expandidos.

## <a name="see-also"></a>Ver também  

+ [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe de expressão OData para filtros e classificação](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples no Azure Pesquisa Cognitiva](query-simple-syntax.md)   
