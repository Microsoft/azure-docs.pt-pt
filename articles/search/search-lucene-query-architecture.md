---
title: Consulta completa de texto e arquitetura de motor de indexação (Lucene)
titleSuffix: Azure Cognitive Search
description: Examina os conceitos de processamento de consultas de Lucene e de recuperação de documentos para pesquisa completa de texto, relacionados com a Pesquisa Cognitiva de Azure.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002753"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Pesquisa de texto completo em Azure Cognitive Search

Este artigo destina-se a desenvolvedores que precisam de uma compreensão mais profunda de como lucene pesquisa completa de texto funciona em Azure Cognitive Search. Para consultas de texto, o Azure Cognitive Search fornecerá diretamente os resultados esperados na maioria dos cenários, mas ocasionalmente pode obter um resultado que parece “desadequado” de alguma forma. Nestas situações, conhecer as quatro fases da execução de consultas Lucene (análise de consultas, análise léxica, correspondência de documentos e classificação) pode ajudá-lo a identificar alterações específicas nos parâmetros de consulta ou na configuração do índice que fornecerá o resultado desejado. 

> [!Note] 
> A Azure Cognitive Search usa Lucene para pesquisa completa de texto, mas a integração de Lucene não é exaustiva. Expomos e alargamos seletivamente a funcionalidade Lucene para permitir os cenários importantes para a Pesquisa Cognitiva do Azure. 

## <a name="architecture-overview-and-diagram"></a>Visão geral da arquitetura e diagrama

O processamento de uma consulta completa de pesquisa por texto começa com a análise do texto de consulta para extrair termos de pesquisa. O motor de busca usa um índice para recuperar documentos com termos correspondentes. Os termos de consulta individual são por vezes divididos e reconstituídos em novas formas para lançar uma rede mais ampla sobre o que poderia ser considerado como um jogo potencial. Um conjunto de resultados é então classificado por uma pontuação de relevância atribuída a cada documento de correspondência individual. Os que estão no topo da lista são devolvidos ao pedido de chamada.

Reativada, a execução de consulta tem quatro fases: 

1. Análise de consulta 
2. Análise lexical 
3. Recuperação de documentos 
4. Classificação 

O diagrama abaixo ilustra os componentes utilizados para processar um pedido de pesquisa. 

 ![Diagrama de arquitetura de consulta lucene em Pesquisa Cognitiva Azure][1]


| Componentes principais | Descrição funcional | 
|----------------|------------------------|
|**Parsers de consulta** | Separe os termos de consulta dos operadores de consulta e crie a estrutura de consulta (uma árvore de consulta) a enviar para o motor de busca. |
|**Analisadores** | Realizar análise lexical em termos de consulta. Este processo pode envolver a transformação, remoção ou expansão dos termos de consulta. |
|**Índice** | Uma estrutura de dados eficiente utilizada para armazenar e organizar termos pes pesjáveis extraídos de documentos indexados. |
|**Motor de pesquisa** | Recupera e pontua os documentos correspondentes com base no conteúdo do índice invertido. |

## <a name="anatomy-of-a-search-request"></a>Anatomia de um pedido de pesquisa

Um pedido de pesquisa é uma especificação completa do que deve ser devolvido num conjunto de resultados. Na forma mais simples, é uma consulta vazia sem critérios de qualquer tipo. Um exemplo mais realista inclui parâmetros, vários termos de consulta, talvez alargados a determinados campos, com possivelmente uma expressão de filtro e regras de encomenda.  

O exemplo a seguir é um pedido de pesquisa que pode enviar para a Azure Cognitive Search utilizando a [API REST](/rest/api/searchservice/search-documents).  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

Para este pedido, o motor de busca faz o seguinte:

