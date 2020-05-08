---
title: Termos parciais, padrões e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Utilize perguntas wildcard, regex e prefixo para combinar em termos inteiros ou parciais num pedido de consulta de pesquisa cognitiva Azure. Padrões difíceis de combinar que incluem caracteres especiais podem ser resolvidos usando sintaxe de consulta completa e analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592048"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Pesquisa parcial de termo e padrões com caracteres especiais (wildcard, regex, padrões)

Uma *pesquisa a termo parcial* refere-se a consultas compostas por fragmentos de termo, onde em vez de um termo inteiro, você pode ter apenas o início, meio ou fim do termo (por vezes referido como prefixo, infixo ou consultas de sufixo). Um *padrão* pode uma combinação de fragmentos, muitas vezes com caracteres especiais como traços ou cortes que fazem parte da corda de consulta. Os casos comuns de utilização incluem consulta de porções de um número de telefone, URL, pessoas ou códigos de produto, ou palavras compostas.

A pesquisa parcial e padrão pode ser problemática se o índice não tiver termos no formato esperado. Durante a fase de [análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) da indexação (assumindo o analisador padrão padrão padrão padrão), os caracteres especiais são descartados, as cordas compostas e compostas são divididas, e o espaço branco é eliminado; tudo o que pode fazer com que as consultas de padrão falhem quando não é encontrada correspondência. Por exemplo, um `+1 (425) 703-6214` número de telefone `"1"` `"425"`como `"703"` `"6214"`(tokened as, `"3-62"` , , ) não aparece numa consulta porque esse conteúdo não existe realmente no índice. 

A solução é invocar um analisador que preserve uma cadeia completa, incluindo espaços e caracteres especiais, se necessário, para que possa combinar em termos e padrões parciais. Criar um campo adicional para uma cadeia intacta, além de usar um analisador de preservação de conteúdo, é a base da solução.

