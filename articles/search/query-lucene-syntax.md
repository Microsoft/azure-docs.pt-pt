---
title: Sintaxe de consulta Lucene
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta lucene completa, como usado em Azure Cognitive Search para wildcard, pesquisa fuzzy, RegEx, e outras construções de consulta avançada.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 0dbf418d0a673dd0799f0f638e454c484f837fd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516600"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta lucene em Pesquisa Cognitiva Azure

Ao criar consultas, pode optar pela sintaxe [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) para formas de consulta especializadas: wildcard, pesquisa fuzzy, pesquisa de proximidade, expressões regulares. Grande parte da sintaxe Lucene Query Parser é [implementada intacta na Pesquisa Cognitiva do Azure,](search-lucene-query-architecture.md)com exceção das pesquisas de *alcance* que são construídas através de **`$filter`** expressões. 

A sintaxe lucene completa é utilizada para expressões de consulta passadas no **`search`** parâmetro de um pedido de Documentos de Busca [(REST API),](/rest/api/searchservice/search-documents) não devendo ser confundida com a [sintaxe OData](query-odata-filter-orderby-syntax.md) utilizada para o [**`$filter`**](search-filters.md) e [**`$orderby`**](search-query-odata-orderby.md) expressões no mesmo pedido. Os parâmetros OData têm diferentes sintaxe e regras para a construção de consultas, cordas de fuga, e assim por diante.

## <a name="example-full-syntax"></a>Exemplo (sintaxe completa)

Desa estale o **`queryType`** parâmetro para especificar lucene completo. O exemplo a seguir invoca a procura no terreno e o aumento de prazos. Esta consulta procura hotéis onde o campo de categoria contém o termo "orçamento". Quaisquer documentos que contenham a frase "recentemente renovado" são classificados mais elevados como resultado do valor de aumento do termo (3).  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

O **`searchMode`** parâmetro é relevante neste exemplo. Sempre que os operadores estiverem em consulta, deve geralmente definir `searchMode=all` para garantir que todos *os* critérios são compatíveis.  