1. Filtra documentos onde o preço é pelo menos $60 e menos de $300.
2. Executa a consulta. Neste exemplo, a consulta de pesquisa consiste em frases e termos: `"Spacious, air-condition* +\"Ocean view\""` (os utilizadores normalmente não entram na pontuação, mas incluí-lo no exemplo permite-nos explicar como os analisadores lidam com isso). Para esta consulta, o motor de busca analisa a descrição e os campos de títulos especificados para `searchFields` documentos que contenham "vista para o mar", e adicionalmente no termo "espaçoso", ou em termos que começam com o prefixo "ar-condicionado". O `searchMode` parâmetro é utilizado para corresponder a qualquer termo (padrão) ou a todos eles, para casos em que um termo não é explicitamente necessário ( `+` ).
3. Encomenda o conjunto resultante de hotéis por proximidade a um determinado local de geografia, e depois voltou ao pedido de chamada. 

A maior parte deste artigo tem a ver com o processamento da *consulta de pesquisa:* `"Spacious, air-condition* +\"Ocean view\""` . Filtragem e encomenda estão fora de alcance. Para mais informações, consulte a documentação de referência da [API de pesquisa.](/rest/api/searchservice/search-documents)

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fase 1: Análise de consulta 

Como notado, a cadeia de consulta é a primeira linha do pedido: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

O parser de consulta separa os operadores (como `*` e `+` no exemplo) dos termos de pesquisa, e desconstrói a consulta de pesquisa em *subqueries* de um tipo suportado: 

+ *consulta de prazo* para termos autónomos (como espaçosos)
+ *consulta de frase* para termos citados (como vista para o oceano)
+ *consulta de prefixo* para termos seguidos por um operador de prefixo `*` (como ar condicionado)

