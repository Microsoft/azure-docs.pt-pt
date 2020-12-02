---
title: Termos parciais, padrões e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Use consultas de wildcard, regex e prefixo para combinar em termos inteiros ou parciais num pedido de pesquisa cognitiva Azure. Padrões difíceis de combinar que incluem caracteres especiais podem ser resolvidos usando sintaxe de consulta completa e analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 9f36502eb464f051cd50b51245db69fa76daa915
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499548"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Pesquisa parcial de prazo e padrões com caracteres especiais (wildcard, regex, padrões)

Uma *pesquisa de termo parcial* refere-se a consultas compostas por fragmentos de termo, onde em vez de um termo inteiro, você pode ter apenas o início, o meio ou o fim do prazo (às vezes referido como prefixo, infixo ou sufixo consultas). Uma pesquisa de termo parcial pode incluir uma combinação de fragmentos, muitas vezes com caracteres especiais, tais como traços ou cortes que fazem parte da cadeia de consulta. Os casos de uso comuns incluem partes de um número de telefone, URL, códigos ou palavras compostas hifenizadas.

As cadeias parciais de pesquisa e consulta que incluem caracteres especiais podem ser problemáticas se o índice não tiver fichas no formato esperado. Durante a fase de [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) da indexação (assumindo o analisador padrão padrão), os caracteres especiais são descartados, palavras compostas são divididas e o espaço branco é eliminado; tudo o que pode fazer com que as consultas falhem quando não se encontra qualquer correspondência. Por exemplo, um número de telefone como `+1 (425) 703-6214` (tokenized como `"1"` , - não vai aparecer numa consulta porque esse conteúdo não existe realmente no `"425"` `"703"` `"6214"` `"3-62"` índice. 

A solução é invocar um analisador durante a indexação que preserva uma cadeia completa, incluindo espaços e caracteres especiais, se necessário, para que possa incluir os espaços e caracteres na sua cadeia de consulta. Da mesma forma, ter uma corda completa que não é tokenizada em partes menores permite combinar padrão para consultas de "começa com" ou "termina com", onde o padrão que fornece pode ser avaliado contra um termo que não é transformado por análise lexical. Criar um campo adicional para uma corda intacta, além de usar um analisador de preservação de conteúdos que emite fichas de todo o prazo, é a solução tanto para a correspondência de padrões como para combinar em cordas de consulta que incluem caracteres especiais.

> [!TIP]
> Se você estiver familiarizado com as APIs do Carteiro e do REST, [descarregue a coleção de exemplos de consulta](https://github.com/Azure-Samples/azure-search-postman-samples/) para consultar termos parciais e caracteres especiais descritos neste artigo.

## <a name="what-is-partial-term-search-in-azure-cognitive-search"></a>O que é pesquisa parcial no termo pesquisa cognitiva de Azure

A Azure Cognitive Search analisa todos os termos simbólicos no índice e não encontrará uma correspondência num termo parcial a menos que inclua operadores de espaços reservados wildcard `*` `?` (e) ou formate a consulta como uma expressão regular. Os termos parciais são especificados utilizando estas técnicas:

+ [Consultas de expressão regular podem](query-lucene-syntax.md#bkmk_regex) ser qualquer expressão regular que seja válida sob Apache Lucene. 

+ [Os operadores wildcard com correspondência de prefixo](query-simple-syntax.md#prefix-search) refere-se a um padrão geralmente reconhecido que inclui o início de um termo, seguido por `*` ou `?` sufixo operadores, como `search=cap*` a correspondência em "Cap'n Jack's Waterfront Inn" ou "Gacc Capital". A correspondência de prefixo é suportada na sintaxe de consulta de Lucene simples e completa.

+ [Wildcard com infix e sufixo combinando](query-lucene-syntax.md#bkmk_wildcard) coloca os `*` operadores e `?` operadores dentro ou no início de um termo, e requer sintaxe de expressão regular (onde a expressão é fechada com cortes dianteiros). Por exemplo, a cadeia de consulta `search=/.*numeric*./` () devolve os resultados em "alfanumérico" e "alfanumérico" como sufixo e infixos.

Para pesquisa parcial de termos ou padrões, e alguns outros formulários de consulta como pesquisa difusa, os analisadores não são usados na hora da consulta. Para estas formas de consulta, que o parser deteta pela presença de operadores e delimiters, a cadeia de consulta é passada para o motor sem análise lexical. Para estas formas de consulta, o analisador especificado no campo é ignorado.

> [!NOTE]
> Quando uma cadeia de consulta parcial inclui caracteres, tais como cortes num fragmento de URL, pode ser necessário adicionar caracteres de fuga. Em JSON, um corte para a frente `/` é escapado com um corte para trás `\` . Como tal, `search=/.*microsoft.com\/azure\/.*/` é a sintaxe para o fragmento de URL "microsoft.com/azure/".

## <a name="solving-partialpattern-search-problems"></a>Resolver problemas de pesquisa parciais/padrões

Quando precisa de pesquisar em fragmentos ou padrões ou caracteres especiais, pode substituir o analisador padrão com um analisador personalizado que opera de acordo com regras de tokenização mais simples, mantendo toda a cadeia no índice. Dando um passo para trás, a abordagem é assim:

+ Defina um campo para armazenar uma versão intacta da cadeia (assumindo que pretende que o texto analisado e não analisado seja analisado na hora da consulta)
+ Avaliar e escolher entre os vários analisadores que emitem fichas no nível certo de granularidade
+ Atribua o analisador ao campo
+ Construir e testar o índice

> [!TIP]
> Avaliar os analisadores é um processo iterativo que requer reconstruções frequentes de índices. Pode facilitar este passo utilizando o Carteiro, as APIs REST para [Criar Índice,](/rest/api/searchservice/create-index) [Eliminar Índice,](/rest/api/searchservice/delete-index)[Documentos de Carga](/rest/api/searchservice/addupdate-or-delete-documents)e [Documentos de Busca](/rest/api/searchservice/search-documents). Para documentos de carga, o organismo de pedido deve conter um pequeno conjunto de dados representativos que pretende testar (por exemplo, um campo com números de telefone ou códigos de produto). Com estas APIs na mesma coleção do Carteiro, você pode pedalar através destes passos rapidamente.

## <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para diferentes cenários

Os analisadores determinam como os termos são tokenizados num índice. Uma vez que os analisadores são atribuídos numa base por campo, você pode criar campos no seu índice para otimizar para diferentes cenários. Por exemplo, pode definir "featureCode" e "featureCodeRegex" para suportar a pesquisa regular de texto completo no primeiro e o padrão avançado correspondente no segundo. Os analisadores atribuídos a cada campo determinam como o conteúdo de cada campo é tokenizado no índice.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Escolha um analisador

Ao escolher um analisador que produz tokens de longo prazo, os seguintes analisadores são escolhas comuns:

| Analisador | Comportamentos |
|----------|-----------|
| [analisadores de linguagem](index-add-language-analyzers.md) | Preserva hífens em palavras ou cordas compostas, mutações vogais e formas de verbo. Se os padrões de consulta incluem traços, usar um analisador de idiomas pode ser suficiente. |
| [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é tokenized como um único termo. |
| [espaço branco](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa-se apenas em espaços brancos. Os termos que incluem traços ou outros caracteres são tratados como um único símbolo. |
| [analisador personalizado](index-add-custom-analyzers.md) | (recomendado) A criação de um analisador personalizado permite especificar tanto o tokenizer como o filtro token. Os analisadores anteriores devem ser utilizados como tal. Um analisador personalizado permite-lhe escolher quais os tokenizers e filtros simbólicos para usar. <br><br>Uma combinação recomendada é o [tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) de [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)com um filtro de ficha minúscula . Por si só, o [analisador de palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não diminui qualquer texto maiústário, o que pode fazer com que as consultas falhem. Um analisador personalizado dá-lhe um mecanismo para adicionar o filtro de token em minúsculas. |

Se estiver a utilizar uma ferramenta de teste web de API como o Carteiro, pode adicionar a [chamada de RESE DO Analisador de Teste](/rest/api/searchservice/test-analyzer) para inspecionar a saída tokenizada.

Deve ter um índice populoso para trabalhar. Dado um índice existente e um campo contendo traços ou termos parciais, você pode experimentar vários analisadores em termos específicos para ver que fichas são emitidas.  

1. Em primeiro lugar, consulte o analisador Standard para ver como os termos são tokenizados por padrão.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Avalie a resposta para ver como o texto é tokenizado dentro do índice. Note como cada termo é descaixado e terminado. Apenas as consultas que coincidirem com estes tokens devolverão este documento nos resultados. Uma consulta que inclua "10-NOR" falhará.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Agora modificar o pedido de `whitespace` utilização do ou `keyword` analisador:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Agora a resposta consiste de um único símbolo, de maiúsão, com traços preservados como parte da corda. Se precisar de pesquisar um padrão ou um termo parcial como "10-NOR", o motor de consulta tem agora a base para encontrar uma correspondência.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Esteja ciente de que os parsers de consulta muitas vezes em termos de minúsculas em uma expressão de pesquisa ao construir a árvore de consulta. Se estiver a utilizar um analisador que não reduz as entradas de texto durante a indexação, e não estiver a obter resultados esperados, esta pode ser a razão. A solução é adicionar um filtro de ficha minúscula, conforme descrito na secção "Use analisadores personalizados" abaixo.

## <a name="configure-an-analyzer"></a>Configure um analisador
 
Quer esteja a avaliar os analisadores ou a avançar com uma configuração específica, terá de especificar o analisador na definição de campo e, possivelmente, configurar o próprio analisador se não estiver a utilizar um analisador incorporado. Ao trocar analisadores, normalmente é necessário reconstruir o índice (cair, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Use analisadores incorporados

Os analisadores incorporados ou predefinidos podem ser especificados pelo nome numa `analyzer` propriedade de uma definição de campo, sem necessidade de configuração adicional no índice. O exemplo a seguir demonstra como definiria o `whitespace` analisador num campo. 

Para outros cenários e para saber mais sobre outros analisadores incorporados, consulte [a lista de analisadores predefinidos.](./index-add-custom-analyzers.md#predefined-analyzers-reference) 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Use analisadores personalizados

Se estiver a utilizar um [analisador personalizado,](index-add-custom-analyzers.md)defina-o no índice com uma combinação definida pelo utilizador de tokenizer, filtro de fichas, com possíveis configurações. Em seguida, refira-o numa definição de campo, tal como um analisador incorporado.

Quando o objetivo é a tokenização a longo prazo, recomenda-se um analisador personalizado que consiste num **tokenizer de palavra-chave** e **um filtro de tokens de minúsculas minúsculas.**

+ O tokenizer de palavra-chave cria um único símbolo para todo o conteúdo de um campo.
+ O filtro de token minúsculo transforma letras maiúsculas em texto minúsculo. Os parsers de consulta tipicamente diminuem as entradas de texto maiúsculas. O invólucro inferior homogeneiza as entradas com os termos simbólicos.

O exemplo a seguir ilustra um analisador personalizado que fornece o tokenizer de palavras-chave e um filtro de ficha minúscula.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> O `keyword_v2` tokenizer e `lowercase` o filtro token são conhecidos do sistema e utilizam as suas configurações padrão, razão pela qual pode fazê-los referenciar pelo nome sem ter de os definir primeiro.

## <a name="build-and-test"></a>Compilar e testar

Depois de definir um índice com analisadores e definições de campo que suportam o seu cenário, carregue documentos que tenham cordas representativas para que possa testar consultas parciais de cordas. 

As secções anteriores explicaram a lógica. Esta secção passa por cada API que deve ligar ao testar a sua solução. Como já foi dito anteriormente, se utilizar uma ferramenta de teste web interativa, como o Carteiro, pode passar por estas tarefas rapidamente.

+ [Delete Index](/rest/api/searchservice/delete-index) remove um índice existente com o mesmo nome para que possa recriá-lo.

+ [O Create Index](/rest/api/searchservice/create-index) cria a estrutura de índice no seu serviço de pesquisa, incluindo definições de analisador e campos com uma especificação de analisador.

+ [Documentos de Carga](/rest/api/searchservice/addupdate-or-delete-documents) importa documentos com a mesma estrutura que o seu índice, bem como conteúdo pesmável. Após este passo, o seu índice está pronto para consultar ou testar.

+ [O Analisador de Testes](/rest/api/searchservice/test-analyzer) foi introduzido no [Choose a analyzer](#choose-an-analyzer). Teste algumas das cordas do seu índice usando uma variedade de analisadores para entender como os termos são tokenizados.

+ [Os Documentos de Pesquisa](/rest/api/searchservice/search-documents) explicam como construir um pedido de consulta, utilizando [sintaxe simples](query-simple-syntax.md) ou [sintaxe Lucene completa](query-lucene-syntax.md) para expressões wildcard e regulares.

  Para consultas parciais, como consultar "3-6214" para encontrar uma correspondência em "+1 (425) 703-6214", pode utilizar a sintaxe simples: `search=3-6214&queryType=simple` .

  Para consultas de infixo e sufixo, tais como consulta "num" ou "numérico para encontrar uma correspondência em "alfanumérico", utilize a sintaxe lucene completa e uma expressão regular: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Otimizar o desempenho de consultas

Se implementar a configuração recomendada que inclui o tokenizer keyword_v2 e o filtro de fichas de caixa inferior, poderá notar uma diminuição do desempenho da consulta devido ao processamento adicional de filtros simbólicos sobre os tokens existentes no seu índice. 

O exemplo a seguir adiciona um [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) para tornar os jogos de prefixo mais rápidos. São gerados tokens adicionais para combinações de caracteres de 2-25 que incluem caracteres: (não só MS, MSF, MSFT, MSFT/S, MSFT/SQ, MSFT/SQL). 

Como pode imaginar, a tokenização adicional resulta num índice maior. Se tiver capacidade suficiente para acomodar o índice maior, esta abordagem com o seu tempo de resposta mais rápido pode ser uma solução melhor.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>Passos seguintes

Este artigo explica como os analisadores contribuem para problemas de consulta e para resolver problemas de consulta. Como passo seguinte, analise mais de perto o impacto do analisador no processamento de indexação e consulta. Em particular, considere usar a API de Texto de Análise para devolver a saída tokenizada para que possa ver exatamente o que um analisador está a criar para o seu índice.

+ [Analisadores de idiomas](search-language-support.md)
+ [Analisadores para processamento de texto em Pesquisa Cognitiva Azure](search-analyzers.md)
+ [Analisar API de texto (REST)](/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa completa de texto (arquitetura de consulta)](search-lucene-query-architecture.md)