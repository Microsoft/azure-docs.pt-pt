---
title: Arquitetura de motor (Lucene) de pesquisa de texto completo - Azure Search
description: Explicação dos conceitos da obtenção de Lucene consulta processamento e o documento para pesquisa em texto completo, como relacionados para o Azure Search.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: d504635121c5153367cd0b89ce593b093bb3cd39
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537249"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Completa como funciona a pesquisa de texto no Azure Search

Este artigo é para desenvolvedores que precisam de uma compreensão mais aprofundada do funcionamento da pesquisa de texto completo do Lucene no Azure Search. Para consultas de texto, o Azure Search perfeitamente fornecerá os resultados esperados na maioria dos cenários, mas, ocasionalmente, poderá obter um resultado que parece "off" alguma forma. Nestas situações, ter um plano de fundo em quatro fases de execução de consulta Lucene (consultar a análise lexical, análise, documente a correspondência, classificação) podem ajudar a identificar alterações específicas para parâmetros de consulta ou configuração de índice que irá fornecer o desejado resultado. 

> [!Note] 
> O Azure Search utiliza Lucene para pesquisa em texto completo, mas a integração de Lucene não é exaustiva. Estamos seletivamente a expor e expandir a funcionalidade de Lucene para permitir os cenários importantes para o Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Descrição geral da arquitetura e de diagrama

Processamento de uma consulta de pesquisa de texto completo começa com o texto da consulta para extrair os termos de pesquisa de análise. O motor de busca utiliza um índice para obter documentos com os termos correspondentes. Termos de consulta individual, por vezes, são divididos e reconstituted em novas formas para converter uma rede mais abrangente sobre o que poderia ser considerado como uma correspondência de potencial. Um conjunto de resultados, em seguida, é ordenado por uma pontuação de relevância atribuída a cada documento correspondente individual. Aqueles na parte superior da lista classificada são devolvidos ao aplicativo de chamada.

Funcionando como a execução de consultas tem quatro fases: 

1. Análise de consulta 
2. Análise lexical 
3. Obtenção de documento 
4. Classificação 

O diagrama abaixo ilustra os componentes usados para processar uma solicitação de pesquisa. 

 ![Diagrama de arquitetura de consulta Lucene no Azure Search][1]


| Componentes principais | Descrição funcional | 
|----------------|------------------------|
|**Analisadores de consulta** | Separe os termos de consulta de operadores de consulta e criar a estrutura de consulta (uma árvore de consulta) a serem enviados para o motor de busca. |
|**Analisadores** | Execute análise lexical em termos de consulta. Este processo pode envolver a transformar, remover ou expansão dos termos de consulta. |
|**Índice** | Uma estrutura de dados eficiente utilizada para armazenar e organizar termos pesquisáveis extraídos de documentos indexados. |
|**Mecanismo de pesquisa** | Obtém e pontuações de documentos com base no conteúdo do índice invertido correspondentes. |

## <a name="anatomy-of-a-search-request"></a>Anatomia de um pedido de pesquisa

Um pedido de pesquisa é uma especificação completa do que deve ser devolvido num conjunto de resultados. Na forma mais simples, é uma consulta em branco sem nenhum critério de qualquer tipo. Um exemplo mais realista inclui a parâmetros, vários termos de consulta, talvez no âmbito de determinados campos, com possivelmente uma expressão de filtro e regras de ordenação.  

