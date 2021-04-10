---
title: Normalização de texto para filtros, facetas, tipo
titleSuffix: Azure Cognitive Search
description: Especifique os normalizadores para os campos de texto num índice para personalizar o comportamento rigoroso da palavra-chave correspondente na filtragem, faceta e classificação.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609526"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalização de texto para filtragem, triagem e triagem de casos insensíveis

 > [!IMPORTANT]
 > O Normalizer está em pré-visualização pública, disponível através da **API REST 2020-06-30-preview**. As funcionalidades de pré-visualização são oferecidas como está, nos Termos Complementares de Utilização.

Procurar e recuperar documentos de um índice de Pesquisa Cognitiva Azure requer a correspondência da consulta com o conteúdo do documento. O conteúdo pode ser analisado para produzir fichas para combinar como é o caso quando o `search` parâmetro é usado, ou pode ser usado como-é para palavras-chave rígidas que combinam como visto com `$filter` , e `facets` `$orderby` . Esta abordagem tudo-ou-nada abrange a maioria dos cenários, mas fica aquém do que é necessário um simples pré-processamento como o invólucro, a remoção do sotaque, asciifolding e assim por diante sem passar por toda a cadeia de análise.

Considere os seguintes exemplos:

+ `$filter=City eq 'Las Vegas'` só devolverá documentos que contenham o texto exato "Las Vegas" e exclua documentos com "LAS VEGAS" e "las vegas" o que é inadequado quando o caso de uso requer todos os documentos, independentemente do invólucro.

+ `search=*&facet=City,count:5` vai devolver "Las Vegas", "LAS VEGAS" e "Las Vegas" como valores distintos, apesar de ser a mesma cidade.

+ `search=usa&$orderby=City` vai devolver as cidades por ordem lexicográfica: "Las Vegas", "Seattle", "las vegas", mesmo que a intenção seja encomendar as mesmas cidades juntos, independentemente do caso. 

## <a name="normalizers"></a>Normalizadores

Um *normalizador* é um componente do motor de busca responsável pelo texto de pré-processamento para correspondência de palavras-chave. Os normalizadores são semelhantes aos analisadores, exceto que não simbolizam a consulta. Algumas das transformações que podem ser alcançadas usando normalizadores são:

+ Converta-se em minúsculas ou maiúsculas.
+ Normalize sotaques e diacríticos como ö ou ê para caracteres equivalentes ASCII "o" e "e".
+ Mapear caracteres como `-` e whitespace em um personagem especificado pelo utilizador.

Os normalizadores podem ser especificados nos campos de texto do índice e são aplicados tanto na indexação como na execução de consultas.

## <a name="predefined-and-custom-normalizers"></a>Normalizadores predefinidos e personalizados 

A Azure Cognitive Search suporta normalizadores predefinidos para casos de uso comuns, juntamente com a capacidade de personalizar conforme necessário.

