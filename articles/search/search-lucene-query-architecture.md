---
title: Arquitetura do mecanismo de pesquisa de texto completo (Lucene)-Azure Search
description: Explicação do processamento de consultas do Lucene e conceitos de recuperação de documentos para pesquisa de texto completo, conforme relacionado a Azure Search.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: jlembicz
ms.openlocfilehash: 6e54bc91ff60ce4f3c2340282410923225601df4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883897"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Como funciona a pesquisa de texto completo no Azure Search

Este artigo é para os desenvolvedores que precisam de uma compreensão mais profunda de como a pesquisa de texto completo do Lucene funciona em Azure Search. Para consultas de texto, Azure Search fornecerá diretamente os resultados esperados na maioria dos cenários, mas ocasionalmente você poderá obter um resultado que parece "fora" de alguma forma. Nessas situações, ter um plano de fundo nos quatro estágios da execução da consulta Lucene (análise de consulta, análise léxica, correspondência de documentos, pontuação) pode ajudá-lo a identificar alterações específicas em parâmetros de consulta ou configuração de índice que fornecerá o desejado saída. 

> [!Note] 
> Azure Search usa o Lucene para pesquisa de texto completo, mas a integração do Lucene não é exaustiva. Vamos expor seletivamente e estender a funcionalidade Lucene para habilitar os cenários importantes para Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Visão geral e diagrama da arquitetura

O processamento de uma consulta de pesquisa de texto completo começa com a análise do texto da consulta para extrair os termos de pesquisa. O mecanismo de pesquisa usa um índice para recuperar documentos com termos correspondentes. Às vezes, os termos de consulta individuais são divididos e reconstituídos em novos formulários para converter uma rede mais ampla sobre o que poderia ser considerado uma correspondência potencial. Um conjunto de resultados é então classificado por uma pontuação de relevância atribuída a cada documento correspondente individual. Os que estão na parte superior da lista classificada são retornados para o aplicativo de chamada.

Reestado, a execução da consulta tem quatro estágios: 

1. Análise de consulta 
2. Análise léxica 
3. Recuperação de documentos 
4. Classificação 

O diagrama a seguir ilustra os componentes usados para processar uma solicitação de pesquisa. 

 ![Diagrama de arquitetura de consulta Lucene no Azure Search][1]


| Componentes principais | Descrição funcional | 
|----------------|------------------------|
|**Analisadores de consulta** | Separe os termos de consulta dos operadores de consulta e crie a estrutura de consulta (uma árvore de consulta) a ser enviada ao mecanismo de pesquisa. |
|**Analisadores** | Executar análise lexical nos termos da consulta. Esse processo pode envolver a transformação, a remoção ou a expansão de termos de consulta. |
|**Índice** | Uma estrutura de dados eficiente usada para armazenar e organizar os termos pesquisáveis extraídos de documentos indexados. |
|**Mecanismo de pesquisa** | Recupera e classifica documentos correspondentes com base no conteúdo do índice invertido. |

## <a name="anatomy-of-a-search-request"></a>Anatomia de uma solicitação de pesquisa

Uma solicitação de pesquisa é uma especificação completa do que deve ser retornado em um conjunto de resultados. Em forma mais simples, é uma consulta vazia sem nenhum critério de nenhum tipo. Um exemplo mais realista inclui parâmetros, vários termos de consulta, talvez com escopo para determinados campos, com possivelmente uma expressão de filtro e regras de ordenação.  

O exemplo a seguir é uma solicitação de pesquisa que você pode enviar para Azure Search usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

Para essa solicitação, o mecanismo de pesquisa faz o seguinte:

