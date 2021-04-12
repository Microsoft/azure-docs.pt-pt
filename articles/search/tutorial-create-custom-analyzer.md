---
title: 'Tutorial: criar um analisador personalizado'
titleSuffix: Azure Cognitive Search
description: Aprenda a construir um analisador personalizado para melhorar a qualidade dos resultados da pesquisa na Azure Cognitive Search.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99097661"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Tutorial: Criar um analisador personalizado para números de telefone

[Os analisadores](search-analyzers.md) são um componente chave em qualquer solução de pesquisa. Para melhorar a qualidade dos resultados da pesquisa, é importante entender como os analisadores funcionam e impactam estes resultados.

Em alguns casos, como com um campo de texto gratuito, simplesmente selecionar o [analisador de idiomas](index-add-language-analyzers.md) correto melhorará os resultados da pesquisa. No entanto, alguns cenários como pesquisar com precisão números de telefone, URLs ou e-mails podem exigir o uso de analisadores personalizados.

Este tutorial utiliza [apis](/rest/api/searchservice/) de descanso do Carteiro e da Azure Cognitive Search para:

> [!div class="checklist"]
> * Explique como os analisadores funcionam
> * Defina um analisador personalizado para pesquisar números de telefone
> * Teste como o analisador personalizado tokenize texto
> * Criar analisadores separados para indexar e procurar melhorar ainda mais os resultados

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes serviços e ferramentas para este tutorial.

