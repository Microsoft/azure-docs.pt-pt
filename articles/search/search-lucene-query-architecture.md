---
title: Consulta de texto completa e arquitetura de motor de indexação (Lucene)
titleSuffix: Azure Cognitive Search
description: Examina conceitos de processamento de consulta lucene e de recuperação de documentos para pesquisa completa de texto, relacionadacom a Pesquisa Cognitiva Azure.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d46d0309b3d2ffb638016e88ba022e49009eedf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282942"
---
# <a name="how-full-text-search-works-in-azure-cognitive-search"></a>Como funciona a pesquisa em texto completo no Azure Cognitive Search

Este artigo é para desenvolvedores que precisam de uma compreensão mais profunda de como lucene full text search funciona em Azure Cognitive Search. Para consultas de texto, o Azure Cognitive Search fornecerá diretamente os resultados esperados na maioria dos cenários, mas ocasionalmente pode obter um resultado que parece “desadequado” de alguma forma. Nestas situações, conhecer as quatro fases da execução de consultas Lucene (análise de consultas, análise léxica, correspondência de documentos e classificação) pode ajudá-lo a identificar alterações específicas nos parâmetros de consulta ou na configuração do índice que fornecerá o resultado desejado. 

> [!Note] 
> A Azure Cognitive Search usa lucene para pesquisa completa de texto, mas a integração lucene não é exaustiva. Expomos e ampliamos seletivamente a funcionalidade Lucene para permitir os cenários importantes para a Pesquisa Cognitiva Azure. 

## <a name="architecture-overview-and-diagram"></a>Visão geral da arquitetura e diagrama

O processamento de uma consulta completa de pesquisa de texto começa com a análise do texto de consulta para extrair termos de pesquisa. O motor de busca usa um índice para recuperar documentos com termos correspondentes. Os termos de consulta individuais são por vezes divididos e reconstituídos em novas formas para lançar uma rede mais ampla sobre o que poderia ser considerado como um potencial jogo. Um conjunto de resultados é então classificado por uma pontuação de relevância atribuída a cada documento de correspondência individual. Os que estão no topo da lista são devolvidos ao pedido de chamada.

Reafirmada, a execução da consulta tem quatro fases: 

1. Consulta de análise 
2. Análise lexical 
3. Recuperação de documentos 
4. Classificação 

O diagrama abaixo ilustra os componentes utilizados para processar um pedido de pesquisa. 

 ![Diagrama de arquitetura de consulta lucene em Pesquisa Cognitiva Azure][1]


| Componentes principais | Descrição funcional | 
|----------------|------------------------|
|**Parsers de consultas** | Separe os termos de consulta dos operadores de consulta e crie a estrutura de consulta (uma árvore de consulta) a enviar para o motor de busca. |
|**Analisadores** | Realizar análises lexical em termos de consulta. Este processo pode envolver a transformação, remoção ou expansão de termos de consulta. |
|**Índice** | Uma estrutura de dados eficiente usada para armazenar e organizar termos pesquisáveis extraídos de documentos indexados. |
|**Motor de busca** | Recupera e pontua documentos correspondentes com base no conteúdo do índice invertido. |

## <a name="anatomy-of-a-search-request"></a>Anatomia de um pedido de pesquisa

Um pedido de pesquisa é uma especificação completa do que deve ser devolvido num conjunto de resultados. Na forma mais simples, é uma consulta vazia sem critérios de qualquer tipo. Um exemplo mais realista inclui parâmetros, vários termos de consulta, talvez telescópios para determinados campos, com possivelmente uma expressão de filtro e regras de ordenação.  