1. Filtra os documentos em que o preço é pelo menos $60 e menor que $300.
2. Executa a consulta. Neste exemplo, a consulta de pesquisa consiste em frases e termos: `"Spacious, air-condition* +\"Ocean view\""` (os usuários normalmente não entram na pontuação, mas incluí-los no exemplo nos permite explicar como os analisadores tratam dele). Para essa consulta, o mecanismo de pesquisa examina os campos de descrição e de `searchFields` título especificados em para documentos que contêm "exibição do oceano" e, além disso, no termo "espaçoso" ou em termos que começam com o prefixo "ar-Condition". O `searchMode` parâmetro é usado para corresponder a qualquer termo (padrão) ou a todos eles, para casos em que um termo não é explicitamente obrigatório`+`().
3. Ordena o conjunto resultante de hotéis por proximidade a um determinado local de Geografia e, em seguida, retornado ao aplicativo de chamada. 

A maior parte deste artigo é sobre o processamento da *consulta*de pesquisa `"Spacious, air-condition* +\"Ocean view\""`:. A filtragem e a ordenação estão fora do escopo. Para obter mais informações, consulte a [documentação de referência da API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Estágio 1: Análise de consulta 

Como observado, a cadeia de caracteres de consulta é a primeira linha da solicitação: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

O analisador de consulta separa operadores ( `*` como e `+` no exemplo) de termos de pesquisa e desconstrói a consulta de pesquisa em subconsultas de um tipo com suporte: 

+ *termo de consulta* para termos autônomos (como espaçoso)
+ *consulta de frase* para termos entre aspas (como exibição do oceano)
+ *consulta de prefixo* para termos seguidos por um `*` operador de prefixo (como Air-Condition)

Para obter uma lista completa de tipos de consulta com suporte, consulte [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Os operadores associados a uma subconsulta determinam se a consulta "deve ser" ou "deve ser" satisfeita para que um documento seja considerado uma correspondência. Por exemplo, `+"Ocean view"` é "deve" devido `+` ao operador. 

O analisador de consulta reestruturará as subconsultas em uma *árvore de consulta* (uma estrutura interna que representa a consulta) que passa para o mecanismo de pesquisa. No primeiro estágio da análise de consulta, a árvore de consulta é parecida com esta.  

 ![Consulta booliana de searchmode][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Analisadores com suporte: Lucene simples e completo 

 Azure Search expõe duas linguagens de consulta `simple` diferentes, (padrão `full`) e. Ao definir o `queryType` parâmetro com sua solicitação de pesquisa, você informa ao analisador de consulta qual linguagem de consulta você escolhe para que ele saiba como interpretar os operadores e a sintaxe. A [linguagem de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) é intuitiva e robusta, geralmente adequada para interpretar a entrada do usuário como está sem o processamento do lado do cliente. Ele dá suporte a operadores de consulta familiares de mecanismos de pesquisa da Web. A [linguagem de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), que você obtém `queryType=full`definindo, estende a linguagem de consulta simples padrão adicionando suporte para mais operadores e tipos de consulta como curinga, difuso, Regex e consultas com escopo de campo. Por exemplo, uma expressão regular enviada na sintaxe de consulta simples seria interpretada como uma cadeia de caracteres de consulta e não uma expressão. A solicitação de exemplo neste artigo usa a linguagem de consulta Lucene completa.

### <a name="impact-of-searchmode-on-the-parser"></a>Impacto do searchmode no analisador 

Outro parâmetro de solicitação de pesquisa que afeta a análise `searchMode` é o parâmetro. Ele controla o operador padrão para consultas booleanas: Any (padrão) ou ALL.  

Quando `searchMode=any`, que é o padrão, o delimitador de espaço entre espaçoso e ar-Condition`||`é ou (), tornando o texto da consulta de exemplo equivalente a: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Operadores explícitos, `+` como in `+"Ocean view"`, não são ambíguos na construção de consulta booliana (o termo *deve* corresponder). Menos óbvio é como interpretar os termos restantes: espaçoso e Air-Condition. O mecanismo de pesquisa deve encontrar correspondências na exibição do oceano *e* espaçoso *e* na condição de ar? Ou ele deve encontrar a exibição do oceano, além de *um* dos termos restantes? 

Por padrão (`searchMode=any`), o mecanismo de pesquisa assume a interpretação mais ampla. Qualquer campo *deve* ser correspondido, refletindo "ou" semânticos. A árvore de consulta inicial ilustrada anteriormente, com as duas operações "deveria", mostra o padrão.  

Suponha que agora definimos `searchMode=all`. Nesse caso, o espaço é interpretado como uma operação "and". Cada um dos termos restantes deve estar presente no documento para ser qualificado como uma correspondência. A consulta de exemplo resultante seria interpretada da seguinte maneira: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Uma árvore de consulta modificada para essa consulta seria a seguinte, em que um documento correspondente é a interseção de todas as três subconsultas: 

 ![Consulta booliana de searchmode tudo][3]

> [!Note] 
> Escolher `searchMode=any` sobre`searchMode=all` o é uma decisão mais bem recebida em executando consultas representativas. Os usuários que provavelmente incluírem operadores (comuns ao pesquisar repositórios de documentos) poderão encontrar resultados mais `searchMode=all` intuitivos se informarem construções de consulta boolianas. Para obter mais informações sobre a Interplay entre `searchMode` operadores e, consulte Sintaxe de [consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Estágio 2: Análise léxica 

Os analisadores léxicos processam consultas de *termo* e *consultas de frase* depois que a árvore de consulta é estruturada. Um analisador aceita as entradas de texto fornecidas pelo analisador, processa o texto e, em seguida, envia os termos de token para serem incorporados à árvore de consulta. 

A forma mais comum de análise lexical é a *análise linguística* que transforma os termos de consulta com base em regras específicas de um determinado idioma: 

* Reduzindo um termo de consulta para a forma raiz de uma palavra 
* Removendo palavras não essenciais (palavras irrelevantes, como "The" ou "and" em inglês) 
* Dividir uma palavra composta em partes de componente 
* Reduzir maiúsculas em uma palavra em maiúsculas 

Todas essas operações tendem a apagar as diferenças entre a entrada de texto fornecida pelo usuário e os termos armazenados no índice. Essas operações vão além do processamento de texto e exigem um conhecimento profundo do próprio idioma. Para adicionar essa camada de conscientização lingüística, Azure Search dá suporte a uma longa lista de analisadores de [linguagem](https://docs.microsoft.com/rest/api/searchservice/language-support) tanto do Lucene quanto da Microsoft.

> [!Note]
> Os requisitos de análise podem variar de mínimo para elaborado, dependendo do seu cenário. Você pode controlar a complexidade da análise lexical selecionando um dos analisadores predefinidos ou criando seu próprio [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Os analisadores têm como escopo os campos pesquisáveis e são especificados como parte de uma definição de campo. Isso permite que você varie a análise lexical em uma base por campo. Não especificado, o analisador Lucene *padrão* é usado.

Em nosso exemplo, antes da análise, a árvore de consulta inicial tem o termo "espaçoso", com um "S" maiúsculo e uma vírgula que o analisador de consulta interpreta como parte do termo da consulta (uma vírgula não é considerada um operador de linguagem de consulta).  

Quando o analisador padrão processa o termo, ele irá minúsculo "exibição do oceano" e "espaçoso" e removerá o caractere de vírgula. A árvore de consulta modificada terá a seguinte aparência: 

 ![Consulta booleana com termos analisados][4]

### <a name="testing-analyzer-behaviors"></a>Comportamentos do analisador de teste 

O comportamento de um analisador pode ser testado usando a [API de análise](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Forneça o texto que você deseja analisar para ver quais termos o analisador irá gerar. Por exemplo, para ver como o analisador padrão processaria o texto "Air-Condition", você pode emitir a seguinte solicitação:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

O analisador padrão quebra o texto de entrada nos dois tokens a seguir, anotando-os com atributos como deslocamentos de início e término (usados para realce de ocorrências), bem como sua posição (usada para correspondência de frase):

~~~~
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Exceções à análise lexical 

A análise lexical aplica-se somente a tipos de consulta que exigem termos completos – uma consulta de termo ou uma consulta de frase. Ele não se aplica a tipos de consulta com termos incompletos – consulta de prefixo, consulta de curinga, consulta de Regex – ou em uma consulta difusa. Esses tipos de consulta, incluindo a consulta de prefixo `air-condition*` com o termo em nosso exemplo, são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada nos termos de consulta desses tipos está em letras minúsculas.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Estágio 3: Recuperação de documentos 

Recuperação de documento refere-se à localização de documentos com termos correspondentes no índice. Esse estágio é compreendido melhor por meio de um exemplo. Vamos começar com um índice de hotéis com o seguinte esquema simples: 

~~~~
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Suponha ainda que esse índice contém os quatro documentos a seguir: 

~~~~
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
~~~~

**Como os termos são indexados**

Para entender a recuperação, é útil saber algumas noções básicas sobre indexação. A unidade de armazenamento é um índice invertido, um para cada campo pesquisável. Dentro de um índice invertido, há uma lista classificada de todos os termos de todos os documentos. Cada termo é mapeado para a lista de documentos em que ele ocorre, como evidente no exemplo abaixo.

Para produzir os termos em um índice invertido, o mecanismo de pesquisa executa uma análise lexical sobre o conteúdo dos documentos, semelhante ao que acontece durante o processamento da consulta:

1. As *entradas de texto* são passadas para um analisador, com letras minúsculas, eliminadas de Pontuação e assim por diante, dependendo da configuração do analisador. 
2. Os tokens são a saída da análise de texto.
3. Os *termos* são adicionados ao índice.

É comum, mas não obrigatório, usar os mesmos analisadores para operações de pesquisa e indexação para que os termos da consulta pareçam mais como termos dentro do índice.

> [!Note]
> Azure Search permite especificar diferentes analisadores para indexação e pesquisa por meio de `indexAnalyzer` parâmetros `searchAnalyzer` de campo e adicionais. Se não for especificado, o analisador definido `analyzer` com a propriedade será usado para indexação e pesquisa.  

**Índice invertido para documentos de exemplo**

Retornando ao nosso exemplo, para o campo **título** , o índice invertido tem esta aparência:

| Termo | Lista de documentos |
|------|---------------|
| atman | 1 |
| Beach | 2 |
| Hotel | 1, 3 |
| oceano | 4  |
| playa | 3 |
| última | 3 |
| retirada | 4 |

No campo título, somente o *Hotel* aparece em dois documentos: 1, 3.

Para o campo **Descrição** , o índice é o seguinte:

| Termo | Lista de documentos |
|------|---------------|
| ar | 3
| e | 4
| Beach | 1
| condicionado | 3
| confortável | 3
| distance | 1
| Ilha | 2
| kauaʻi | 2
| localizado | 2
| Unidos | 2
| oceano | 1, 2, 3
| de | 2
| em |2
| tranqüilo | 4
| salas  | 1, 3
| secluded | 4
| reforçá | 2
| espaçoso | 1
| o | 1, 2
| para | 1
| ver | 1, 2, 3
| vivos | 1
| com | 3


**Termos de consulta correspondentes em termos indexados**

Considerando os índices invertidos acima, vamos voltar para a consulta de exemplo e ver como os documentos correspondentes são encontrados para nossa consulta de exemplo. Lembre-se de que a árvore de consulta final tem esta aparência: 

 ![Consulta booleana com termos analisados][4]

Durante a execução da consulta, as consultas individuais são executadas em relação aos campos pesquisáveis de forma independente. 

+ O TermQuery, "espaçoso", corresponde ao documento 1 (Hotel Atman). 

+ O consulta, "Air-Condition *", não corresponde a nenhum documento. 

  Esse é um comportamento que, às vezes, confundi os desenvolvedores. Embora o termo ar-condicionado exista no documento, ele é dividido em dois termos pelo analisador padrão. Lembre-se de que as consultas de prefixo, que contêm termos parciais, não são analisadas. Portanto, os termos com o prefixo "ar-Condition" são pesquisados no índice invertido e não são encontrados.

+ O consulta, "exibição do oceano", pesquisa os termos "oceano" e "exibição" e verifica a proximidade dos termos no documento original. Os documentos 1, 2 e 3 correspondem a essa consulta no campo Descrição. Observe que o documento 4 tem o termo oceano no título, mas não é considerado uma correspondência, pois estamos procurando a frase "exibição do oceano" em vez de palavras individuais. 

> [!Note]
> Uma consulta de pesquisa é executada de forma independente em todos os campos pesquisáveis no índice Azure Search, a menos que você `searchFields` limite os campos definidos com o parâmetro, conforme ilustrado na solicitação de pesquisa de exemplo. Os documentos que correspondem em qualquer um dos campos selecionados são retornados. 

No todo, para a consulta em questão, os documentos que correspondem são 1, 2, 3. 

## <a name="stage-4-scoring"></a>Estágio 4: Classificação  

Cada documento em um conjunto de resultados de pesquisa recebe uma pontuação de relevância. A função da Pontuação de relevância é classificar os documentos mais altos que melhor respondem a uma pergunta de usuário, conforme expresso pela consulta de pesquisa. A pontuação é computada com base nas propriedades estatísticas dos termos correspondentes. No núcleo da fórmula de pontuação está [TF/IDF (frequência de termo-frequência de documento inversa)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Em consultas que contêm termos raros e comuns, TF/IDF promove os resultados que contêm o termo raro. Por exemplo, em um índice hipotético com todos os artigos da Wikipédia, de documentos que corresponderam à consulta *o Presidente, os*documentos que correspondem ao *Presidente* são considerados mais relevantes do que *os*documentos que correspondem no.


### <a name="scoring-example"></a>Exemplo de Pontuação

Lembre-se dos três documentos que corresponderam à nossa consulta de exemplo:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

O documento 1 correspondia à consulta melhor porque o termo *espaçoso* e a exibição do *oceano* de frase necessária ocorrem no campo Descrição. Os próximos dois documentos correspondem apenas à *exibição do oceano*da frase. Pode ser surpreendente que a pontuação de relevância para o documento 2 e 3 seja diferente, mesmo que corresponda à consulta da mesma maneira. É porque a fórmula de Pontuação tem mais componentes do que apenas TF/IDF. Nesse caso, o documento 3 recebeu uma pontuação um pouco mais alta porque sua descrição é mais curta. Saiba mais sobre a [fórmula de Pontuação prática do Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) para entender como o tamanho do campo e outros fatores podem influenciar a pontuação de relevância.

Alguns tipos de consulta (curinga, prefixo, Regex) sempre contribuem com uma pontuação constante para a pontuação geral do documento. Isso permite que as correspondências encontradas por meio da expansão de consulta sejam incluídas nos resultados, mas sem afetar a classificação. 

Um exemplo ilustra por que isso é importante. Pesquisas de curinga, incluindo pesquisas de prefixo, são ambíguas por definição porque a entrada é uma cadeia de caracteres parcial com correspondências potenciais em um número muito grande de termos diferentes (considere uma entrada de "Tour *", com correspondências encontradas em "Tours", "turrão" e " passarela "). Devido à natureza desses resultados, não há como inferir de forma razoável quais termos são mais valiosos do que outros. Por esse motivo, ignoramos as frequências de termo quando a pontuação resulta em consultas de tipos curinga, prefixo e Regex. Em uma solicitação de pesquisa de várias partes que inclui termos parciais e completos, os resultados da entrada parcial são incorporados com uma pontuação constante para evitar a tendência em relação a correspondências potencialmente inesperadas.

### <a name="score-tuning"></a>Ajuste de Pontuação

Há duas maneiras de ajustar as pontuações de relevância em Azure Search:

1. Os **perfis de Pontuação** promovem documentos na lista classificada de resultados com base em um conjunto de regras. Em nosso exemplo, podemos considerar documentos que corresponderam ao campo título mais relevante do que documentos que corresponderam ao campo Descrição. Além disso, se nosso índice tivesse um campo de preço para cada hotel, poderíamos promover documentos com preço menor. Saiba mais sobre como [adicionar perfis de Pontuação a um índice de pesquisa.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Aumento de termo** (disponível somente na sintaxe de consulta Lucene completa) fornece um operador `^` de aumento que pode ser aplicado a qualquer parte da árvore de consulta. No nosso exemplo, em vez de procurar no prefixo *air-condition*\*, um pode pesquisar por qualquer o termo exato *air-condition* ou o prefixo, mas os documentos que correspondem o termo exato são com a classificação superior ao aplicar boost à consulta de termo: *condição de ar ^ 2 | | Air-condition* *. Saiba mais sobre o [aumento de termo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Pontuação em um índice distribuído

Todos os índices no Azure Search são divididos automaticamente em vários fragmentos, permitindo que possamos distribuir rapidamente o índice entre vários nós durante o escalonamento ou redução vertical do serviço. Quando uma solicitação de pesquisa é emitida, ela é emitida em cada fragmento independentemente. Os resultados de cada fragmento são mesclados e ordenados por Pontuação (se nenhuma outra ordem for definida). É importante saber que a função de Pontuação pondera a frequência do termo de consulta em relação à sua frequência de documento inversa em todos os documentos dentro do fragmento, não em todos os fragmentos!

Isso significa que uma pontuação de relevância *pode* ser diferente para documentos idênticos se eles residirem em fragmentos diferentes. Felizmente, essas diferenças tendem a desaparecer à medida que o número de documentos no índice cresce devido a uma distribuição de termo mais uniforme. Não é possível supor em qual fragmento qualquer documento em questão será colocado. No entanto, supondo que uma chave de documento não seja alterada, ela sempre será atribuída ao mesmo fragmento.

Em geral, a Pontuação do documento não é o melhor atributo para ordenar documentos se a estabilidade do pedido for importante. Por exemplo, considerando dois documentos com uma pontuação idêntica, não há nenhuma garantia de qual deles aparece primeiro nas execuções subsequentes da mesma consulta. A Pontuação do documento deve dar um sentido geral à relevância do documento em relação a outros documentos no conjunto de resultados.

## <a name="conclusion"></a>Conclusão

O sucesso dos mecanismos de pesquisa da Internet gerou expectativas de pesquisa de texto completo sobre dados privados. Para praticamente qualquer tipo de experiência de pesquisa, agora esperamos que o mecanismo entenda nossa intenção, mesmo quando os termos são digitados incorretamente ou incompletos. Podemos até esperar correspondências com base em termos equivalentes ou sinônimos que nunca especificamos de fato.

Do ponto de vista técnico, a pesquisa de texto completo é altamente complexa, exigindo uma análise linguística sofisticada e uma abordagem sistemática para o processamento de maneiras que extraem, expandem e transformem termos de consulta para fornecer um resultado relevante. Considerando as complexidades inerentes, há muitos fatores que podem afetar o resultado de uma consulta. Por esse motivo, investir tempo para entender a mecânica da pesquisa de texto completo oferece benefícios tangíveis ao tentar trabalhar com resultados inesperados.  

Este artigo explorou a pesquisa de texto completo no contexto de Azure Search. Esperamos que ele forneça um plano de fundo suficiente para reconhecer possíveis causas e resoluções para resolver problemas comuns de consulta. 

## <a name="next-steps"></a>Passos Seguintes

+ Crie o índice de exemplo, experimente consultas diferentes e examine os resultados. Para obter instruções, consulte [criar e consultar um índice no portal](search-get-started-portal.md#query-index).

+ Tente sintaxe de consulta adicional na seção de exemplo [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) ou na [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) no Gerenciador de pesquisa no Portal.

+ Revise os [perfis de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se desejar ajustar a classificação em seu aplicativo de pesquisa.

+ Saiba como aplicar [analisadores léxicos específicos a um idioma](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configure analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para o processamento mínimo ou processamento especializado em campos específicos.

## <a name="see-also"></a>Consulte também

[Pesquisar API REST de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Processar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
