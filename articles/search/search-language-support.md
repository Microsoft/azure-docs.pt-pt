---
title: Indexação de vários idiomas para consultas de pesquisa que não estão em inglês
titleSuffix: Azure Cognitive Search
description: O Azure Pesquisa Cognitiva dá suporte a 56 idiomas, aproveitando os analisadores de idioma da tecnologia de processamento de idioma natural e Lucene da Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793599"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Como criar um índice para vários idiomas no Azure Pesquisa Cognitiva

Os índices podem incluir campos que contenham conteúdo de vários idiomas, por exemplo, criando campos individuais para cadeias de caracteres específicas do idioma. Para obter melhores resultados durante a indexação e a consulta, atribua um analisador de linguagem que fornece as regras linguísticas apropriadas. 

O Azure Pesquisa Cognitiva oferece uma grande seleção de analisadores de linguagem tanto do Lucene quanto da Microsoft que podem ser atribuídos a campos individuais usando a propriedade do analisador. Você também pode especificar um analisador de idioma no portal, conforme descrito neste artigo.

## <a name="add-analyzers-to-fields"></a>Adicionar analisadores a campos

Um analisador de idioma é especificado quando um campo é criado. A adição de um analisador a uma definição de campo existente requer a substituição (e o recarregamento) do índice ou a criação de um novo campo idêntico ao original, mas com uma atribuição de analisador. Em seguida, você pode excluir o campo não utilizado de sua conveniência.

1. Entre no [portal do Azure](https://portal.azure.com) e localize o serviço de pesquisa.
1. Clique em **Adicionar índice** na barra de comandos na parte superior do painel de serviço para iniciar um novo índice ou abra um índice existente para definir um analisador sobre novos campos que você está adicionando a um índice existente.
1. Inicie uma definição de campo fornecendo um nome.
1. Escolha o tipo de dados EDM. String. Somente os campos de cadeia de caracteres são pesquisáveis com texto completo.
1. Defina o atributo **pesquisável** para habilitar a propriedade do analisador. Um campo deve ser baseado em texto para fazer uso de um analisador de idioma.
1. Escolha um dos analisadores disponíveis. 

![Atribuir analisadores de idioma durante a definição do campo](media/search-language-support/select-analyzer.png "Atribuir analisadores de idioma durante a definição do campo")

Por padrão, todos os campos pesquisáveis usam o [analisador Lucene padrão](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , que é independente de linguagem. Para exibir a lista completa de analisadores com suporte, consulte [Adicionar analisadores de idioma a um índice de pesquisa cognitiva do Azure](index-add-language-analyzers.md).

No portal, os analisadores se destinam a serem usados no estado em que se encontram. Se você precisar de personalização ou de uma configuração específica de filtros e criadores, deverá [criar um analisador personalizado](index-add-custom-analyzers.md) no código. O portal não dá suporte à seleção ou configuração de analisadores personalizados.

## <a name="query-language-specific-fields"></a>Campos específicos da linguagem de consulta

Depois que o analisador de idioma for selecionado para um campo, ele será usado com cada solicitação de indexação e pesquisa para esse campo. Quando uma consulta é emitida em vários campos usando analisadores diferentes, a consulta será processada independentemente pelos analisadores atribuídos para cada campo.

Se o idioma do agente que emite uma consulta for conhecido, uma solicitação de pesquisa poderá ser delimitada para um campo específico usando o parâmetro de consulta **searchFields** . A consulta a seguir será emitida somente em relação à descrição em polonês:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Você pode consultar o índice no portal, usando o [**Search Explorer**](search-explorer.md) para colar uma consulta semelhante à mostrada acima.

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos do idioma

Às vezes, o idioma do agente que emite uma consulta não é conhecido; nesse caso, a consulta pode ser emitida em todos os campos simultaneamente. Se necessário, a preferência por resultados em um determinado idioma pode ser definida usando [perfis de Pontuação](index-add-scoring-profiles.md). No exemplo a seguir, as correspondências encontradas na descrição em inglês serão pontuadas em relação às correspondências em polonês e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Passos seguintes

Se você for um desenvolvedor do .NET, observe que é possível configurar os analisadores de idioma usando o [SDK do .net pesquisa cognitiva do Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) e a propriedade do [analisador](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) . 