Para obter uma lista completa de tipos de consulta suportados ver [sintaxe de consulta lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Os operadores associados a uma subquery determinam se a consulta "deve ser" ou "deve ser" satisfeita para que um documento seja considerado uma correspondência. Por exemplo, `+"Ocean view"` é "obrigatório" devido ao `+` operador. 

O parser de consulta reestrutura as subqueries numa *árvore de consulta* (uma estrutura interna que representa a consulta) que passa para o motor de busca. Na primeira fase da consulta, a árvore de consulta é assim.  

 ![Boolean consulta de pesquisamode qualquer][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Parsers suportados: Lucene simples e completo 

 A Azure Cognitive Search expõe duas línguas de consulta diferentes, `simple` (padrão) e `full` . Ao definir o parâmetro com o `queryType` seu pedido de pesquisa, informe o parser de consulta que escolher para que saiba interpretar os operadores e sintaxe. A [linguagem de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) é intuitiva e robusta, muitas vezes adequada para interpretar a entrada do utilizador como-é sem processamento do lado do cliente. Suporta operadores de consulta familiares de motores de pesquisa web. A [linguagem de consulta Lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search), que obtém por definição, estende o `queryType=full` idioma de consulta simples padrão adicionando suporte a mais operadores e tipos de consulta como wildcard, fuzzy, regex e consultas de âmbito de campo. Por exemplo, uma expressão regular enviada em sintaxe de consulta simples seria interpretada como uma cadeia de consulta e não uma expressão. O pedido de exemplo neste artigo utiliza a linguagem de consulta Full Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Impacto da pesquisaMode no parser 

Outro parâmetro de pedido de pesquisa que afeta a análise é o `searchMode` parâmetro. Controla o operador predefinido para consultas booleanas: qualquer (predefinido) ou todos.  

Quando `searchMode=any` , que é o padrão, o delimiter do espaço entre espaçoso e ar-condicionado é OR `||` (), tornando o texto de consulta de amostra equivalente a: 

```
Spacious,||air-condition*+"Ocean view" 
```

Os operadores `+` explícitos, como em `+"Ocean view"` , são inequívocos na construção de consultas booleanas (o termo *deve coincidir).* Menos óbvio é como interpretar os termos restantes: espaçoso e ar condicionado. O motor de busca deve encontrar fósforos na vista para o mar *e* espaçosos *e* ar condicionado? Ou deve encontrar vista para o mar mais *um dos* termos restantes? 

Por `searchMode=any` predefinição (), o motor de busca assume uma interpretação mais ampla. Qualquer um dos *campos deve* ser combinado, refletindo semântica "ou" A árvore de consulta inicial ilustrada anteriormente, com as duas operações "deve", mostra o padrão.  

Suponha que agora `searchMode=all` definimos. Neste caso, o espaço é interpretado como uma operação "e". Cada um dos termos restantes deve estar presente no documento para se qualificar como um jogo. A consulta da amostra resultante seria interpretada da seguinte forma: 

```
+Spacious,+air-condition*+"Ocean view"
```

Uma árvore de consulta modificada para esta consulta seria a seguinte, onde um documento correspondente é a intersecção dos três subqueries: 

 ![Pesquisa de consulta booleana todos][3]

> [!Note] 
> Escolher `searchMode=any` é `searchMode=all` uma decisão que melhor se chega ao executar consultas representativas. Os utilizadores que são suscetíveis de incluir operadores (comuns ao pesquisar lojas de documentos) podem encontrar resultados mais intuitivos se informarem que a `searchMode=all` consulta booleana construa. Para obter mais informações sobre a interação entre `searchMode` os operadores e os operadores, consulte [a sintaxe de consulta simples.](/rest/api/searchservice/simple-query-syntax-in-azure-search)

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fase 2: Análise lexical 

Os analisadores lexical processam *consultas de termos* e consultas de *frase* após a estrutura da árvore de consulta. Um analisador aceita as entradas de texto que lhe são dadas pelo parser, processa o texto e, em seguida, envia de volta termos simbólicos para serem incorporados na árvore de consulta. 

A forma mais comum de análise lexical é *a análise linguística* que transforma termos de consulta com base em regras específicas de uma determinada língua: 

* Reduzindo um termo de consulta à forma de raiz de uma palavra 
* Remoção de palavras não essenciais (palavras de ordem, tais como "o" ou "e" em inglês) 
* Quebrar uma palavra composta em peças componentes 
* Invólucro inferior uma palavra maiúsca 

Todas estas operações tendem a apagar diferenças entre a entrada de texto fornecida pelo utilizador e os termos armazenados no índice. Tais operações vão além do processamento de texto e requerem um conhecimento aprofundado da própria língua. Para adicionar esta camada de consciência linguística, a Azure Cognitive Search suporta uma longa lista de [analisadores linguísticos](/rest/api/searchservice/language-support) tanto da Lucene como da Microsoft.

> [!Note]
> Os requisitos de análise podem variar de mínimo a elaborado dependendo do seu cenário. Pode controlar a complexidade da análise lexical selecionando um dos analisadores predefinidos ou criando o seu próprio [analisador personalizado](/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Os analisadores são analisados para campos pesquisáveis e são especificados como parte de uma definição de campo. Isto permite variar a análise lexical por campo. Não especificado, o analisador *padrão* lucene é usado.

No nosso exemplo, antes da análise, a árvore de consulta inicial tem o termo "Espaçoso", com um "S" maiúscula e uma vírgula que o parser de consulta interpreta como parte do termo de consulta (uma vírgula não é considerada um operador de linguagem de consulta).  

Quando o analisador padrão processa o termo, ele vai minúscula "vista para o oceano" e "espaçosa", e remover o caráter vírgula. A árvore de consulta modificada será a seguinte: 

 ![Consulta booleana com termos analisados][4]

### <a name="testing-analyzer-behaviors"></a>Testar comportamentos do analisador 

O comportamento de um analisador pode ser testado utilizando a [API de análise.](/rest/api/searchservice/test-analyzer) Forneça o texto que pretende analisar para ver que termos dado o analisador irá gerar. Por exemplo, para ver como o analisador padrão processaria o texto "ar condicionado", pode emitir o seguinte pedido:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

O analisador padrão quebra o texto de entrada nos dois tokens seguintes, anotando-os com atributos como compensações de início e fim (utilizados para realçar o impacto) bem como a sua posição (usada para combinar frases):

```json
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
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Exceções à análise lexical 

A análise lexical aplica-se apenas a tipos de consulta que requerem termos completos – seja uma consulta de termos ou uma consulta de frase. Não se aplica a tipos de consulta com termos incompletos – consulta de prefixo, consulta de wildcard, consulta de regex – ou a uma consulta fuzzy. Esses tipos de consulta, incluindo a consulta de prefixo com o termo `air-condition*` no nosso exemplo, são adicionados diretamente à árvore de consulta, contornando a fase de análise. A única transformação realizada em termos de consulta destes tipos é a redução.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fase 3: Recuperação de documentos 

A recuperação do documento refere-se à procura de documentos com termos correspondentes no índice. Esta fase é melhor entendida através de um exemplo. Vamos começar com um índice de hotéis com o seguinte esquema simples: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Assuma ainda que este índice contém os seguintes quatro documentos: 

```json
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
```

**Como os termos são indexados**

Para compreender a recuperação, ajuda a saber alguns básicos sobre indexação. A unidade de armazenamento é um índice invertido, um para cada campo pesmável. Dentro de um índice invertido está uma lista ordenada de todos os termos de todos os documentos. Cada termo mapeia a lista de documentos em que ocorre, como é evidente no exemplo abaixo.

Para produzir os termos num índice invertido, o motor de busca realiza uma análise lexical sobre o conteúdo dos documentos, semelhante ao que acontece durante o processamento de consultas:

1. *As entradas de texto* são passadas para um analisador, minúscula, despojada de pontuação, e assim por diante, dependendo da configuração do analisador. 
2. *Os tokens* são a produção da análise lexical.
3. *Os termos* são adicionados ao índice.

É comum, mas não é necessário, usar os mesmos analisadores para operações de pesquisa e indexação para que os termos de consulta se pareçam mais com termos dentro do índice.

> [!Note]
> A Azure Cognitive Search permite especificar diferentes analisadores para indexar e pesquisar através de parâmetros adicionais `indexAnalyzer` e `searchAnalyzer` de campo. Se não especificado, o conjunto de analisador com a `analyzer` propriedade é usado tanto para indexar como para pesquisar.  

**Índice invertido para documentos de exemplo**

Voltando ao nosso exemplo, para o campo **do título,** o índice invertido é o seguinte:

| Termo | Lista de documentos |
|------|---------------|
| atman | 1 |
| praia | 2 |
| hotel | 1, 3 |
| oceano | 4  |
| playa | 3 |
| resort | 3 |
| retiro | 4 |

No campo do título, apenas o *hotel* aparece em dois documentos: 1, 3.

Para o campo de **descrição,** o índice é o seguinte:

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
| tranquila | 4
| quartos  | 1, 3
| isolado | 4
| costa | 2
| espaçoso | 1
| o | 1, 2
| para | 1
| ver | 1, 2, 3
| andar | 1
| com o | 3


**Condições de consulta correspondentes aos termos indexados**

Tendo em conta os índices invertidos acima, vamos voltar à consulta de amostra e ver como os documentos correspondentes são encontrados para a nossa consulta de exemplo. Lembre-se que a árvore de consulta final é assim: 

 ![Consulta booleana com termos analisados][4]

Durante a execução de consultas, as consultas individuais são executadas contra os campos pes pesjáveis de forma independente. 

+ O TermoQuery, "espaçoso", corresponde ao documento 1 (Hotel Atman). 

+ O PrefixQuery, "ar-condicionado",não corresponde a nenhum documento. 

  Este é um comportamento que às vezes confunde desenvolvedores. Embora o termo ar-condicionado exista no documento, é dividido em dois termos pelo analisador padrão. Lembre-se que as consultas de prefixo, que contêm termos parciais, não são analisadas. Portanto, os termos com "ar-condição" prefixo são olhados para cima no índice invertido e não encontrados.

+ A FraseQuery, "vista para o mar", olha para os termos "oceano" e "vista" e verifica a proximidade dos termos no documento original. Os documentos 1, 2 e 3 correspondem a esta consulta no campo de descrição. O documento de aviso 4 tem o termo oceano no título, mas não é considerado um fósforo, pois estamos à procura da frase "vista para o mar" em vez de palavras individuais. 

> [!Note]
> Uma consulta de pesquisa é executada independentemente contra todos os campos pes pesjáveis no índice de Pesquisa Cognitiva Azure, a menos que limite os campos definidos com o `searchFields` parâmetro, como ilustrado no pedido de pesquisa de exemplo. Os documentos que correspondam em qualquer um dos campos selecionados são devolvidos. 

No geral, para a consulta em questão, os documentos que correspondem são 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fase 4: Pontuação  

Cada documento de um conjunto de resultados de pesquisa é atribuído uma pontuação de relevância. A função da pontuação de relevância é classificar mais alto os documentos que melhor respondam a uma pergunta do utilizador expressa pela consulta de pesquisa. A pontuação é calculada com base em propriedades estatísticas de termos que correspondam. No cerne da fórmula de pontuação está [TF/IDF (frequência de documento inverso de frequência de frequência)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Em consultas que contenham termos raros e comuns, a TF/IDF promove resultados que contenham o termo raro. Por exemplo, num índice hipotético com todos os artigos da Wikipédia, a partir de documentos que correspondam à consulta *do presidente, os* documentos correspondentes ao *presidente* são considerados mais relevantes do que documentos correspondentes *no*.


### <a name="scoring-example"></a>Exemplo de pontuação

Lembre-se dos três documentos que combinam com a nossa consulta de exemplo:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
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
```

O documento 1 corresponde melhor à consulta porque tanto o termo *espaçoso* como a frase exigida *vista para* o mar ocorrem no campo de descrição. Os dois documentos seguintes correspondem apenas à expressão vista para o *mar.* Pode ser surpreendente que a pontuação de relevância para os documentos 2 e 3 seja diferente, mesmo que correspondam à consulta da mesma forma. É porque a fórmula de pontuação tem mais componentes do que apenas TF/IDF. Neste caso, o documento 3 foi atribuído a uma pontuação ligeiramente superior porque a sua descrição é mais curta. Saiba mais sobre [a Fórmula de Pontuação Prática de Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) para entender como o comprimento do campo e outros fatores podem influenciar a pontuação de relevância.

Alguns tipos de consulta (wildcard, prefixo, regex) sempre contribuem com uma pontuação constante para a pontuação geral do documento. Isto permite que os jogos encontrados através da expansão de consultas sejam incluídos nos resultados, mas sem afetar o ranking. 

Um exemplo ilustra por que razão isto importa. As pesquisas de wildcard, incluindo pesquisas de prefixos, são ambíguas por definição porque a entrada é uma cadeia parcial com potenciais fósforos num número muito grande de termos díspares (considere uma entrada de "tour*", com fósforos encontrados em "tours", "tourettes" e "tourmaline"). Dada a natureza destes resultados, não há forma de inferir razoavelmente quais os termos mais valiosos do que outros. Por esta razão, ignoramos as frequências de termos ao marcar resultados em consultas de tipos wildcard, prefixo e regex. Num pedido de pesquisa em várias partes que inclui termos parciais e completos, os resultados da entrada parcial são incorporados com uma pontuação constante para evitar distorções em relação a jogos potencialmente inesperados.

### <a name="score-tuning"></a>Afinação de pontuação

Existem duas formas de sintonizar pontuações de relevância na Pesquisa Cognitiva Azure:

1. **Os perfis de pontuação** promovem documentos na lista de resultados classificados com base num conjunto de regras. No nosso exemplo, poderíamos considerar documentos que correspondam no campo do título mais relevantes do que documentos que correspondam no campo da descrição. Além disso, se o nosso índice tivesse um campo de preços para cada hotel, poderíamos promover documentos com um preço mais baixo. Saiba mais como [adicionar Perfis de Pontuação a um índice de pesquisa.](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **O reforço de prazos** (disponível apenas na sintaxe de consulta Lucene completa) fornece um operador impulsionador `^` que pode ser aplicado a qualquer parte da árvore de consulta. No nosso exemplo, em vez de procurar no *pré-ar condicionado, pode-se* \* procurar o termo exato de ar *condicionado* ou o prefixo, mas os documentos que correspondem ao termo exato são classificados mais alto aplicando aumento à consulta do termo: *ar-condicionado^2|| ar condicionado**. Saiba mais sobre [o aumento de mandatos.](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost)


### <a name="scoring-in-a-distributed-index"></a>Pontuação num índice distribuído

Todos os índices na Pesquisa Cognitiva Azure são automaticamente divididos em múltiplos fragmentos, permitindo-nos distribuir rapidamente o índice entre vários nós durante a escala de serviço para cima ou para baixo. Quando um pedido de pesquisa é emitido, é emitido contra cada fragmento independentemente. Os resultados de cada fragmento são então fundidos e encomendados por pontuação (se não for definida nenhuma outra encomenda). É importante saber que a função de pontuação pondera a frequência do termo contra a sua frequência de documento inversa em todos os documentos dentro do fragmento, e não em todos os fragmentos!

Isto significa que uma pontuação de relevância *pode* ser diferente para documentos idênticos se residirem em fragmentos diferentes. Felizmente, tais diferenças tendem a desaparecer à medida que o número de documentos no índice cresce devido a uma distribuição mais uniforme a prazo. Não é possível presumir em que fragmento qualquer documento será colocado. No entanto, assumindo que uma chave de documento não muda, será sempre atribuída ao mesmo fragmento.

Em geral, a pontuação do documento não é o melhor atributo para encomendar documentos se a estabilidade da ordem for importante. Por exemplo, tendo em conta dois documentos com uma pontuação idêntica, não há garantias de qual aparece primeiro nas sequências subsequentes da mesma consulta. A pontuação do documento só deve dar um sentido geral de relevância documental em relação a outros documentos no conjunto de resultados.

## <a name="conclusion"></a>Conclusão

O sucesso dos motores de pesquisa de internet suscitou expectativas de uma pesquisa completa de texto sobre dados privados. Para quase qualquer tipo de experiência de pesquisa, esperamos agora que o motor compreenda a nossa intenção, mesmo quando os termos são mal escritos ou incompletos. Podemos até esperar jogos baseados em termos ou sinónimos quase equivalentes que nunca especificamos.

Do ponto de vista técnico, a pesquisa completa de texto é altamente complexa, requerendo uma análise linguística sofisticada e uma abordagem sistemática ao processamento de formas que destilam, expandam e transformam termos de consulta para obter um resultado relevante. Dadas as complexidades inerentes, há muitos fatores que podem afetar o resultado de uma consulta. Por esta razão, investir o tempo para entender a mecânica da pesquisa completa de texto oferece benefícios tangíveis ao tentar trabalhar através de resultados inesperados.  

Este artigo explorou a pesquisa completa de texto no contexto da Pesquisa Cognitiva Azure. Esperamos que lhe dê conhecimentos suficientes para reconhecer potenciais causas e resoluções para resolver problemas de consulta comuns. 

## <a name="next-steps"></a>Passos seguintes

+ Construa o índice de amostra, experimente diferentes consultas e analise os resultados. Para obter instruções, consulte [Construir e consultar um índice no portal](search-get-started-portal.md#query-index).

+ Experimente sintaxe de consulta adicional a partir da secção de exemplo [de Documentos de Busca](/rest/api/searchservice/search-documents#bkmk_examples) ou a partir de [sintaxe](/rest/api/searchservice/simple-query-syntax-in-azure-search) de consulta simples no explorador de pesquisa no portal.

+ [Reveja perfis de pontuação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se quiser sintonizar o ranking na sua aplicação de pesquisa.

+ Saiba como aplicar [analisadores lexical específicos](/rest/api/searchservice/language-support)da linguagem.

+ [Configure os analisadores personalizados](/rest/api/searchservice/custom-analyzers-in-azure-search) para o processamento mínimo ou para o processamento especializado em campos específicos.

## <a name="see-also"></a>Ver também

[Search Documents REST API](/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST) 

[Sintaxe de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Sintaxe de consulta Lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Processar os resultados da pesquisa](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png