+ [Aplicação de ambiente de trabalho Postman](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Transferir ficheiros

O código-fonte para este tutorial está na pasta [de analisadores personalizados](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) no repositório [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub.

## <a name="1---create-azure-cognitive-search-service"></a>1 - Criar serviço de Pesquisa Cognitiva Azure

Para completar este tutorial, você precisará de um serviço de Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Free para completar este walkthrough.

Para o próximo passo, terá de saber o nome do seu serviço de pesquisa e da sua Chave API. Se não tem a certeza de como encontrar esses itens, confira este [arranque rápido.](search-create-service-portal.md#get-a-key-and-url-endpoint)


## <a name="2---set-up-postman"></a>2 - Configurar carteiro

Em seguida, inicie o Carteiro e importe a coleção que descarregou a partir de [amostras Azure-Samples/azure-search-postman.](https://github.com/Azure-Samples/azure-search-postman-samples)

Para importar a coleção, vá a **Files**  >  **Import,** em seguida, selecione o ficheiro de cobrança que pretende importar.

Para cada pedido, tem de:

1. Substitua `<YOUR-SEARCH-SERVICE>` pelo nome do seu serviço de pesquisa.

1. `<YOUR-ADMIN-API-KEY>`Substitua-a pela chave primária ou secundária do seu serviço de pesquisa.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Carteiro solicita URL e cabeçalho" border="false":::

Se não está familiarizado com o Carteiro, consulte [o Explore Azure Cognitive Search REST APIs](search-get-started-rest.md).

## <a name="3---create-an-initial-index"></a>3 - Criar um índice inicial

Neste passo, vamos criar um índice inicial, carregar documentos no índice e, em seguida, consultar os documentos para ver como as nossas pesquisas iniciais funcionam.

### <a name="create-index"></a>Criar índice

Começaremos por criar um índice simples chamado `tutorial-basic-index` com dois campos: `id` e `phone_number` . Ainda não definimos um analisador, pelo que o `standard.lucene` analisador será usado por padrão.

Para criar o índice, enviamos o seguinte pedido:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Carregar dados

Em seguida, vamos carregar dados no índice. Em alguns casos, pode não ter controlo sobre o formato dos números de telefone ingeridos para que possamos testar contra diferentes tipos de formatos. Idealmente, uma solução de pesquisa irá devolver todos os números de telefone correspondentes, independentemente do seu formato.

Os dados são carregados no índice utilizando o seguinte pedido: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Com os dados no índice, estamos prontos para começar a procurar.

### <a name="search"></a>Pesquisar

Para tornar a pesquisa intuitiva, o melhor é não esperar que os utilizadores formatem consultas de uma forma específica. Um utilizador pode pesquisar `(425) 555-0100` em qualquer um dos formatos que mostramos acima e ainda espera que os resultados sejam devolvidos. Neste passo, vamos testar algumas consultas de amostra para ver como se comportam.

Começamos por `(425) 555-0100` pesquisar:

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Esta consulta devolve **três dos quatro resultados esperados,** mas também devolve **dois resultados inesperados:**

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Em seguida, vamos procurar um número sem qualquer formatação `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Esta consulta faz ainda pior, devolvendo apenas **um dos quatro jogos corretos.**

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Se achas estes resultados confusos, não estás sozinho. Na próxima secção, vamos descobrir porque estamos a obter estes resultados.

## <a name="4---debug-search-results"></a>4 - Resultados da pesquisa de Debug

Para compreender estes resultados de pesquisa, é importante primeiro entender como os analisadores funcionam. A partir daí, podemos testar o analisador padrão usando a [API de Texto de Análise](/rest/api/searchservice/test-analyzer) e, em seguida, criar um analisador que atenda às nossas necessidades.

### <a name="how-analyzers-work"></a>Como os analisadores funcionam

Um [analisador](search-analyzers.md) é um componente do motor de pesquisa de [texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de consulta e documentos indexados. Diferentes analisadores manipulam texto de diferentes maneiras dependendo do cenário. Para este cenário, precisamos de construir um analisador adaptado aos números de telefone.

Os analisadores consistem em três componentes:

+ [**Filtros de caracteres**](#CharFilters) que removem ou substituem caracteres individuais do texto de entrada.
+ Um [**Tokenizer**](#Tokenizers) que quebra o texto de entrada em fichas, que se tornam chaves no índice de pesquisa.
+ [**Filtros token que**](#TokenFilters) manipulam os tokens produzidos pelo tokenizer.

No diagrama abaixo, pode ver como estes três componentes trabalham em conjunto para tokenizar uma frase:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Diagrama do processo do Analisador para tokenizar uma frase":::

Estes tokens são então armazenados num índice invertido, o que permite pesquisas rápidas e completas de texto.  Um índice invertido permite a pesquisa por texto completo mapeando todos os termos únicos extraídos durante a análise lexical aos documentos em que ocorrem. Pode ver um exemplo no diagrama abaixo:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Índice invertido de exemplo":::

Toda a procura se resume a procurar os termos armazenados no índice invertido. Quando um utilizador emite uma consulta:

1. A consulta é analisada e os termos de consulta são analisados.
1. O índice invertido é então digitalizado para documentos com termos correspondentes.
1. Finalmente, os documentos recuperados são classificados pelo [algoritmo de semelhança.](index-ranking-similarity.md)

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Diagrama do processo do Analisador classificando a semelhança":::

Se os termos de consulta não corresponderem aos termos do seu índice invertido, os resultados não serão devolvidos. Para saber mais sobre como funcionam as consultas, consulte este artigo na pesquisa completa por [texto.](search-lucene-query-architecture.md)

> [!Note]
> [Consultas a prazo parcial](search-query-partial-matching.md) são uma exceção importante a esta regra. Estas consultas (consulta de prefixo, consulta wildcard, consulta de regex) contornam o processo de análise lexical ao contrário de consultas de prazo regular. Os termos parciais são apenas mais baixos antes de serem correspondidos com os termos do índice. Se um analisador não estiver configurado para suportar este tipo de consultas, muitas vezes receberá resultados inesperados porque os termos correspondentes não existem no índice.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Analisador de teste usando a API de Texto de Análise

A Azure Cognitive Search fornece uma [API de Texto de Análise](/rest/api/searchservice/test-analyzer) que permite testar os analisadores para entender como processam o texto.

A API de Texto de Análise é chamada utilizando o seguinte pedido:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

Em seguida, a API devolve uma lista dos tokens extraídos do texto. Pode ver que o analisador padrão lucene divide o número de telefone em três fichas separadas:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Inversamente, o número de telefone `4255550100` formatado sem qualquer pontuação é tokenizado num único token.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Tenha em mente que tanto os termos de consulta como os documentos indexados são analisados. Recordando os resultados da pesquisa do passo anterior, podemos começar a perceber por que esses resultados foram devolvidos.

Na primeira consulta, os números de telefone incorretos foram devolvidos porque um dos seus `555` termos, correspondeu a um dos termos que procuramos. Na segunda consulta, apenas o número foi devolvido porque era o único registo que tinha um termo `4255550100` correspondente.

## <a name="5---build-a-custom-analyzer"></a>5 - Construir um analisador personalizado

Agora que entendemos os resultados que estamos a ver, vamos construir um analisador personalizado para melhorar a lógica da tokenização.

O objetivo é fornecer uma pesquisa intuitiva contra números de telefone independentemente do formato da consulta ou da cadeia indexada. Para alcançar este resultado, especificaremos um filtro de [caracteres](#CharFilters), um [tokenizer](#Tokenizers)e um [filtro simbólico.](#TokenFilters)

<a name="CharFilters"></a>

### <a name="character-filters"></a>Filtros de caracteres

Os filtros de caracteres são usados para processar texto antes de ser introduzido no tokenizer. As utilizações comuns dos filtros de caracteres incluem filtrar elementos HTML ou substituir caracteres especiais.

Para números de telefone, queremos remover whitespace e caracteres especiais porque nem todos os formatos de números de telefone contêm os mesmos caracteres e espaços especiais.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

O filtro acima removerá `-` `(` `)` `+` `.` e os espaços da entrada.

|Input|Saída|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizers

Os tokenizers dividem o texto em fichas e descartam alguns caracteres, como a pontuação, ao longo do caminho. Em muitos casos, o objetivo da tokenização é dividir uma frase em palavras individuais.

Para este cenário, usaremos um tokenizer de palavras-chave, `keyword_v2` porque queremos capturar o número de telefone como um único termo. Note que esta não é a única maneira de resolver este problema. Consulte a secção [de abordagens Alternativa](#Alternate) abaixo.

Os tokenizers de palavra-chave sempre derão o mesmo texto que foi dado como um único termo.

|Input|Saída|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Filtros token

Os filtros token filtrarão ou modificarão os tokens gerados pelo tokenizer. Um uso comum de um filtro simbólico é reduzir todos os caracteres usando um filtro de ficha minúscula. Outra utilização comum é filtrar as palavras de ordem `the` `and` como, ou `is` .

Embora não precisemos de usar nenhum desses filtros para este cenário, usaremos um filtro de fichas nGram para permitir pesquisas parciais de números de telefone.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

O [filtro de fichas nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) divide fichas em n-gramas de um dado tamanho com base nos `minGram` parâmetros e `maxGram` parâmetros.

Para o analisador de telefone, definimos `minGram` para porque este é o `3` sublpesto mais curto que esperamos que os utilizadores procurem. `maxGram` está definido `20` para garantir que todos os números de telefone, mesmo com extensões, caberão num único n-grama.

 O infeliz efeito colateral de n-gramas é que alguns falsos positivos serão devolvidos. Vamos corrigir isto no passo 7 construindo um analisador separado para pesquisas que não incluam o filtro n-gram token.

|Input|Saída|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analisador

Com os nossos filtros de personagens, tokenizer e filtros simbólicos no lugar, estamos prontos para definir o nosso analisador.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Input|Saída|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Note que qualquer uma das fichas na saída pode agora ser revistada. Se a nossa consulta incluir algum desses tokens, o número de telefone será devolvido.

Com o analisador personalizado definido, recrie o índice para que o analisador personalizado esteja disponível para testes no passo seguinte. Para simplificar, a coleção Do Carteiro cria um novo índice nomeado `tutorial-first-analyzer` com o analisador que definimos.

## <a name="6---test-the-custom-analyzer"></a>6 - Testar o analisador personalizado

Depois de criar o índice, pode agora testar o analisador que criamos usando o seguinte pedido:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Poderá então ver a recolha de fichas resultantes do número de telefone:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - Construir um analisador personalizado para consultas

Depois de fazer algumas consultas de amostra contra o índice com o analisador personalizado, você vai descobrir que a recuperação melhorou e todos os números de telefone correspondentes são agora devolvidos. No entanto, o filtro de ficha n-gram também faz com que alguns falsos positivos sejam devolvidos. Este é um efeito colateral comum de um filtro de token n-gram.

Para evitar falsos positivos, criaremos um analisador separado para consulta. Este analisador será o mesmo que o analisador que já criámos, mas **sem** o `custom_ngram_filter` .

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

Na definição de índice, especificamos um `indexAnalyzer` e um `searchAnalyzer` .

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Com esta mudança, está tudo preparado. Recrie o índice, indexe os dados e teste novamente as consultas para verificar os trabalhos de pesquisa como esperado. Se estiver a usar a coleção Do Carteiro, criará um terceiro índice chamado `tutorial-second-analyzer` .

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Abordagens alternativas

O analisador acima foi projetado para maximizar a flexibilidade de pesquisa. No entanto, fá-lo à custa de armazenar muitos termos potencialmente sem importância no índice.

O exemplo abaixo mostra um analisador diferente que também pode ser usado para esta tarefa. 

O analisador funciona bem, exceto para dados de entrada como `14255550100` este que dificulta logicamente a recolha do número de telefone. Por exemplo, o analisador não seria capaz de separar o código do país, `1` do código de área, `425` . Esta discrepância levaria a que o número acima não fosse devolvido se um utilizador não incluísse um código de país na sua pesquisa.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Pode ver no exemplo abaixo que o número de telefone é dividido em pedaços que normalmente esperaria que um utilizador procurasse.

|Input|Saída|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

Dependendo dos seus requisitos, esta pode ser uma abordagem mais eficiente para o problema.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Para simplificar, este tutorial fez-te criar três novos índices. No entanto, é comum apagar e recriar índices durante as fases iniciais do desenvolvimento. Pode eliminar um índice no portal Azure ou utilizar a seguinte chamada API:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Conclusões

Este tutorial demonstrou o processo de construção e teste de um analisador personalizado. Criou um índice, dados indexados, e depois questionou-se contra o índice para ver que resultados de pesquisa foram devolvidos. A partir daí, usou a API de Texto de Análise para ver o processo de análise lexical em ação.

Embora o analisador definido neste tutorial ofereça uma solução fácil para pesquisar números de telefone, este mesmo processo pode ser usado para construir um analisador de telefone personalizado para qualquer cenário que possa ter.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando está a trabalhar na sua própria subscrição, é uma boa ideia remover os recursos que já não precisa no final de um projeto. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com a forma de criar um analisador personalizado, vamos dar uma olhada em todos os diferentes filtros, tokenizers e analisadores disponíveis para construir uma rica experiência de pesquisa.

> [!div class="nextstepaction"]
> [Analisadores personalizados em Pesquisa Cognitiva Azure](index-add-custom-analyzers.md)