Para outros exemplos, consulte [os exemplos de sintaxe](search-query-lucene-examples.md)de consulta de Lucene . Para obter mais informações sobre o pedido de consulta e os parâmetros, consulte [Documentos de Pesquisa (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Fundamentos da sintaxe  

Os seguintes fundamentos da sintaxe aplicam-se a todas as consultas que utilizam a sintaxe lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação do operador em contexto

A colocação determina se um símbolo é interpretado como um operador ou apenas como outro personagem numa corda.

Por exemplo, na sintaxe completa de Lucene, o azulejo (~) é usado tanto para pesquisa de fuzzy como para pesquisa de proximidade. Quando colocado após uma frase citada, ~ invoca a procura de proximidade. Quando colocado no final de um termo, ~ invoca a procura confusa.

Dentro de um prazo, como "business~analyst", o personagem não é avaliado como operador. Neste caso, assumindo que a consulta é um termo ou consulta de frase, [a pesquisa completa de texto](search-lucene-query-architecture.md) com análise [lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) tira o ~ e quebra o termo "business~analyst" em dois: analista de negócios OU.

O exemplo acima é o azulejo (~), mas o mesmo princípio aplica-se a todos os operadores.

### <a name="escaping-special-characters"></a>Escapando de personagens especiais

Para utilizar qualquer um dos operadores de pesquisa como parte do texto de pesquisa, escape o personagem prefixando-o com uma única pestana `\` (). Por exemplo, para uma pesquisa de wildcard em `https://` , onde faz parte da cadeia de `://` consulta, você especificaria `search=https\:\/\/*` . Da mesma forma, um padrão de número de telefone escapado pode ser `\+1 \(800\) 642\-7676` assim.

Os caracteres especiais que requerem fuga incluem o seguinte:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Embora escapar mantenha os tokens juntos, a [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) durante a indexação pode despi-los. Por exemplo, o analisador padrão lucene quebrará palavras sobre hífens, espaço branco e outros personagens. Se necessitar de caracteres especiais na cadeia de consulta, poderá precisar de um analisador que os preserve no índice. Algumas escolhas incluem [analisadores de linguagem](index-add-language-analyzers.md)natural da Microsoft , que preserva palavras hifenizadas, ou um analisador personalizado para padrões mais complexos. Para mais informações, consulte [termos, padrões e caracteres especiais.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres inseguros e reservados em URLs

Certifique-se de que todos os caracteres inseguros e reservados estão codificados num URL. Por exemplo, '#' é um personagem inseguro porque é um identificador de fragmento/âncora num URL. O carácter deve ser codificado `%23` se for utilizado num URL. '&' e '=' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva Azure. Consulte [RFC1738: Localizadores de recursos uniformes (URL)](https://www.ietf.org/rfc/rfc1738.txt) para mais detalhes.

Personagens inseguros ``" ` < > # % { } | \ ^ ~ [ ]`` são. Os caracteres reservados `; / ? : @ = + &` são.

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Operadores booleanas

Pode incorporar os operadores Boolean numa cadeia de consulta para melhorar a precisão de uma correspondência. A sintaxe completa suporta os operadores de texto, para além dos operadores de caracteres. Especifique sempre os operadores de boolean de texto (E, OU, NÃO) em todas as tampas.

|Operador de texto | Caráter | Exemplo | Utilização |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | Especifica termos que uma correspondência deve conter. No exemplo, o motor de consulta procurará documentos que contenham ambos `wifi` e `luxury` . O carácter mais `+` () é usado para os termos necessários. Por exemplo, `+wifi +luxury` estipula que ambos os termos devem aparecer em algum lugar no campo de um único documento.|
| OR | `|` | `wifi | luxury` | Encontra uma correspondência quando qualquer um dos termos é encontrado. No exemplo, o motor de consulta voltará a corresponder em documentos que contenham `wifi` ou `luxury` ambos. Como o OR é o operador de conjunção padrão, também pode deixá-lo de fora, tal que `wifi luxury` é o equivalente a  `wifi | luxury` .|
| NOT | `!`, `-` | `wifi –luxury` | Devolução de correspondências em documentos que excluam o termo. Por exemplo, `wifi –luxury` procurará documentos que tenham o `wifi` termo, mas não `luxury` . <br/><br/>O `searchMode` parâmetro de um pedido de consulta controla se um termo com o operador NÃO é ANDed ou ORed com outros termos na consulta (assumindo que não existe nenhum `+` ou operador nos `|` outros termos). Valores válidos incluem `any` ou `all` .  <br/><br/>`searchMode=any` aumenta a recolha de consultas através da inclusão de mais resultados, e por defeito `-` será interpretado como "OU NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contenham o termo `wifi` ou os que não contenham o termo `luxury` .  <br/><br/>`searchMode=all` aumenta a precisão das consultas, incluindo menos resultados, e por defeito - será interpretado como "E NÃO". Por exemplo, `wifi -luxury` corresponderá a documentos que contenham o termo `wifi` e não contenham o termo "luxo". Este é indiscutivelmente um comportamento mais intuitivo para o `-` operador. Portanto, deve considerar a utilização `searchMode=all` em vez de se `searchMode=any` pretender otimizar as pesquisas de precisão em vez de se lembrar, *e* os seus utilizadores usam frequentemente o operador `-` nas pesquisas.<br/><br/>Ao decidir sobre uma `searchMode` definição, considere os padrões de interação do utilizador para consultas em várias aplicações. Os utilizadores que procuram informações são mais propensos a incluir um operador numa consulta, ao contrário dos sites de e-commerce que têm mais estruturas de navegação incorporadas. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Pesquisa de campo

Pode definir uma operação de pesquisa em campo com a `fieldName:searchExpression` sintaxe, onde a expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa em parênteses, opcionalmente com operadores Boolean. Alguns exemplos incluem:  

- género:jazz NÃO história  

- artistas:("Miles Davis" "John Coltrane")

Certifique-se de colocar várias cordas dentro das aspas se quiser que ambas as cordas sejam avaliadas como uma única entidade, neste caso procurando dois artistas distintos no `artists` campo.  

O campo especificado `fieldName:searchExpression` deve ser um `searchable` campo.  Consulte [o Índice de Criação](/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos do índice são usados nas definições de campo.  

> [!NOTE]
> Ao utilizar expressões de pesquisa em campo, não precisa de utilizar o `searchFields` parâmetro porque cada expressão de pesquisa no terreno tem um nome de campo explicitamente especificado. No entanto, ainda pode utilizar o `searchFields` parâmetro se quiser executar uma consulta onde algumas partes são telescópios para um campo específico, e o resto pode aplicar-se a vários campos. Por exemplo, a consulta `search=genre:jazz NOT history&searchFields=description` `jazz` corresponderia apenas ao `genre` campo, enquanto combinava `NOT history` com o `description` campo. O nome de campo fornecido `fieldName:searchExpression` sempre tem precedência sobre o `searchFields` parâmetro, e é por isso que neste exemplo, não precisamos incluir `genre` no `searchFields` parâmetro.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Pesquisa fuzzy

Uma pesquisa difusa encontra fósforos em termos que têm uma construção semelhante, expandindo um termo até ao máximo de 50 termos que cumprem os critérios de distância de dois ou menos. Para mais informações, consulte [a pesquisa fuzzy.](search-query-fuzzy.md)

Para fazer uma pesquisa difusa, utilize o símbolo de azulejo "~" no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (predefinido), que especifica a distância de edição. Por exemplo, "blue~" ou "blue~1" devolveria "azul", "blues" e "cola".

A pesquisa fuzzy só pode ser aplicada a termos, não frases, mas pode anexar o azulejo a cada termo individualmente em um nome ou frase multi-partes. Por exemplo, "Unviersty~ de~ "Wshington~" corresponderia à "Universidade de Washington".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Pesquisa de proximidade

As pesquisas de proximidade são usadas para encontrar termos que estão próximos uns dos outros em um documento. Insira um símbolo de azulejos "~" no final de uma frase seguida do número de palavras que criam o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrará os termos "hotel" e "aeroporto" dentro de 5 palavras um do outro num documento.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Aumento de prazos

O aumento de prazos refere-se à classificação de um documento mais elevado se contiver o termo aumentado, em relação a documentos que não contêm o termo. Isto difere de perfis de pontuação em que perfis de pontuação impulsionam certos campos, em vez de termos específicos.  

O exemplo a seguir ajuda a ilustrar as diferenças. Suponha que há um perfil de pontuação que impulsiona os jogos num determinado campo, digamos *género* no  [exemplo da musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). O aumento de prazos poderia ser usado para aumentar ainda mais certos termos de pesquisa mais elevados do que outros. Por exemplo, `rock^2 electronic` irá impulsionar documentos que contenham os termos de pesquisa no campo de género mais elevados do que outros campos pes pesjáveis no índice. Além disso, os documentos que contenham o termo de pesquisa *rock* serão classificados mais alto do que o outro termo de pesquisa *eletrónico* como resultado do termo aumento do valor (2).  

 Para impulsionar um termo use o caret, símbolo "^", com um fator de aumento (um número) no final do termo que está a pesquisar. Também pode aumentar as frases. Quanto maior for o fator de impulso, mais relevante será o termo relativo a outros termos de pesquisa. Por padrão, o fator de impulso é 1. Embora o fator de impulso tenha de ser positivo, pode ser inferior a 1 (por exemplo, 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Pesquisa regular de expressão  
 Uma pesquisa regular de expressão encontra uma correspondência baseada em padrões que são válidos sob Apache Lucene, como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). Na Pesquisa Cognitiva Azure, uma expressão regular é fechada entre cortes para a frente `/` .

 Por exemplo, para encontrar documentos que contenham "motel" ou "hotel", especifique `/[mh]otel/` . As pesquisas regulares de expressão são compatívels com palavras únicas.

Algumas ferramentas e línguas impõem requisitos adicionais de carácter de fuga. Para JSON, as cordas que incluem um corte para a frente escapam com um corte para trás: "microsoft.com/azure/" torna-se `search=/.*microsoft.com\/azure\/.*/` onde define a expressão `search=/.* <string-placeholder>.*/` regular, e é a corda com `microsoft.com\/azure\/` um corte para a frente escapado.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Pesquisa wildcard

Pode utilizar sintaxe geralmente reconhecida para pesquisas de caracteres múltiplos `*` () ou simples `?` () de caracteres wildcard. Por exemplo, uma expressão de consulta de `search=alpha*` devoluções "alfanumérica" ou "alfabética". Note que o parser de consulta Lucene suporta o uso destes símbolos com um único termo, e não uma frase.

A sintaxe Lucene completa suporta prefixo, infixo e sufixo correspondente. No entanto, se tudo o que precisa é de correspondência de prefixo, pode utilizar a sintaxe simples (a correspondência de prefixo é suportada em ambos).

A correspondência de sufixos, onde `*` ou precede a corda `?` (como `search=/.*numeric./` em) ou a correspondência de infixo requer uma sintaxe lucene completa, bem como a expressão regular para a frente `/` delimiters. Não pode usar um ou? símbolo como o primeiro caráter de um termo, ou dentro de um termo, sem o `/` . 

> [!NOTE]  
> Regra geral, a correspondência de padrões é lenta, por isso pode querer explorar métodos alternativos, como a tokenização edge n-gram que cria fichas para sequências de caracteres num termo. O índice será maior, mas as consultas podem executar mais rapidamente, dependendo da construção de padrões e do comprimento das cordas que está a indexar.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Impacto de um analisador em consultas wildcard

Durante a análise de consulta, as consultas formuladas como prefixo, sufixo, wildcard ou expressões regulares são passadas como-é para a árvore de consulta, contornando a [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis). As correspondências só serão encontradas se o índice contiver as cordas no formato que a sua consulta especifica. Na maioria dos casos, você precisará de um analisador durante a indexação que preserva a integridade das cordas para que o termo parcial e a correspondência de padrões sejam bem sucedidos. Para obter mais informações, consulte [a pesquisa parcial de termo nas consultas de Pesquisa Cognitiva Azure](search-query-partial-matching.md).

Considere uma situação em que pode querer que a consulta de pesquisa 'terminat*' devolva resultados que contenham termos como 'terminate', 'termination' e 'terminates'.

Se usasse o analisador en.lucene (Inglês Lucene), aplicaria uma decisão agressiva de cada termo. Por exemplo, 'terminar', 'rescisão', 'terminates' será tudo tokenizado até ao símbolo 'termi' no seu índice. Por outro lado, os termos em consultas que usam wildcards ou pesquisa duvidosa não são analisados de todo., por isso não haveria resultados que correspondessem à consulta 'terminat*'.

Por outro lado, os analisadores da Microsoft (neste caso, o analisador en.microsoft) são um pouco mais avançados e usam a lematização em vez de se conter. Isto significa que todos os tokens gerados devem ser palavras inglesas válidas. Por exemplo, 'terminate', 'terminates' e 'termination' permanecerão na sua maioria inteiros no índice, e seria uma escolha preferível para cenários que dependem muito de wildcards e pesquisa duvidosa.

## <a name="scoring-wildcard-and-regex-queries"></a>Consultas de wildcard e regex

A Azure Cognitive Search utiliza pontuação baseada em frequência[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)para consultas de texto. No entanto, para consultas de wildcard e regex onde o âmbito de termos pode potencialmente ser amplo, o fator de frequência é ignorado para evitar que o ranking seja tendencioso para jogos de termos mais raros. Todos os jogos são tratados igualmente para pesquisas wildcard e regex.

## <a name="special-characters"></a>Carateres especiais

Em algumas circunstâncias, você pode querer procurar um personagem especial, como um emoji '❤' ou o sinal '€'. Nesses casos, certifique-se de que o analisador que utiliza não filtra esses caracteres. O analisador padrão contorna muitos caracteres especiais, excluindo-os do seu índice.

Os analisadores que irão tokenizar caracteres especiais incluem o analisador "whitespace", que leva em consideração quaisquer sequências de caracteres separadas por espaços brancos como símbolos (de modo que a corda "❤" seria considerada um símbolo). Além disso, um analisador de idiomas como o analisador de inglês da Microsoft ("en.microsoft"), levaria a cadeia "€" como um símbolo. Pode [testar um analisador](/rest/api/searchservice/test-analyzer) para ver que fichas gera para uma determinada consulta.

Ao utilizar caracteres Unicode, certifique-se de que os símbolos são corretamente escapados no url de consulta (por exemplo, para "❤" utilizaria a sequência de `%E2%9D%A4+` fuga). O carteiro faz esta tradução automaticamente.  

## <a name="precedence-grouping"></a>Precedência (agrupamento)

Você pode usar parênteses para criar subqueries, incluindo operadores dentro da declaração parêntesis. Por exemplo, `motel+(wifi|luxury)` procurará documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas coloca o agrupamento num único campo. Por exemplo, `hotelAmenities:(gym+(wifi|pool))` procura no campo "hotelAmenities" para "ginásio" e "wifi", ou "ginásio" e "piscina".  

## <a name="query-size-limits"></a>Limites de tamanho de consulta

Há um limite para o tamanho das consultas que pode enviar para a Pesquisa Cognitiva Azure. Especificamente, pode ter no máximo 1024 cláusulas (expressões separadas por E, OR, e assim por diante). Existe também um limite de aproximadamente 32 KB sobre o tamanho de qualquer termo individual numa consulta. Se a sua aplicação gerar consultas de pesquisa programáticamente, recomendamos que a desenhe de forma a não gerar consultas de tamanho ilimitado.  

## <a name="see-also"></a>Ver também

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para pesquisa completa de Lucene](search-query-lucene-examples.md)
+ [Documentos de Pesquisa](/rest/api/searchservice/Search-Documents)
+ [Sintaxe de expressão OData para filtros e triagem](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples na Pesquisa Cognitiva Azure](query-simple-syntax.md)
