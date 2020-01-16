---
title: Corresponder padrões e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Use as consultas curinga e de prefixo para corresponder a termos inteiros ou parciais em uma solicitação de consulta do Azure Pesquisa Cognitiva. Padrões de difícil correspondência que incluem caracteres especiais podem ser resolvidos usando a sintaxe de consulta completa e os analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989621"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Correspondência em padrões e caracteres especiais (traços)

Para consultas que incluem caracteres especiais (`-, *, (, ), /, \, =`) ou para padrões de consulta com base em termos parciais em um termo maior, normalmente são necessárias etapas de configuração adicionais para garantir que o índice contenha o conteúdo esperado, no formato correto. 

Por padrão, um número de telefone como `+1 (425) 703-6214` é indexado como `"1"`, `"425"`, `"703"``"6214"`. Como você pode imaginar, Pesquisar em `"3-62"`, termos parciais que incluem um traço, falhará porque esse conteúdo não existe realmente no índice. 

Quando você precisar pesquisar em cadeias de caracteres parciais ou especiais, poderá substituir o analisador padrão por um analisador personalizado que opera sob regras de geração de tokens mais simples, preservando os termos inteiros, necessários quando as cadeias de caracteres de consulta incluem partes de um termo ou especial personagens. Voltando um pouco, a abordagem é parecida com esta:

+ Escolha um analisador predefinido ou defina um analisador personalizado que produza a saída desejada
+ Atribuir o analisador ao campo
+ Compilar o índice e o teste

Este artigo orienta você durante essas tarefas. A abordagem descrita aqui é útil em outros cenários: as consultas de expressão regular e curinga também precisam de termos inteiros como base para correspondência de padrões. 