O exemplo seguinte é um pedido de pesquisa que pode enviar para A Pesquisa Cognitiva Azure usando a [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

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

Para este pedido, o motor de busca faz o seguinte:

1. Filtra documentos onde o preço é de pelo menos $60 e menos de $300.
2. Executa a consulta. Neste exemplo, a consulta de pesquisa consiste em `"Spacious, air-condition* +\"Ocean view\""` frases e termos: (os utilizadores normalmente não entram na pontuação, mas incluí-lo no exemplo permite-nos explicar como os analisadores lidam com isso). Para esta consulta, o motor de busca digitaliza a `searchFields` descrição e os campos de títuloespecificados para documentos que contenham "vista para o oceano", e adicionalmente sobre o termo "espaçoso", ou em termos que começam com o prefixo "ar condicionado". O `searchMode` parâmetro é utilizado para corresponder a qualquer termo (padrão) ou todos eles, para`+`casos em que um termo não seja explicitamente exigido ( ).
3. Encomenda o conjunto resultante de hotéis por proximidade a uma determinada localização geográfica, e depois volta à aplicação de chamada. 

A maior parte deste artigo é sobre o `"Spacious, air-condition* +\"Ocean view\""`processamento da consulta de *pesquisa*: . Filtragem e encomenda estão fora de alcance. Para mais informações, consulte a documentação de referência da [API de pesquisa.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fase 1: Consulta de parsing 

Como notado, a corda de consulta é a primeira linha do pedido: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

O parser de consulta separa `*` `+` os operadores (como e no exemplo) dos termos de pesquisa, e desconstrói a consulta de pesquisa em *subqueimas* de um tipo suportado: 

+ *consulta de termo* para termos autónomos (como espaçoso)
+ *consulta de frase* para termos citados (como vista para o mar)
+ *pré-fixação consulta* para termos seguidos por um operador `*` de prefixo (como ar condicionado)

Para uma lista completa de tipos de consulta suportada consulte [Lucene consulta sintaxe](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Os operadores associados a um subqueríaco determinam se a consulta "deve ser" ou "deve ser" satisfeita para que um documento seja considerado compatível. Por exemplo, `+"Ocean view"` é "deve" `+` devido ao operador. 

O parser de consulta reestrutura os subqueries em uma *árvore de consulta* (uma estrutura interna que representa a consulta) que passa para o motor de busca. Na primeira fase da consulta, a árvore de consulta é assim.  

 ![Boolean consulta searchmode qualquer][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Parsers suportados: Lucene simples e completo 

 A Pesquisa Cognitiva Azure expõe duas `simple` línguas de `full`consulta diferentes, (padrão) e . Ao definir `queryType` o parâmetro com o seu pedido de pesquisa, informe o parser de consulta que escolhe para que saiba interpretar os operadores e a sintaxe. A [linguagem de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) é intuitiva e robusta, muitas vezes adequada para interpretar a entrada do utilizador como está sem processamento do lado do cliente. Suporta operadores de consulta familiarizados com motores de pesquisa web. A linguagem de [consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) `queryType=full`, que obtém por definição , alarga a linguagem simples padrão, adicionando suporte a mais operadores e tipos de consultas como wildcard, fuzzy, regex e consultas de campo. Por exemplo, uma expressão regular enviada em simples sintaxe de consulta seria interpretada como uma corda de consulta e não uma expressão. O pedido de exemplo neste artigo usa a linguagem de consulta lucene completa.

### <a name="impact-of-searchmode-on-the-parser"></a>Impacto do modo de pesquisa no parser 

Outro parâmetro de pedido de pesquisa que `searchMode` afeta a análise é o parâmetro. Controla o operador predefinido para consultas booleanas: qualquer (padrão) ou tudo.  

Quando, `searchMode=any`que é o padrão, o espaço delimitador entre`||`espaçoso e ar condicionado é OR ( ), tornando o texto de consulta da amostra equivalente a: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Os operadores `+` explícitos, como o in, `+"Ocean view"`são inequívocos na construção de consultas booleanas (o termo *deve* coincidir). Menos óbvio é como interpretar os restantes termos: espaçoso e ar condicionado. O motor de busca deve encontrar fósforos na vista para o mar *e* espaçoso *e* ar condicionado? Ou deve encontrar vista para o mar mais *qualquer um* dos termos restantes? 

Por predefinição ,`searchMode=any`o motor de busca assume uma interpretação mais ampla. Qualquer um dos campos *deve* ser combinado, refletindo semântica "ou". A árvore de consulta inicial ilustrada anteriormente, com as duas operações "deve", mostra o padrão.  

Suponha que `searchMode=all`agora definimos. Neste caso, o espaço é interpretado como uma operação de "e" operação. Cada um dos restantes termos deve estar presente no documento para se qualificar como compatível. A consulta de amostra resultante seria interpretada da seguinte forma: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Uma árvore de consulta modificada para esta consulta seria a seguinte, onde um documento correspondente é a intersecção dos três subqueries: 

 ![Boolean consulta searchmode todos][3]

> [!Note] 
> Escolher `searchMode=any` é `searchMode=all` uma decisão melhor tomada por perguntas representativas. Os utilizadores que possam incluir operadores (comuns ao pesquisar `searchMode=all` lojas de documentos) podem encontrar resultados mais intuitivos se informarem as construções de consultas booleanas. Para saber mais sobre `searchMode` a interação entre e operadores, consulte a [simples sintaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)de consulta.

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fase 2: Análise lexical 

Os analisadores lexical processam consultas de *termo* e consultas de *frase* sem que a árvore de consulta seja estruturada. Um analisador aceita as inputs de texto que lhe foram dadas pelo parser, processa o texto e, em seguida, envia de volta termos tokenizados para ser incorporado na árvore de consulta. 

A forma mais comum de análise lexical é a *análise linguística* que transforma termos de consulta com base em regras específicas de uma determinada língua: 

* Reduzindo um termo de consulta à forma raiz de uma palavra 
* Remoção de palavras não essenciais (cronómetros, como "o" ou "e" em inglês) 
* Quebrar uma palavra composta em componentes 
* Baixar o invólucro uma palavra maiúscula 

Todas estas operações tendem a apagar diferenças entre a entrada de texto fornecida pelo utilizador e os termos armazenados no índice. Estas operações vão além do processamento de texto e exigem um conhecimento aprofundado da própria língua. Para adicionar esta camada de consciência linguística, a Azure Cognitive Search suporta uma longa lista de [analisadores](https://docs.microsoft.com/rest/api/searchservice/language-support) de linguagem tanto da Lucene como da Microsoft.

> [!Note]
> Os requisitos de análise podem variar de mínimos a elaborados dependendo do seu cenário. Pode controlar a complexidade da análise lexical selecionando um dos analisadores predefinidos ou criando o seu próprio [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Os analisadores são analisados para campos pesquisáveis e são especificados como parte de uma definição de campo. Isto permite-lhe variar a análise lexical numa base por campo. Não especificado, o analisador *lucene padrão* é usado.

No nosso exemplo, antes da análise, a consulta inicial tem o termo "Espaçoso", com um "S" maiúsculo e uma vírem que o parser de consulta interpreta como parte do termo de consulta (uma vírem não é considerada um operador de linguagem consulta).  

Quando o analisador padrão processa o termo, irá diminuir "vista para o oceano" e "espaçoso", e remover o caráter da vírcula. A árvore de consulta modificada será a seguinte: 

 ![Consulta booleana com termos analisados][4]

### <a name="testing-analyzer-behaviors"></a>Testar comportamentos de analisador 

O comportamento de um analisador pode ser testado usando a [API de análise.](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) Forneça o texto que pretende analisar para ver quais os termos que o analisador irá gerar. Por exemplo, para ver como o analisador padrão processaria o texto "ar condicionado", pode emitir o seguinte pedido:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

O analisador padrão quebra o texto de entrada nas duas fichas seguintes, anotando-os com atributos como compensações de início e de fim (usadas para realçar o impacto) bem como a sua posição (usada para combinar frases):

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

A análise lexical aplica-se apenas a tipos de consulta que requerem termos completos – seja uma consulta de termo ou uma consulta de frase. Não se aplica a tipos de consulta com termos incompletos – consulta prefixo, consulta de wildcard, consulta de regex – ou a uma consulta confusa. Esses tipos de consulta, incluindo a `air-condition*` consulta prefixo com termo no nosso exemplo, são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta deste tipo é a redução.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fase 3: Recuperação de documentos 

A recuperação de documentos refere-se à procura de documentos com termos correspondentes no índice. Esta fase é melhor entendida através de um exemplo. Vamos começar com um índice de hotéis com o seguinte esquema simples: 

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

Partam ainda do princípio de que este índice contém os seguintes quatro documentos: 

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

Para compreender a recuperação, ajuda a saber alguns princípios básicos sobre indexação. A unidade de armazenamento é um índice invertido, um para cada campo pesquisável. Dentro de um índice invertido está uma lista ordenada de todos os termos de todos os documentos. Cada termo mapeia a lista de documentos em que ocorre, como evidente no exemplo abaixo.

Para produzir os termos num índice invertido, o motor de busca realiza análises lexical sobre o conteúdo dos documentos, semelhantes ao que acontece durante o processamento de consultas:

1. *As inputs* de texto são passadas para um analisador, minúsculas, despojadas de pontuação, e assim por diante, dependendo da configuração do analisador. 
2. *Tokens* são a saída da análise de texto.
3. *Os termos* são adicionados ao índice.

É comum, mas não necessário, usar os mesmos analisadores para operações de pesquisa e indexação para que os termos de consulta se pareçam mais com termos dentro do índice.

> [!Note]
> A Pesquisa Cognitiva Azure permite especificar diferentes analisadores para indexação e pesquisa através de parâmetros adicionais `indexAnalyzer` e `searchAnalyzer` de campo. Se não especificado, o `analyzer` conjunto de analisador com a propriedade é utilizado tanto para indexação como para pesquisa.  

**Índice invertido, por exemplo, documentos**

Voltando ao nosso exemplo, para o campo do **título,** o índice invertido é assim:

| Termo | Lista de documentos |
|------|---------------|
| atman | 1 |
| praia | 2 |
| hotel | 1, 3 |
| oceano | 4  |
| playa | 3 |
| resort | 3 |
| retiro | 4 |

Na área do título, apenas o *hotel* aparece em dois documentos: 1, 3.

Para o campo **de descrição,** o índice é o seguinte:

| Termo | Lista de documentos |
|------|---------------|
| ar | 3
| e | 4
| praia | 1
| condicionado | 3
| confortável | 3
| distância | 1
| ilha | 2
| kauaėi | 2
| localizado | 2
| norte | 2
| oceano | 1, 2, 3
| de | 2
| em |2
| tranquilo | 4
| quartos  | 1, 3
| isolado | 4
| costa | 2
| espaçoso | 1
| o | 1, 2
| para | 1
| ver | 1, 2, 3
| andar | 1
| com o | 3


**Termos de consulta correspondentes contra termos indexados**

Tendo em conta os índices invertidos acima, vamos voltar à consulta da amostra e ver como os documentos correspondentes são encontrados para a nossa consulta de exemplo. Lembre-se que a árvore de consulta final é assim: 

 ![Consulta booleana com termos analisados][4]

Durante a execução da consulta, as consultas individuais são executadas contra os campos pesquisáveis de forma independente. 

+ O TermQuery, "espaçoso", corresponde ao documento 1 (Hotel Atman). 

+ O Prefixo, "ar condicionado*", não corresponde a nenhum documento. 

  Este é um comportamento que às vezes confunde os desenvolvedores. Embora o termo ar-condicionado exista no documento, é dividido em dois termos pelo analisador padrão. Lembre-se que as consultas prefixos, que contêm termos parciais, não são analisadas. Portanto, os termos com prefixo "ar condicionado" são consultados no índice invertido e não encontrados.

+ O PhraseQuery, "vista para o mar", olha para os termos "oceano" e "visão" e verifica a proximidade dos termos no documento original. Os documentos 1, 2 e 3 correspondem a esta consulta no campo de descrição. O documento de aviso 4 tem o termo oceano no título, mas não é considerado compatível, pois estamos à procura da frase "vista para o mar" em vez de palavras individuais. 

> [!Note]
> Uma consulta de pesquisa é executada independentemente contra todos os campos pesquisáveis `searchFields` no índice de Pesquisa Cognitiva Azure, a menos que limite os campos definidos com o parâmetro, como ilustrado no pedido de pesquisa de exemplo. Documentos que correspondam em qualquer um dos campos selecionados são devolvidos. 

No geral, para a consulta em questão, os documentos que coincidem são 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fase 4: Pontuação  

Cada documento num conjunto de resultados de pesquisa é atribuído a uma pontuação de relevância. A função da pontuação de relevância é classificar mais alto os documentos que melhor respondem a uma pergunta do utilizador, tal como expressa pela consulta de pesquisa. A pontuação é calculada com base em propriedades estatísticas de termos que correspondem. No cerne da fórmula de pontuação está [TF/IDF (frequência de documento inverso de frequência)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Em consultas que contenham termos raros e comuns, a TF/IDF promove resultados que contenham o termo raro. Por exemplo, num índice hipotético com todos os artigos da Wikipédia, a partir de documentos que correspondam à consulta *do presidente,* os documentos correspondentes ao *presidente* são considerados mais relevantes do que documentos correspondentes *ao*.


### <a name="scoring-example"></a>Exemplo de pontuação

Lembre-se dos três documentos que correspondem à nossa consulta de exemplo:
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

O documento 1 correspondia melhor à consulta porque tanto o termo *espaçoso* como a frase necessária para o *oceano* ocorrem no campo de descrição. Os próximos dois documentos correspondem apenas à frase vista para o *oceano.* Pode ser surpreendente que a pontuação de relevância para os documentos 2 e 3 seja diferente, apesar de terem igualado a consulta da mesma forma. É porque a fórmula de pontuação tem mais componentes do que apenas TF/IDF. Neste caso, o documento 3 foi atribuído a uma pontuação ligeiramente superior porque a sua descrição é mais curta. Saiba mais sobre a [Fórmula prática de pontuação de Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) para entender como o comprimento do campo e outros fatores podem influenciar a pontuação de relevância.

Alguns tipos de consultas (wildcard, prefixo, regex) sempre contribuem com uma pontuação constante para a pontuação geral do documento. Isto permite que os fósforos encontrados através da expansão da consulta sejam incluídos nos resultados, mas sem afetar o ranking. 

Um exemplo ilustra porque é que isto importa. As pesquisas wildcard, incluindo pesquisas prefixos, são ambíguas por definição porque a entrada é uma cadeia parcial com potenciais fósforos em um número muito grande de termos díspares (considere uma entrada de "tour*", com fósforos encontrados em "tours", "tourettes" e "tourmaline"). Dada a natureza destes resultados, não há forma de inferir razoavelmente quais os termos mais valiosos do que outros. Por esta razão, ignoramos as frequências de termo quando a pontuação resulta em consultas de tipos wildcard, prefixo e regex. Num pedido de pesquisa em várias partes que inclui termos parciais e completos, os resultados da entrada parcial são incorporados com uma pontuação constante para evitar distorções para partidas potencialmente inesperadas.

### <a name="score-tuning"></a>Afinação de pontuação

Existem duas formas de afinar pontuações de relevância na Pesquisa Cognitiva De Azure:

1. Os perfis de pontuação promovem **documentos** na lista de resultados classificados com base num conjunto de regras. No nosso exemplo, poderíamos considerar documentos que correspondessem no campo do título mais relevantes do que os documentos que correspondiam no campo de descrição. Além disso, se o nosso índice tivesse um preço para cada hotel, poderíamos promover documentos com um preço mais baixo. Saiba mais como [adicionar Perfis de Pontuação a um índice de pesquisa.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **O aumento de prazos** (disponível apenas na sintaxe de `^` consulta Lucene completa) fornece um operador impulsionador que pode ser aplicado a qualquer parte da árvore de consulta. No nosso exemplo, em vez de pesquisar o *ar condicionado*\*prefixo, pode-se pesquisar o termo exato *ar condicionado* ou o prefixo, mas os documentos que correspondem ao termo exato são classificados mais alto aplicando o impulso ao termo consulta: *ar-condicionado^2[/ ar condicionado**. Saiba mais sobre [o aumento de termos.](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost)


### <a name="scoring-in-a-distributed-index"></a>Pontuação num índice distribuído

Todos os índices em Pesquisa Cognitiva Azure são automaticamente divididos em vários fragmentos, permitindo-nos distribuir rapidamente o índice entre vários nós durante a escala de serviço para cima ou para baixo. Quando um pedido de pesquisa é emitido, é emitido contra cada fragmento independentemente. Os resultados de cada fragmento são então fundidos e encomendados por pontuação (se nenhuma outra encomenda for definida). É importante saber que a função de pontuação pondera a frequência de prazo de consulta contra a sua frequência de documento inverso em todos os documentos dentro do fragmento, e não em todos os fragmentos!

Isto significa que uma pontuação de relevância *pode* ser diferente para documentos idênticos se residirem em fragmentos diferentes. Felizmente, tais diferenças tendem a desaparecer à medida que o número de documentos no índice cresce devido à distribuição de prazos mais uniforme. Não é possível assumir em que fragmento qualquer documento será colocado. No entanto, assumindo que uma chave de documentos não muda, será sempre atribuída ao mesmo fragmento.

Em geral, a pontuação do documento não é o melhor atributo para encomendar documentos se a estabilidade da ordem for importante. Por exemplo, tendo em conta dois documentos com uma pontuação idêntica, não há garantia de que se apareça primeiro em sequências da mesma consulta. A pontuação do documento só deve dar uma sensação geral de relevância documental em relação a outros documentos no conjunto de resultados.

## <a name="conclusion"></a>Conclusão

O sucesso dos motores de pesquisa de internet suscitou expectativas de uma pesquisa completa de texto sobre dados privados. Para quase qualquer tipo de experiência de pesquisa, esperamos agora que o motor compreenda a nossa intenção, mesmo quando os termos são mal escritos ou incompletos. Podemos até esperar fósforos baseados em termos quase equivalentes ou sinónimos que nunca especificámos.

Do ponto de vista técnico, a pesquisa completa de texto é altamente complexa, requerendo uma análise linguística sofisticada e uma abordagem sistemática ao processamento de formas que destilam, expandem e transformam termos de consulta para obter um resultado relevante. Dadas as complexidades inerentes, há muitos fatores que podem afetar o resultado de uma consulta. Por esta razão, investir o tempo para entender a mecânica da pesquisa completa de texto oferece benefícios tangíveis ao tentar trabalhar através de resultados inesperados.  

Este artigo explorou a pesquisa completa de texto no contexto da Pesquisa Cognitiva Azure. Esperamos que lhe dê antecedentes suficientes para reconhecer potenciais causas e resoluções para resolver problemas comuns de consulta. 

## <a name="next-steps"></a>Passos seguintes

+ Construa o índice de amostra, experimente diferentes consultas e reveja os resultados. Para obter instruções, consulte [Construir e consultar um índice no portal](search-get-started-portal.md#query-index).

+ Experimente sintaxe adicional de consulta da secção de exemplo de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) de Pesquisa ou de [sintaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) de consulta simples no explorador de pesquisa no portal.

+ Reveja os perfis de [pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se quiser afinar o ranking na sua aplicação de pesquisa.

+ Aprenda a aplicar [analisadores lexicais específicos da linguagem](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configure os analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para um processamento mínimo ou processamento especializado em áreas específicas.

## <a name="see-also"></a>Consulte também

[Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST) 

[Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Sintaxe de consulta Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Processar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