O exemplo seguinte é uma solicitação de pesquisa pode enviar para o Azure Search utilizando o [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2017-11-11 
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

Para este pedido, o mecanismo de pesquisa faz o seguinte:

1. Filtros de saída de documentos em que o preço é, pelo menos, 60 e menos de US $300.
2. Executa a consulta. Neste exemplo, a consulta de pesquisa consiste em frases e termos: `"Spacious, air-condition* +\"Ocean view\""` (utilizadores, normalmente, não introduzem pontuação, mas incluí-lo no exemplo permite-nos explicar como analisadores manipulá-lo). Para esta consulta, o motor de busca analisa a descrição e campos de título especificado no `searchFields` para documentos que contenham "View Oceano" e adicionalmente o termo "spacious" ou à medida que começam com o prefixo "air-condition". O `searchMode` parâmetro é utilizado para correspondência com qualquer termo (predefinição) ou todos eles, para casos em que um termo não é explicitamente necessário (`+`).
3. Ordena resultante de hotéis, proximidade para uma localização de determinada geografia definiu e, em seguida, retornados ao aplicativo de chamada. 

A maior parte deste artigo é sobre o processamento do *consulta de pesquisa*: `"Spacious, air-condition* +\"Ocean view\""`. Filtragem e ordenação estão fora do escopo. Para obter mais informações, consulte a [documentação de referência da API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fase 1: Análise de consulta 

Como observado, a cadeia de consulta é a primeira linha do pedido: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

O analisador de consultas separa operadores (tais como `*` e `+` no exemplo) a partir da pesquisa de termos e deconstructs a consulta de pesquisa no *subconsultas* de um tipo suportado: 

+ *consulta de termo* para termos autónomo (como spacious)
+ *consulta de frase* para termos com aspas simples (como exibição do Oceano)
+ *consulta de prefixo* por termos seguidos de um operador de prefixo `*` (como air-condition)

Para obter uma lista completa dos tipos de consulta suportados, consulte [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operadores de associadas com uma subconsulta determinam se a consulta "tem de ser" ou "deve ser" satisfeitas para que um documento a ser considerado uma correspondência. Por exemplo, `+"Ocean view"` é "prioridade" devido ao `+` operador. 

O analisador de consultas restructures subconsultas num *árvore de consulta* (uma estrutura interna que representa a consulta) que ele passa para o motor de busca. Na primeira fase de análise de consulta, a árvore de consulta é semelhante a esta.  

 ![Valor booleano de consulta searchmode qualquer][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Analisadores suportados: Lucene completa e Simple 

 O Azure Search expõe duas linguagens de consulta diferentes `simple` (predefinição) e `full`. Ao definir o `queryType` parâmetro com o seu pedido de pesquisa, diz o analisador de consultas a linguagem de consulta optar para que ela Saiba como interpretar os operadores e sintaxe. O [linguagem de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) é intuitiva e robusta, muitas vezes, adequado interpretar a entrada do usuário como-sem processamento do lado do cliente. Ele oferece suporte a operadores de consulta familiares web dos motores de busca. O [linguagem de consulta Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), que obtém ao definir `queryType=full`, estende o idioma de consulta simples padrão, adicionando suporte para obter mais operadores e tipos de consulta como caráter universal, difusa, regex e consultas no âmbito do campo. Por exemplo, uma expressão regular enviada na sintaxe de consulta simples poderia ser interpretada como uma cadeia de consulta e não uma expressão. O pedido de exemplo neste artigo utiliza a linguagem de consulta Lucene completa.

### <a name="impact-of-searchmode-on-the-parser"></a>Impacto da searchMode sobre o analisador 

Outro parâmetro de pedido de pesquisa que afeta a análise é o `searchMode` parâmetro. Controla o operador de padrão para consultas booleanos: qualquer (predefinição) ou todos.  

Quando `searchMode=any`, que é o padrão, o delimitador de espaço entre spacious e air-condition é ou (`||`), tornando o texto da consulta de exemplo equivalente a: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Operadores explícitas, por exemplo, `+` no `+"Ocean view"`, são inequívoca na construção de consulta booleano (o termo *tem* corresponde ao). Menos óbvio é como interpretar os termos restantes: spacious e air-condition. O motor de busca encontrará correspondências na vista do Oceano *e* spacious *e* air-condition? Ou encontra vista Oceano plus *individualmente* dos termos restantes? 

Por predefinição (`searchMode=any`), o motor de busca assume a interpretação mais ampla. O campo *deve* ser correspondido, refletindo semântica de "ou". A árvore de consulta inicial ilustrado anteriormente, com os dois "devemos" operações, mostra as predefinições.  

Suponha que definimos agora `searchMode=all`. Neste caso, o espaço é interpretado como uma operação de "e". Cada um dos restantes termos tem ambos de estar presente no documento para ser qualificado como uma correspondência. O exemplo de consulta resultante poderia ser interpretado da seguinte forma: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Uma árvore de consulta modificada para esta consulta seria o seguinte, em que um documento correspondente é a intersecção de todas as três subconsultas: 

 ![Booleano de consulta searchmode todos os][3]

> [!Note] 
> Escolher `searchMode=any` ao longo do `searchMode=all` é uma decisão melhor chegaram ao executar consultas representativas. Os utilizadores que têm propensão incluir operadores (comum quando armazena de pesquisa de documento) pode encontrar os resultados mais intuitivo se `searchMode=all` informa construções de consulta booleano. Para obter mais informações sobre a interação entre `searchMode` e operadores, consulte [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fase 2: Análise lexical 

Processo de analisadores lexicais *prazo consultas* e *frase consultas* depois da árvore de consulta está estruturada. Um analisador aceita as entradas de texto foi fornecidas pelo analisador, processa o texto e, em seguida, envia de volta com token termos sejam incorporados a árvore de consulta. 

É a forma mais comum de análise lexical *análise linguística* que condições com base em regras específicas para um determinado idioma de consulta de transformações: 

* A redução de um termo de consulta para o formulário de raiz de uma palavra 
* Remover palavras não essencial (palavras de paragem, por exemplo, "a" ou "e" em inglês) 
* Dividir uma palavra composta em partes de componente 
* Inferior casing, uma palavra em maiúsculas 

Todas essas operações tendem a apagar as diferenças entre a entrada de texto fornecida pelo utilizador e os termos armazenados no índice. Essas operações vão além do processamento de texto e requerem um conhecimento profundo da linguagem em si. Para adicionar esta camada de reconhecimento de linguística, Azure Search oferece suporte a uma longa lista de [analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) do Lucene e da Microsoft.

> [!Note]
> Requisitos de análise podem variar entre mínimo para elaborado dependendo do seu cenário. Pode controlar a complexidade da análise lexical, selecionar um dos analisadores predefinidos ou criando sua própria [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analisadores estão confinados aos campos pesquisáveis e são especificados como parte de uma definição de campo. Isso permite variar a análise lexical numa base por campo. Não especificado, o *padrão* Lucene analyzer é utilizado.

No nosso exemplo, antes da análise, a árvore de consulta inicial, tem o termo "Spacious", com uma letra maiúscula "S" e uma vírgula que o analisador de consultas interpreta como parte do termo de consulta (uma vírgula não é considerada um operador de linguagem de consulta).  

Quando o analisador de predefinida processa o termo, ele será minúsculas de "modo de exibição do Oceano" e "spacious" e remover o caráter de vírgula. A árvore de consulta modificada será semelhante ao seguinte: 

 ![Consulta booleana com termos analisados][4]

### <a name="testing-analyzer-behaviors"></a>Comportamentos de analisador de teste 

O comportamento de um analisador pode ser testado com o [API analisar](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Forneça o texto que pretende analisar para ver o que irão gerar termos dadas analyzer. Por exemplo, para ver como o analisador padrão seria processada o texto "air-condition", pode emitir o pedido seguinte:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

O analisador padrão quebra o texto de entrada para os seguintes dois tokens, anotando-los com atributos, como iniciar e deslocamentos de fim (utilizados para o detetor de ocorrências), bem como a respetiva posição (utilizado para a correspondência de expressão):

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

### <a name="exceptions-to-lexical-analysis"></a>Exceções a análise lexical 

Análise lexical aplica-se apenas aos tipos de consulta que necessitam de termos completos – uma consulta de termo ou uma consulta de frase. Não se aplica aos tipos de consulta com os termos incompletos – consulta de prefixo, consulta de caráter universal, consultas de regex – ou a uma consulta difusa. Os tipos, incluindo a consulta de prefixo com o termo de consulta `air-condition*` no nosso exemplo, são adicionados diretamente à árvore de consulta, ignorando a fase de análise. A transformação de apenas executada nos termos de consulta desses tipos é minúsculas.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fase 3: Obtenção de documento 

Obtenção de documento refere-se para encontrar os documentos com correspondência de termos no índice. Nesta fase é compreendi melhor por meio de um exemplo. Vamos começar com um índice de hotéis o esquema simples seguinte: 

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

Ainda mais partem do princípio de que este índice contém os documentos de quatro seguintes: 

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

Para compreender a obtenção, ajuda para saber algumas noções básicas sobre indexação. A unidade de armazenamento é um índice invertido, um para cada campo pesquisável. Dentro de um índice invertido é uma lista ordenada de todos os termos de todos os documentos. Cada termo mapeia para a lista de documentos em que este ocorrer, tão evidente no exemplo abaixo.

Para produzir os termos de um índice invertido, o motor de busca efetua a análise lexical sobre o conteúdo de documentos, semelhantes ao que acontece durante o processamento de consulta:

1. *Entradas de texto* são transmitidos para um analisador, em minúsculas, despido de pontuação e assim por diante, dependendo da configuração do analyzer. 
2. *Tokens* são o resultado da análise de texto.
3. *Termos* são adicionados ao índice.

É comum, mas não é necessária, para utilizar os analisadores mesmo para pesquisa e operações de indexação para que os termos de consulta uma aparência mais semelhante termos dentro do índice.

> [!Note]
> O Azure Search permite-lhe especificar diferentes analisadores de indexação e pesquisa via adicionais `indexAnalyzer` e `searchAnalyzer` parâmetros de campo. Se não for especificado, o analisador de conjunto com o `analyzer` propriedade é utilizada para a indexação e a pesquisa.  

**Invertida índice para documentos de exemplo**

Retornando ao nosso exemplo, para o **title** campo, o índice invertido tem esta aparência:

| Termo | Lista de documentos |
|------|---------------|
| atman | 1 |
| praia | 2 |
| hotel | 1, 3 |
| Oceano | 4  |
| playa | 3 |
| recurso | 3 |
| em retirada | 4 |

No campo de título, apenas *hotel* aparece em dois documentos: 1, 3.

Para o **Descrição** campo, o índice é o seguinte:

| Termo | Lista de documentos |
|------|---------------|
| air | 3
| e em | 4
| praia | 1
| condicionadas de | 3
| mais confortáveis | 3
| distância | 1
| Ilha | 2
| kauaʻi | 2
| localizado | 2
| Norte | 2
| Oceano | 1, 2, 3
| de | 2
| em |2
| silencioso | 4
| ambientes  | 1, 3
| secluded | 4
| shore | 2
| spacious | 1
| o | 1, 2
| para | 1
| ver | 1, 2, 3
| movimentação | 1
| com | 3


**Correspondência de termos de consulta em relação a indexada termos**

Tendo em conta os índices invertidos acima, vamos voltar para a consulta de exemplo e ver documentos correspondentes como encontram-se para a nossa consulta de exemplo. Lembre-se de que a árvore de consulta final é semelhante a este: 

 ![Consulta booleana com termos analisados][4]

Durante a execução da consulta, consultas individuais são executadas em relação os campos pesquisáveis independentemente. 

+ As correspondências de "spacious" TermQuery, documentam 1 (Hotel Atman). 

+ PrefixQuery, "air-condition *", não corresponde a todos os documentos. 

  Este é um comportamento que confunde, às vezes, os desenvolvedores. Embora o termo com ar-condicionado existe no documento, ele é dividido em dois termos pelo analisador padrão. Lembre-se de que as consultas de prefixo, que contêm parciais termos, não estão a ser analisadas. Por conseguinte, os termos com o prefixo "air-condition" são pesquisados no índice invertido e não foi encontrados.

+ PhraseQuery, "view Oceano", procura do Oceano"termos" e "ver" e verifica a proximidade de termos no documento original. Documentos 1, 2 e 3 correspondem a esta consulta no campo Descrição. Tenha em atenção o documento 4 tem o Oceano termo no título, mas não é considerado uma correspondência, como estamos procurando a frase "modo de exibição Oceano" em vez de palavras individuais. 

> [!Note]
> Uma consulta de pesquisa é executada de forma independente em relação a todos os campos pesquisáveis no índice de pesquisa do Azure, a menos que limite os campos em conjunto com o `searchFields` parâmetro, conforme ilustrado no exemplo de solicitação de pesquisa. Documentos que correspondam em qualquer um dos campos selecionados são devolvidos. 

Em todo o para a consulta em questão, os documentos que correspondem ao são 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fase 4: Classificação  

Todos os documentos num conjunto de resultados de pesquisa é atribuído uma pontuação de relevância. A função a pontuação de relevância é a classificação mais elevada desses documentos que melhor responder a uma pergunta de utilizador, conforme expressa por consulta de pesquisa. A classificação é calculada com base nas propriedades de estatísticas de termos correspondentes. No núcleo da fórmula da classificação é [TF/IDF (frequência de documento de frequência inverso do termo)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Em consultas que contém os termos de raros e comuns, TF/IDF promove resultados com o termo raro. Por exemplo, num índice hipotético com todos os artigos da Wikipedia, de documentos que correspondam a consulta *presidente*, documentos que corresponde aos *presidente* são considerados mais relevantes do que os documentos efetuar a correspondência em *o*.


### <a name="scoring-example"></a>Exemplo de classificação

Lembre-se os três documentos que corresponder à nossa consulta de exemplo:
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

Documento 1 correspondido a consulta melhor porque tanto o termo *spacious* e a frase necessária *vista Oceano* ocorrem no campo Descrição. Os próximos dois documentos correspondem apenas a frase *modo de exibição do Oceano*. Pode ser surpreendente que a pontuação de relevância para documento 2 e 3 é diferente, apesar de que corresponderam a consulta da mesma forma. É porque a fórmula de classificação tem mais componentes do que apenas TF/IDF. Neste caso, o documento 3 foi atribuído uma pontuação ligeiramente superior porque sua descrição é mais curta. Saiba mais sobre [fórmula de classificação prático do Lucene](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) para compreender como o comprimento de campo e ainda outros fatores podem influenciar a pontuação de relevância.

Alguns tipos (com carateres universais, prefixo, regex) de consulta sempre contribuir com uma pontuação constante para a classificação de documento geral. Isso permite que as correspondências encontradas por meio da expansão de consulta a serem incluídos nos resultados, mas sem afetar a classificação. 

Um exemplo ilustra por que isso é importante. Pesquisas com carateres universais, incluindo as pesquisas de prefixo, são ambíguas por definição, uma vez que a entrada é uma cadeia parcial com correspondências possíveis num grande número de termos diferentes (considere uma entrada de "apresentação *", com correspondências encontradas em "tours", "tourettes", e " tourmaline"). Não é dada a natureza desses resultados, é possível inferir razoavelmente os termos encontram-se mais valiosos do que outras pessoas. Por esse motivo, estamos a ignorar freqüências quando os resultados de classificação em consultas de caráter universal de tipos, o prefixo e o regex. Numa solicitação de pesquisa com várias partes que inclui os termos de parciais e integrais, os resultados da entrada parcial estão incorporados com uma pontuação de constante para evitar ajustes em relação aos correspondências potencialmente inesperadas.

### <a name="score-tuning"></a>Pontuação de otimização

Existem duas formas de otimizar a pontuação de relevância no Azure Search:

1. **Perfis de classificação** promover documentos na lista classificada de resultados com base num conjunto de regras. No nosso exemplo, poderia considerá-documentos correspondentes no campo de título mais relevante do que os documentos correspondentes no campo Descrição. Além disso, se o nosso índice apresentava um campo de preço para cada hotel, podemos promover documentos com o preço mais reduzido. Saiba mais como [adicionar perfis de classificação para um índice de pesquisa.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Prazo adaptativo** (disponível apenas na sintaxe de consulta Lucene completa) fornece um operador adaptativo `^` que podem ser aplicados a qualquer parte da árvore de consulta. No nosso exemplo, em vez de procurar no prefixo *air-condition*\*, um pode pesquisar por qualquer o termo exato *air-condition* ou o prefixo, mas os documentos que correspondem o termo exato são com a classificação superior ao aplicar boost à consulta de termo: * condição de ar ^ 2 | | Air-condition * *. Saiba mais sobre [prazo adaptativo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Classificação num índice distribuído

Todos os índices no Azure Search automaticamente são divididos em várias partições horizontais, permitindo-nos distribuir rapidamente o índice entre vários nós durante o dimensionamento do serviço de segurança ou reduzir verticalmente. Quando é emitida uma solicitação de pesquisa, ele é emitido relativamente a cada partição horizontal independentemente. Os resultados de cada partição horizontal, em seguida, são intercalados e ordenados por classificação (se não existem outros ordenação é definido). É importante saber que os pesos de função de classificação consultar a frequência de termo em relação a frequência de documento inversa em todos os documentos na partição horizontal, não em todas as partições horizontais!

Isso significa que uma pontuação de relevância *poderia* ser diferentes para documentos idênticos se residem em partições horizontais diferentes. Felizmente, essas diferenças tendem desaparecer à medida que cresce o número de documentos no índice devido a mais termo distribuição uniforme. Não é possível supor nos quais as partições horizontais qualquer determinado documento será colocado. No entanto, partindo do princípio de que uma chave de documento não é alterado, ele será sempre atribuído à mesma partição horizontal.

Em geral, a pontuação de documento não é o atributo melhor para ordenação documentos se estabilidade de ordem é importante. Por exemplo, tendo em conta dois documentos com uma classificação idêntico, não é garantido qual aparece em primeiro lugar nas execuções subsequentes da mesma consulta. Pontuação de documento apenas deve lhe dar uma noção geral de relevância de documento em relação a outros documentos no conjunto de resultados.

## <a name="conclusion"></a>Conclusão

O sucesso dos mecanismos de pesquisa da internet gerou o expectativas para a pesquisa em texto completo em dados privados. Para praticamente qualquer tipo de experiência de pesquisa, agora podemos esperar o mecanismo para compreender a nossa intenção, mesmo quando os termos encontram-se com erros ortográficos ou incompletas. Podemos até mesmo poderia esperar correspondências com base em junto ao termos equivalentes ou sinónimos que especificamos nunca realmente.

Do ponto de vista técnico, a pesquisa em texto completo é altamente complexa, que requerem uma análise linguística sofisticada e uma abordagem sistemática para processamento de formas que separe, expandam e transformar os termos de consulta para fornecer um resultado relevante. Tendo em conta as complexidades inerentes, existem muitos fatores que podem afetar o resultado de uma consulta. Por esse motivo, investir tempo para compreender o mecanismo de pesquisa em texto completo oferece benefícios tangíveis ao tentar trabalhar com resultados inesperados.  

Este artigo explorou a pesquisa em texto completo no contexto do Azure Search. Esperamos que dá-lhe suficiente em segundo plano para reconhecer possíveis causas e resoluções para resolver problemas comuns de consulta. 

## <a name="next-steps"></a>Passos Seguintes

+ Criar o índice de exemplo, experimente diferentes consultas e rever os resultados. Para obter instruções, consulte [criar e consultar um índice no portal do](search-get-started-portal.md#query-index).

+ Experimente a sintaxe de consulta adicionais do [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) secção de exemplo ou a partir de [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) no Explorador de pesquisa no portal.

+ Revisão [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) se quiser otimizar a classificação em seu aplicativo de pesquisa.

+ Saiba como aplicar [analisadores lexicais de idioma específico](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurar os analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) para processamento mínimo ou processamento especializado em campos específicos.

+ [Compare os analisadores de padrão e em inglês](https://alice.unearth.ai/)) lado a lado neste site da web de demonstração. 

## <a name="see-also"></a>Ver também

[Procurar nos documentos de REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Processar os resultados da pesquisa](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