> [!TIP]
> Avaliar o analyers é um processo iterativo que requer recompilações de índice frequentes. Você pode tornar essa etapa mais fácil usando o postmaster, as APIs REST para [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index), [excluir índice](https://docs.microsoft.com/rest/api/searchservice/delete-index),[carregar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents). Para carregar documentos, o corpo da solicitação deve conter um pequeno conjunto de dados representativos que você deseja testar (por exemplo, um campo com números de telefone ou códigos de produto). Com essas APIs na mesma coleção do postmaster, você pode percorrer essas etapas rapidamente.

## <a name="choosing-an-analyzer"></a>Escolhendo um analisador

Ao escolher um analisador que produz tokens de termo inteiro, os seguintes analisadores são opções comuns:

| Analyzer | Comportamentos |
|----------|-----------|
| [chaves](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é indexado como um único termo. |
| [diferente](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa somente em espaços em branco. Os termos que incluem traços ou outros caracteres são tratados como um único token. |
| [analisador personalizado](index-add-custom-analyzers.md) | aconselhável A criação de um analisador personalizado permite especificar o filtro de token e criador. Os analisadores anteriores devem ser usados no estado em que se encontram. Um analisador personalizado permite que você escolha quais filtros de criadores e token usar. <br><br>Uma combinação recomendada é a [palavra-chave criador](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) com um [filtro de token em](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)minúsculas. Por si só, o [analisador de palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não faz minúsculas em texto em letras maiúsculas, o que pode fazer com que as consultas falhem. Um analisador personalizado oferece um mecanismo para adicionar o filtro de token em minúsculas. |

Se você estiver usando uma ferramenta de teste de API da Web como o postmaster, poderá adicionar a [chamada REST do analisador de teste](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para inspecionar a saída com tokens. Dado um índice existente e um campo contendo traços ou termos parciais, você pode experimentar vários analisadores sobre termos específicos para ver quais tokens são emitidos.  

1. Verifique o analisador padrão para ver como os termos são indexados por padrão.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Avalie a resposta para ver como o texto é indexado no índice. Observe como cada termo é inferior e dividido.

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
1. Modifique a solicitação para usar o `whitespace` ou o analisador de `keyword`:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Agora, a resposta consiste em um único token, com maiúsculas e minúsculas, com traços preservados como parte da cadeia de caracteres. Se você precisar pesquisar em um padrão ou em um termo parcial, o mecanismo de consulta agora terá a base para encontrar uma correspondência.


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
> Lembre-se de que os analisadores de consulta geralmente são os termos minúsculos em uma expressão de pesquisa ao criar a árvore de consulta. Se você estiver usando um analisador que não faz entradas de texto em letras minúsculas e não estiver obtendo resultados esperados, isso pode ser o motivo. A solução é adicionar um filtro de token lwower.

## <a name="analyzer-definitions"></a>Definições do analisador
 
Se você estiver avaliando analisadores ou avançando com uma configuração específica, será necessário especificar o analisador na definição de campo e, possivelmente, configurar o próprio analisador se você não estiver usando um analisador interno. Ao trocar analisadores, normalmente você precisa recompilar o índice (remover, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Usar analisadores internos

Os analisadores internos ou predefinidos podem ser especificados pelo nome em uma propriedade `analyzer` de uma definição de campo, sem nenhuma configuração adicional necessária no índice. O exemplo a seguir demonstra como você definiria o `whitespace` Analyzer em um campo.

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
Para obter mais informações sobre todos os analisadores internos disponíveis, consulte a [lista de analisadores predefinidos](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Usar analisadores personalizados

Se você estiver usando um [analisador personalizado](index-add-custom-analyzers.md), defina-o no índice com uma combinação definida pelo usuário de criador, tokenfilter, com possíveis parâmetros de configuração. Em seguida, faça referência a ela em uma definição de campo, exatamente como você faria com um analisador interno.

Quando o objetivo é a geração de tokens de longo prazo, é recomendado um analisador personalizado que consiste em uma **palavra-chave criador** e um **filtro de token com letras minúsculas** .

+ A palavra-chave criador cria um único token para todo o conteúdo de um campo.
+ O filtro de token em minúsculas transforma letras maiúsculas em texto em minúsculas. Analisadores de consulta geralmente são minúsculos quaisquer entradas de texto em maiúsculas. Minúsculas homogenizes as entradas com os termos com token.

O exemplo a seguir ilustra um analisador personalizado que fornece a palavra-chave criador e um filtro de token em minúsculas.

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
]

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
> O filtro de token `keyword_v2` criador e `lowercase` é conhecido pelo sistema e usando suas configurações padrão, motivo pelo qual você pode referenciá-los por nome sem precisar defini-los primeiro.

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas

### <a name="tune-query-performance"></a>Otimizar o desempenho de consultas

Se você implementar a configuração recomendada que inclui o keyword_v2 criador e o filtro de token em letras minúsculas, poderá notar uma redução no desempenho da consulta devido ao processamento adicional de filtro de token sobre os tokens existentes no índice. 

O exemplo a seguir adiciona um [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) para fazer correspondências de prefixo mais rapidamente. Tokens adicionais são gerados em combinações de caracteres de 2-25 que incluem caracteres: (não apenas MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Como você pode imaginar, a geração de tokens adicional resulta em um índice maior.

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
]

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Usar analisadores diferentes para indexação e processamento de consulta

Os analisadores são chamados durante a indexação e durante a execução da consulta. É comum usar o mesmo analisador para ambos, mas você pode configurar analisadores personalizados para cada carga de trabalho. As substituições do analisador são especificadas na [definição do índice](https://docs.microsoft.com/rest/api/searchservice/create-index) em uma `analyzers` seção e, em seguida, referenciadas em campos específicos. 

Quando a análise personalizada é necessária apenas durante a indexação, você pode aplicar o analisador personalizado para apenas indexar e continuar a usar o analisador Lucene padrão (ou outro analisador) para consultas.

Para especificar a análise específica de função, você pode definir propriedades no campo para cada uma, definindo `indexAnalyzer` e `searchAnalyzer` em vez da propriedade de `analyzer` padrão.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para cenários diferentes

Outra opção aproveita a atribuição do analisador por campo para otimizar para cenários diferentes. Especificamente, você pode definir "featureCode" e "featureCodeRegex" para dar suporte à pesquisa de texto completo regular na primeira e à correspondência de padrão avançada no segundo.

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

Este artigo explica como os analisadores contribuem para problemas de consulta e solucionam problemas de consulta. Como uma próxima etapa, examine atentamente o impacto do analisador sobre a indexação e o processamento de consultas. Em particular, considere usar a API de análise de texto para retornar a saída com tokens para que você possa ver exatamente o que um analisador está criando para o índice.

+ [Analisadores de idiomas](search-language-support.md)
+ [Analisadores para processamento de texto no Azure Pesquisa Cognitiva](search-analyzers.md)
+ [API de análise de texto (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa de texto completo (arquitetura de consulta)](search-lucene-query-architecture.md)