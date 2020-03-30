---
title: Padrões de correspondência e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Utilize consultas de wildcard e prefixo para combinar em termos inteiros ou parciais num pedido de consulta de pesquisa cognitiva Azure. Padrões difíceis de combinar que incluem caracteres especiais podem ser resolvidos usando sintaxe de consulta completa e analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289316"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Combine em padrões e caracteres especiais (traços)

Para consultas que incluam`-, *, (, ), /, \, =`caracteres especiais ( ), ou para padrões de consulta baseados em termos parciais dentro de um termo maior, são normalmente necessários passos de configuração adicionais para garantir que o índice contém o conteúdo esperado, no formato certo. 

Por predefinição, `+1 (425) 703-6214` um número de `"1"` `"425"`telefone `"703"` `"6214"`como é tokenized como , , . Como pode imaginar, `"3-62"`pesquisar em termos parciais que incluem um traço, falhará porque esse conteúdo não existe realmente no índice. 

Quando você precisa pesquisar em cordas parciais ou caracteres especiais, você pode substituir o analisador padrão com um analisador personalizado que opera sob regras de tokenização mais simples, preservando termos inteiros, necessários quando as cordas de consulta incluem partes de um termo ou especial personagens. Dando um passo para trás, a abordagem é a seguinte:

+ Escolha um analisador predefinido ou defina um analisador personalizado que produza a saída desejada
+ Atribuir o analisador ao campo
+ Construa o índice e teste

Este artigo acompanha-o nestas tarefas. A abordagem aqui descrita é útil noutros cenários: perguntas de wildcard e expressão regular também precisam de termos inteiros como base para a correspondência de padrões. 

> [!TIP]
> Avaliar os analíers é um processo iterativo que requer reconstruções frequentes do índice. Pode facilitar este passo utilizando o Carteiro, as APIs REST para [Criar Índice,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Eliminar Índice,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Carregar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e Documentos de [Pesquisa.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Para documentos de carga, o organismo de pedido deve conter um pequeno conjunto de dados representativo que pretende testar (por exemplo, um campo com números de telefone ou códigos de produto). Com estas APIs na mesma coleção de Carteiro, você pode pedalar através destes passos rapidamente.

## <a name="choosing-an-analyzer"></a>Escolher um analisador

Ao escolher um analisador que produz fichas de todo o termo, os seguintes analisadores são escolhas comuns:

| Analisador | Comportamentos |
|----------|-----------|
| [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é tokenizado como um único termo. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa-se apenas em espaços brancos. Termos que incluem traços ou outros caracteres são tratados como um único símbolo. |
| [analisador personalizado](index-add-custom-analyzers.md) | (recomendado) A criação de um analisador personalizado permite especificar tanto o tokenizer como o filtro de fichas. Os analisadores anteriores devem ser utilizados como está. Um analisador personalizado permite-lhe escolher quais tokenizers e filtros de fichas para usar. <br><br>Uma combinação recomendada é o [tokenizer de palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) com um [filtro de ficha inferior](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Por si só, o analisador de [palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não diminui qualquer texto maiúsculo, o que pode fazer com que as consultas falhem. Um analisador personalizado dá-lhe um mecanismo para adicionar o filtro de ficha sinuoso. |

Se estiver a utilizar uma ferramenta de teste web API como o Carteiro, pode adicionar a [chamada DE TESTE ANALISADOR REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para inspecionar a saída tokenizada. Dado um índice existente e um campo que contenha traços ou termos parciais, pode experimentar vários analisadores em termos específicos para ver quais os tokens emitidos.  

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
> Esteja ciente de que os parsers de consulta muitas vezes em termos minúsculos em uma expressão de pesquisa ao construir a árvore de consulta. Se estiver a utilizar um analisador que não reduz as inputs de texto, e não está a obter resultados esperados, esta pode ser a razão. A solução é adicionar um filtro de token de caixa lwower.

## <a name="analyzer-definitions"></a>Definições de analisadores
 
Quer esteja a avaliar os analisadores ou a avançar com uma configuração específica, terá de especificar o analisador na definição de campo e, possivelmente, configurar o próprio analisador se não estiver a utilizar um analisador incorporado. Ao trocar os analisadores, normalmente é necessário reconstruir o índice (baixar, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Utilizar analisadores incorporados

Os analisadores incorporados ou predefinidos podem `analyzer` ser especificados pelo nome numa propriedade de uma definição de campo, sem nenhuma configuração adicional necessária no índice. O exemplo que se segue `whitespace` demonstra como colocaria o analisador num campo.

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
Para obter mais informações sobre todos os analisadores incorporados disponíveis, consulte a lista de [analisadores predefinidos](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Use analisadores personalizados

Se estiver a utilizar um [analisador personalizado,](index-add-custom-analyzers.md)defina-o no índice com uma combinação definida pelo utilizador de tokenizer, filtro de token, com possíveis configurações de configuração. Em seguida, remecite-o numa definição de campo, assim como um analisador incorporado.

Quando o objetivo é a tokenização a termo inteiro, recomenda-se um analisador personalizado que consista num **tokenizer de palavra-chave** e num filtro de **token de minúscula.**

+ O tokenizer de palavra-chave cria um único símbolo para todo o conteúdo de um campo.
+ O filtro de ficha minúscula transforma letras maiúsculas em texto minúsculo. Os parsers de consulta normalmente diminuem qualquer entrada de texto maiúscula. A redução da redução homogeneiza as inputs com os termos tokenizados.

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

### <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para diferentes cenários

Outra opção aproveita a atribuição de analisador por campo para otimizar para diferentes cenários. Especificamente, pode definir "featureCode" e "featureCodeRegex" para suportar uma pesquisa regular de texto completo no primeiro, e padrão avançado correspondente no segundo.

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Passos seguintes

Este artigo explica como os analisadores contribuem tanto para problemas de consulta e para resolver problemas de consulta. Como próximo passo, veja mais de perto o impacto do analisador na indexação e processamento de consultas. Em particular, considere usar a API de Texto Analisado para devolver a saída tokenizada para que possa ver exatamente o que um analisador está a criar para o seu índice.

+ [Analisadores de idiomas](search-language-support.md)
+ [Analisadores para processamento de texto em Pesquisa Cognitiva Azure](search-analyzers.md)
+ [Analisar a API de Texto (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa completa de texto (arquitetura de consulta)](search-lucene-query-architecture.md)