> [!TIP]
> Familiarizado com o Carteiro e as APIs do REST? [Descarregue a coleção](https://github.com/Azure-Samples/azure-search-postman-samples/) de exemplos de consulta para consultar termos parciais e caracteres especiais descritos neste artigo.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>O que é pesquisa parcial na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, a pesquisa parcial e o padrão estão disponíveis nestas formas:

+ [Prefixo pesquisa](query-simple-syntax.md#prefix-search), `search=cap*`como , combinando em "Cap'n Jack's Waterfront Inn" ou "Gacc Capital". Pode utilizar a sintaxe de consulta simples ou a sintaxe de consulta lucene completa para pesquisa prefixo.

+ [Pesquisa de Wildcard](query-lucene-syntax.md#bkmk_wildcard) ou [expressões regulares](query-lucene-syntax.md#bkmk_regex) que procuram um padrão ou partes de uma corda embutida. Wildcard e expressões regulares requerem a sintaxe lucene completa. As consultas de sufixo e índice são formuladas como uma expressão regular.

  Alguns exemplos de pesquisa parcial de termo incluem o seguinte. Para uma consulta de sufixo, dado o termo "alfanumérico", usaria uma pesquisa de wildcard para`search=/.*numeric.*/`encontrar uma correspondência. Para um termo parcial que inclui caracteres interiores, como um fragmento de URL, você pode precisar adicionar caracteres de fuga. Na JSON, um `/` corte para a `\`frente é escapado com um corte para trás . Como tal, `search=/.*microsoft.com\/azure\/.*/` é a sintaxe para o fragmento de URL "microsoft.com/azure/".

Como referido, todos os acima referidos exigem que o índice contenha cordas num formato propício à correspondência de padrões, que o analisador padrão não fornece. Seguindo os passos deste artigo, pode garantir que existem os conteúdos necessários para suportar estes cenários.

## <a name="solving-partialpattern-search-problems"></a>Resolver problemas de pesquisa parcial/padrão

Quando precisa pesquisar fragmentos ou padrões ou caracteres especiais, pode substituir o analisador predefinido com um analisador personalizado que opera sob regras de tokenização mais simples, mantendo toda a cadeia. Dando um passo para trás, a abordagem é a seguinte:

+ Defina um campo para armazenar uma versão intacta da cadeia (assumindo que quer texto analisado e não analisado)
+ Escolha um analisador predefinido ou defina um analisador personalizado para obter uma corda intacta não analisada
+ Atribuir o analisador personalizado ao campo
+ Construir e testar o índice

> [!TIP]
> Avaliar os analisadores é um processo iterativo que requer reconstruções frequentes do índice. Pode facilitar este passo utilizando o Carteiro, as APIs REST para [Criar Índice,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Eliminar Índice,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Carregar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e Documentos de [Pesquisa.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Para documentos de carga, o organismo de pedido deve conter um pequeno conjunto de dados representativo que pretende testar (por exemplo, um campo com números de telefone ou códigos de produto). Com estas APIs na mesma coleção de Carteiro, você pode pedalar através destes passos rapidamente.

## <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para diferentes cenários

Os analisadores são atribuídos numa base por campo, o que significa que pode criar campos no seu índice para otimizar para diferentes cenários. Especificamente, pode definir "featureCode" e "featureCodeRegex" para suportar uma pesquisa regular de texto completo no primeiro, e padrão avançado correspondente no segundo.

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

Ao escolher um analisador que produz fichas de todo o termo, os seguintes analisadores são escolhas comuns:

| Analisador | Comportamentos |
|----------|-----------|
| [analisadores de linguagem](index-add-language-analyzers.md) | Preserva os hífenes em palavras ou cordas compostas, mutações vogais e formas de verbo. Se os padrões de consulta incluem traços, usar um analisador de linguagem pode ser suficiente. |
| [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é tokenizado como um único termo. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa-se apenas em espaços brancos. Termos que incluem traços ou outros caracteres são tratados como um único símbolo. |
| [analisador personalizado](index-add-custom-analyzers.md) | (recomendado) A criação de um analisador personalizado permite especificar tanto o tokenizer como o filtro de fichas. Os analisadores anteriores devem ser utilizados como está. Um analisador personalizado permite-lhe escolher quais tokenizers e filtros de fichas para usar. <br><br>Uma combinação recomendada é o [tokenizer de palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) com um [filtro de ficha inferior](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Por si só, o analisador de [palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não diminui qualquer texto maiúsculo, o que pode fazer com que as consultas falhem. Um analisador personalizado dá-lhe um mecanismo para adicionar o filtro de ficha sinuoso. |

Se estiver a utilizar uma ferramenta de teste web API como o Carteiro, pode adicionar a [chamada DE TESTE ANALISADOR REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para inspecionar a saída tokenizada.

Deve ter um índice existente para trabalhar. Dado um índice existente e um campo que contenha traços ou termos parciais, pode experimentar vários analisadores em termos específicos para ver quais os tokens emitidos.  

1. Verifique o analisador Standard para ver como os termos são tokenizados por padrão.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Avalie a resposta para ver como o texto é tokenizado dentro do índice. Repare como cada termo é minúsculo e separado.

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
1. Modificar o pedido `whitespace` de `keyword` utilização do ou analisador:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Agora a resposta consiste de um único símbolo, maiúsculo, com traços preservados como parte da corda. Se precisar de pesquisar um padrão ou um termo parcial, o motor de consulta tem agora a base para encontrar uma correspondência.


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
> Esteja ciente de que os parsers de consulta muitas vezes em termos minúsculos em uma expressão de pesquisa ao construir a árvore de consulta. Se estiver a utilizar um analisador que não reduz as inputs de texto, e não está a obter resultados esperados, esta pode ser a razão. A solução é adicionar um filtro de ficha sinuoso inferior, conforme descrito na secção "Utilizar os analisadores personalizados" abaixo.

## <a name="configure-an-analyzer"></a>Configure um analisador
 
Quer esteja a avaliar os analisadores ou a avançar com uma configuração específica, terá de especificar o analisador na definição de campo e, possivelmente, configurar o próprio analisador se não estiver a utilizar um analisador incorporado. Ao trocar os analisadores, normalmente é necessário reconstruir o índice (baixar, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Utilizar analisadores incorporados

Os analisadores incorporados ou predefinidos podem `analyzer` ser especificados pelo nome numa propriedade de uma definição de campo, sem nenhuma configuração adicional necessária no índice. O exemplo que se segue `whitespace` demonstra como colocaria o analisador num campo. 

Para outros cenários e para saber mais sobre outros analisadores incorporados, consulte a lista de [analisadores predefinidos](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

Se estiver a utilizar um [analisador personalizado,](index-add-custom-analyzers.md)defina-o no índice com uma combinação definida pelo utilizador de tokenizer, filtro de fichas, com possíveis configurações de configuração. Em seguida, remecite-o numa definição de campo, assim como um analisador incorporado.

Quando o objetivo é a tokenização a termo inteiro, recomenda-se um analisador personalizado que consista num **tokenizer de palavra-chave** e num filtro de **token de minúscula.**

+ O tokenizer de palavra-chave cria um único símbolo para todo o conteúdo de um campo.
+ O filtro de ficha minúscula transforma letras maiúsculas em texto minúsculo. Os parsers de consulta normalmente diminuem qualquer entrada de texto maiúscula. O invólucro inferior homogeneiza as inputs com os termos tokenizados.

O exemplo seguinte ilustra um analisador personalizado que fornece o tokenizer de palavra-chave e um filtro de ficha minúscula.

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
> O `keyword_v2` tokenizer `lowercase` e o filtro de fichas são conhecidos do sistema e utilizam as suas configurações predefinidas, razão pela qual pode referencia-los pelo nome sem ter que defini-los primeiro.

## <a name="build-and-test"></a>Compilar e testar

Uma vez definido um índice com analisadores e definições de campo que suportam o seu cenário, carregue documentos que tenham cordas representativas para que possa testar consultas parciais de cordas. 

As secções anteriores explicaram a lógica. Esta secção atravessa cada API a que deve ligar ao testar a sua solução. Como anteriormente notado, se utilizar uma ferramenta de teste web interativa, como o Carteiro, pode passar por estas tarefas rapidamente.

+ [Delete Index](https://docs.microsoft.com/rest/api/searchservice/delete-index) remove um índice existente com o mesmo nome para que possa recriá-lo.

+ [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) cria a estrutura de índice no seu serviço de pesquisa, incluindo definições de analisadores e campos com uma especificação de analisador.

+ [Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importa documentos com a mesma estrutura que o seu índice, bem como conteúdo pesquisável. Após este passo, o seu índice está pronto para consultar ou testar.

+ [O Analisador](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) de Teste foi introduzido em [Escolha um analisador](#choose-an-analyzer). Teste algumas das cordas do seu índice usando uma variedade de analisadores para entender como os termos são tokenizados.

+ [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) de pesquisa explica como construir um pedido de consulta, usando [sintaxe simples](query-simple-syntax.md) ou [sintaxe lucene completa](query-lucene-syntax.md) para wildcard e expressões regulares.

  Para consultas a termo parcial, como consultar "3-6214" para encontrar uma correspondência em "+1 (425) 703-6214", pode utilizar a simples sintaxe: `search=3-6214&queryType=simple`.

  Para consultas de infixo e sufixo, tais como consulta "num" ou "numérico para encontrar uma correspondência em alfanumérico", use a sintaxe lucene completa e uma expressão regular:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas

### <a name="tune-query-performance"></a>Otimizar o desempenho de consultas

Se implementar a configuração recomendada que inclui o keyword_v2 tokenizer e o filtro de fichas minúsculas, poderá notar uma diminuição do desempenho da consulta devido ao processamento adicional de filtros simbólicos sobre as fichas existentes no seu índice. 

O exemplo seguinte adiciona um [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) para tornar as correspondências prefixomais rápidas. São geradas tokens adicionais em combinações de caracteres 2-25 que incluem caracteres: (não só MS, MSF, MSFT, MSFT, MSFT/, MSFT/S, MSFT/S, MSFT/SQ, MSFT/SQL). Como pode imaginar, a tokenização adicional resulta num índice maior.

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Utilize diferentes analisadores para indexação e processamento de consultas

Os analisadores são chamados durante a indexação e durante a execução da consulta. É comum usar o mesmo analisador para ambos, mas pode configurar analisadores personalizados para cada carga de trabalho. As sobreposições do analisador são especificadas na [definição](https://docs.microsoft.com/rest/api/searchservice/create-index) de índice numa `analyzers` secção e, em seguida, referenciadas em campos específicos. 

Quando a análise personalizada é necessária apenas durante a indexação, pode aplicar o analisador personalizado apenas indexação e continuar a usar o analisador lucene padrão (ou outro analisador) para consultas.

Para especificar a análise específica do papel, pode definir `indexAnalyzer` propriedades `searchAnalyzer` no campo `analyzer` para cada uma, definindo e em vez da propriedade predefinida.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Passos seguintes

Este artigo explica como os analisadores contribuem tanto para problemas de consulta e para resolver problemas de consulta. Como próximo passo, veja mais de perto o impacto do analisador na indexação e processamento de consultas. Em particular, considere usar a API de Texto Analisado para devolver a saída tokenizada para que possa ver exatamente o que um analisador está a criar para o seu índice.

+ [Analisadores de idiomas](search-language-support.md)
+ [Analisadores para processamento de texto em Pesquisa Cognitiva Azure](search-analyzers.md)
+ [Analisar a API de Texto (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa completa de texto (arquitetura de consulta)](search-lucene-query-architecture.md)