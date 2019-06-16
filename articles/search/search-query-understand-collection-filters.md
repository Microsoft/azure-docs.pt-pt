---
title: Noções básicas sobre filtros de recolha de OData - Azure Search
description: Compreender como funcionam os filtros de recolha de OData em consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 7af1b0ab95d04249d6d74e3324dbeb30eda6e1de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079603"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Noções básicas sobre filtros de recolha de OData no Azure Search

Para [filtro](query-odata-filter-orderby-syntax.md) em campos de coleção do Azure Search, pode utilizar os [ `any` e `all` operadores](search-query-odata-collection-operators.md) em conjunto com **expressões lambda**. As expressões lambda são expressões booleanas que se referem a um **variável de alcance**. O `any` e `all` operadores são análogos a um `for` loop na maioria das linguagens de programação, com a variável de alcance, levando a função de variável de loop e a expressão lambda como o corpo do loop. A variável de alcance assume o valor "current" da coleção durante a iteração do loop.

Pelo menos essa é a forma como ele funciona conceitualmente. Na realidade, o Azure Search implementa filtros de forma muito diferente, como `for` faz um loop de trabalho. O ideal é que essa diferença deve ser invisível para, mas em determinadas situações não é. O resultado final é que existem regras que tiver a seguir ao escrever expressões lambda.

Este artigo explica por que as regras para filtros de recolha existem ao explorar como o Azure Search executa esses filtros. Se estiver escrevendo filtros avançados com expressões lambda complexa, poderá considerar este artigo úteis na criação de sua compreensão do que é possível em filtros e por que.