| Categoria | Descrição |
|----------|-------------|
| [Normalizadores predefinidos](#predefined-normalizers) | Fornecido fora da caixa e pode ser usado sem qualquer configuração. |
|[Normalizadores personalizados](#add-custom-normalizers) | Para cenários avançados. Requer uma configuração definida pelo utilizador de uma combinação de elementos existentes, consistindo de filtros de carvão e ficha. <sup>1</sup>|

<sup>(1)</sup> Os normalizadores personalizados não especificam os tokenizers, uma vez que os normalizadores produzem sempre um único símbolo.

## <a name="how-to-specify-normalizers"></a>Como especificar os normalizadores

Os normalizadores podem ser especificados por campo em campos de texto `Edm.String` (e `Collection(Edm.String)` ) que têm pelo menos um de , ou propriedades `filterable` `sortable` `facetable` definidas como verdadeiras. Definir um normalizador é opcional e é `null` por defeito. Recomendamos a avaliação dos normalizadores predefinidos antes de configurar um personalizado para facilitar a utilização. Experimente um normalizador diferente se não forem esperados resultados.

Os normalizadores só podem ser especificados quando um novo campo é adicionado ao índice. É encorajado a avaliar antecipadamente as necessidades de normalização e atribuir normalizadores nas fases iniciais de desenvolvimento ao cair e recriar índices é rotina. Os normalizadores não podem ser especificados num campo que já foi criado.

1. Ao criar uma definição de campo no [índice,](/rest/api/searchservice/create-index)defina a propriedade  **do normalizador** para um dos [seguintes: um normalizador predefinido](#predefined-normalizers) `lowercase` como, tal, ou um normalizador personalizado (definido no mesmo esquema de índice).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Os normalizadores personalizados têm de ser definidos na secção **[normalizadores]** do índice primeiro e, em seguida, ser atribuídos à definição de campo como mostrado no passo anterior. Para obter mais informações, consulte [Criar Índice](/rest/api/searchservice/create-index) e também [adicionar normalizadores personalizados.](#add-custom-normalizers)


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Para alterar o normalizador de um campo existente, terá de reconstruir completamente o índice (não é possível reconstruir campos individuais).

Uma boa solução para os índices de produção, onde os índices de reconstrução são dispendiosos, é criar um novo campo idêntico ao antigo, mas com o novo normalizador, e usá-lo no lugar do antigo. Utilize o [Índice de Atualização](/rest/api/searchservice/update-index) para incorporar o novo campo e [fundir oOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) para o povoar. Mais tarde, como parte da manutenção planeada do índice, pode limpar o índice para remover campos obsoletos.

## <a name="add-custom-normalizers"></a>Adicionar normalizadores personalizados

Os normalizadores personalizados são definidos dentro do esquema de índice e podem ser especificados usando a propriedade de campo. A definição de normalizador personalizado inclui um nome, um tipo, um ou mais filtros de carvão e filtros simbólicos. Os filtros de carvão e os filtros simbólicos são os blocos de construção para um normalizador personalizado e responsáveis pelo processamento do texto. Estes filtros são aplicados da esquerda para a direita.

 O `token_filter_name_1` nome do filtro token é o nome do filtro simbólico e são os `char_filter_name_1` `char_filter_name_2` nomes dos filtros de carvão (ver [filtros de fichas suportados](#supported-token-filters) e quadros de filtros Char abaixo para valores válidos).

A definição de normalizador é uma parte do índice maior. Consulte [a API do Índice de Criação](/rest/api/searchservice/create-index) para obter informações sobre o resto do índice.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Os normalizadores personalizados podem ser adicionados durante a criação de índices ou mais tarde atualizando um existente. A adição de um normalizador personalizado a um índice existente requer que a bandeira **de tempo de admissão permita** ser especificada no Índice de [Atualização](/rest/api/searchservice/update-index) e fará com que o índice fique indisponível durante alguns segundos.

## <a name="normalizers-reference"></a>Referência dos normalizadores

### <a name="predefined-normalizers"></a>Normalizadores predefinidos
|**Nome**|**Descrição e Opções**|  
|-|-|  
|padrão| Minúsculas, o texto seguido de asciifolding.|  
|minúscula| Transforma os caracteres em minúsculas.|
|maiúscula| Transforma os caracteres em maiúsculas.|
|asciifolding| Transforma caracteres que não estão no bloco Basic Latin Unicode para o seu equivalente ASCII, se existir. Por exemplo, mudar à.|
|elisão| Remove a elissão do início dos tokens.|

### <a name="supported-char-filters"></a>Filtros de carvão suportados
Para obter mais detalhes sobre os filtros de carvão, consulte a [Referência dos Filtros de Char](index-add-custom-analyzers.md#CharFilter).
+ [mapeamento](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Filtros simbólicos suportados
A lista abaixo mostra os filtros simbólicos suportados para os normalizadores e é um subconjunto dos filtros gerais de fichas envolvidos na análise lexical. Para obter mais detalhes sobre os filtros, consulte a [Referência dos Filtros Token](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elisão](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [minúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [maiúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Exemplo de normalizador personalizado

O exemplo abaixo ilustra uma definição de normalizador personalizado com filtros de carvão correspondentes e filtros de fichas. As opções personalizadas para filtros de carvão e filtros de fichas são especificadas separadamente como construções nomeadas e, em seguida, referenciadas na definição de normalizador como ilustrado abaixo.

* Um normalizador personalizado "my_custom_normalizer" é definido na `normalizers` secção da definição de índice.
* O normalizador é composto por dois filtros de carvão e três filtros simbólicos: elisão, minúscula e filtro asciifolding personalizado "my_asciifolding".
* O primeiro filtro de carvão "map_dash" substitui todos os traços por sublinhados enquanto o segundo "remove_whitespace" remove todos os espaços.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Ver também
+ [Analisadores para processamento linguístico e de texto](search-analyzers.md)

+ [Search Documents REST API](/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST) 