Para obter informações sobre o que as regras para filtros de recolha são, incluindo exemplos, consulte [filtros de recolha de OData de resolução de problemas no Azure Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Por que motivo estão limitados filtros de recolha

Existem três motivos subjacentes por que não a todas as funcionalidades de filtro são suportadas para todos os tipos de coleções:

1. Apenas determinados operadores são suportadas para determinados tipos de dados. Por exemplo, não faz sentido para comparar os valores booleanos `true` e `false` usando `lt`, `gt`e assim por diante.
1. O Azure Search não suporta **pesquisa correlacionada** em campos do tipo `Collection(Edm.ComplexType)`.
1. O Azure utiliza pesquisa invertido índices para executar filtros através de todos os tipos de dados, incluindo coleções.

O primeiro motivo é apenas uma conseqüência de como o idioma de OData e o sistema de tipo EDM são definidas. As duas últimas são explicadas em mais detalhes no resto deste artigo.

## <a name="correlated-versus-uncorrelated-search"></a>Correlacionado versus uncorrelated pesquisa

Ao aplicar vários critérios de filtro através de uma coleção de objetos complexos, os critérios são **correlacionado** uma vez que eles se aplicam ao *cada objeto da coleção*. Por exemplo, o filtro seguinte irá devolver hotéis que tenham, pelo menos, uma sala deluxe com uma taxa de menor que 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se a filtragem foi *uncorrelated*, o filtro acima pode devolver hotéis em que uma sala é deluxe e uma sala de diferente tem uma base de classificar inferior a 100. Isso não faz sentido, uma vez que ambas as cláusulas da expressão lambda aplicam-se a mesma variável de alcance, ou seja `room`. É por isso esses filtros são correlacionados.

No entanto, para pesquisa em texto completo, não é possível fazer referência a uma variável de intervalo específico. Se utilizar a pesquisa respondida para emitir uma [consultas de Lucene completas](query-lucene-syntax.md) como este:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

poderá receber hotéis, novamente, em que uma sala é deluxe, e uma sala de diferente menciona "visão de cidade" na descrição. Por exemplo, o documento abaixo com `Id` de `1` corresponderia a consulta:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

O motivo é que `Rooms/Type` refere-se a todas as condições de analisado, o `Rooms/Type` campo em todo o documento e, da mesma forma para `Rooms/Description`, conforme mostrado nas tabelas abaixo.

Como `Rooms/Type` é armazenado para pesquisa em texto completo:

| Termo na `Rooms/Type` | IDs de documento |
| --- | --- |
| Deluxe | 1, 2 |
| standard | 1 |

Como `Rooms/Description` é armazenado para pesquisa em texto completo:

| Termo na `Rooms/Description` | IDs de documento |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| Garden | 1 |
| Grandes | 1 |
| motel | 2 |
| Espaço | 1, 2 |
| standard | 1 |
| Suite | 1 |
| ver | 1 |

Portanto, ao contrário de filtro acima, que basicamente diz "corresponda nos documentos em que tem uma sala `Type` igual a 'Sala Deluxe' e **essa mesma sala** tem `BaseRate` menos de 100", diz a consulta de pesquisa "correspondência documenta onde `Rooms/Type`tem o termo "deluxe" e `Rooms/Description` tem a frase "vista de cidade". Não há nenhum conceito de salas individuais cujos campos podem ser correlacionados no último caso.

> [!NOTE]
> Se gostaria de ver suporte para pesquisa correlacionada adicionada ao Azure Search, vote [este item de Uservoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Índices invertidos e coleções

Deve ter observado que há muito menos restrições sobre as expressões lambda sobre coleções complexas do que para coleções simples como `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`e assim por diante. Isto acontece porque o Azure Search armazena coleções complexas como reais coleções de documentos secundárias, embora coleções simples não são armazenadas como coleções em todos os.

Por exemplo, considere um campo de coleção de cadeia de caracteres filtrável como `seasons` num índice para um varejista online. Alguns documentos que carregou para este índice podem ser assim:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Os valores do `seasons` campo são armazenados numa estrutura chamada uma **invertido índice**, que é parecido com este:

| Termo | IDs de documento |
| --- | --- |
| Spring | 1, 2 |
| Verão | 1 |
| se enquadram | 1, 2 |
| Inverno | 2, 3 |

Essa estrutura de dados foi concebida para responder a uma pergunta com grande velocidade: Em quais documentos é um determinado termo apresentada? Resposta a essa pergunta funciona mais como uma verificação de igualdade simples que um loop através de uma coleção. Na verdade, isso é por isso que, para coleções de cadeia de caracteres, pesquisa do Azure só permite `eq` como um operador de comparação dentro de uma expressão lambda para `any`.

Criar cópia de segurança de igualdade, em seguida, veremos como é possível combinar várias verificações de igualdade sobre a mesma variável de alcance com `or`. Ele funciona, graças ao álgebra e [a propriedade distributive de quantificadores](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Esta expressão:

    seasons/any(s: s eq 'winter' or s eq 'fall')

é equivalente a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e cada um dos dois `any` as subexpressões podem ser executadas de forma eficiente a usando o índice de invertida. Além disso, graças à [a lei de negação de quantificadores](https://en.wikipedia.org/wiki/Existential_quantification#Negation), esta expressão:

    seasons/all(s: s ne 'winter' and s ne 'fall')

é equivalente a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

por isso, é possível usar `all` com `ne` e `and`.

> [!NOTE]
> Embora os detalhes estão além do escopo deste documento, esses mesmos princípios se expandir para o [testes de distância e interseção para coleções de pontos geoespaciais](search-query-odata-geo-spatial-functions.md) também. É por isso que, no `any`:
>
> - `geo.intersects` Não é possível negada
> - `geo.distance` tem de ser comparadas com `lt` ou `le`
> - expressões devem ser combinadas com `or`, e não `and`
>
> As regras de contrário são aplicadas para `all`.

Uma variedade mais ampla de expressões são permitidas quando tipos de filtragem em coleções de dados que suportam o `lt`, `gt`, `le`, e `ge` operadores, tais como `Collection(Edm.Int32)` por exemplo. Especificamente, pode usar `and` , bem como `or` na `any`, enquanto as expressões de comparação subjacentes são combinadas num **comparações de intervalo** usando `and`, que, em seguida, são mais combinar utilizando `or`. Esta estrutura de expressões booleanas é chamada [Disjunctive Normal formulário (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), também conhecida como "ORs de ANDs". Por outro lado, as expressões lambda para `all` esses dados de tipos tem de constar [Conjunctive Normal formulário (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), também conhecida como "ANDs de ORs". O Azure Search permite que essas comparações de intervalo porque ele pode executá-los usando invertido índices de forma eficiente, tal como ele pode fazer pesquisa rápida de termo para cadeias de caracteres.

Em resumo, aqui estão as regras básicas para o que é permitido numa expressão lambda:

- Dentro `any`, *verificações positivas* são sempre permitidas, como igualdade, comparações de intervalo, `geo.intersects`, ou `geo.distance` em comparação com `lt` ou `le` (Pense em "limite de proximidade" como se fosse igualdade quando se trata de verificação de distância).
- Dentro `any`, `or` é sempre permitido. Pode usar `and` apenas para tipos de dados que podem expressar verificações de intervalos e apenas se usar ORs de ANDs (DNF).
- Dentro `all`, as regras são invertidas – apenas *negativos verificações* são permitidos, pode usar `and` sempre, e pode usar `or` apenas para o intervalo verifica expresso como ANDs ORs (CNF).

Na prática, estes são os tipos de filtros que mais provavelmente utilizar qualquer forma. É útil para entender os limites do que é possível no entanto.

Para obter exemplos específicos de quais tipos de filtros são permitidos e que não são, consulte [como escrever filtros de recolha válido](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passos Seguintes  

- [Resolução de problemas de filtros de recolha de OData no Azure Search](search-query-troubleshoot-collection-filters.md)
- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
