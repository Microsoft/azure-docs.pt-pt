---
title: Indexação em várias línguas para consultas de pesquisa não inglesas
titleSuffix: Azure Cognitive Search
description: A Azure Cognitive Search suporta 56 línguas, aproveitando os analisadores linguísticos da Lucene e da tecnologia de processamento de linguagem natural da Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: a121751e71bffdb76341f6a7dc2a01a22240019b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534473"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Como criar um índice para várias línguas na Pesquisa Cognitiva Azure

Os índices podem incluir campos que contenham conteúdo de várias línguas, por exemplo, criando campos individuais para cordas específicas da linguagem. Para obter os melhores resultados durante a indexação e consulta, atribua um analisador de idiomas que forneça as regras linguísticas adequadas. 

A Azure Cognitive Search oferece uma grande seleção de analisadores de linguagem tanto da Lucene como da Microsoft que podem ser atribuídos a campos individuais usando a propriedade do Analyzer. Também pode especificar um analisador de idiomas no portal, conforme descrito neste artigo.

## <a name="add-analyzers-to-fields"></a>Adicionar analisadores aos campos

Um analisador de idiomas é especificado quando um campo é criado. Adicionar um analisador a uma definição de campo existente requer sobreescrita (e recarregamento) do índice, ou criar um novo campo idêntico ao original, mas com uma atribuição de analisador. Em seguida, pode apagar o campo não-americano à sua conveniência.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e encontre o seu serviço de pesquisa.
1. Clique em **Adicionar índice** na barra de comando no topo do painel de serviço para iniciar um novo índice, ou abrir um índice existente para definir um analisador em novos campos que está a adicionar a um índice existente.
1. Inicie uma definição de campo fornecendo um nome.
1. Escolha o tipo de dados Edm.String. Apenas os campos de cordas são pesjáveis por texto completo.
1. Desa estade o atributo **Searchable** para ativar a propriedade Do Analisador. Um campo deve ser baseado em texto para utilizar um analisador de idiomas.
1. Escolha um dos analisadores disponíveis. 

![Atribuir analisadores de linguagem durante a definição de campo](media/search-language-support/select-analyzer.png "Atribuir analisadores de linguagem durante a definição de campo")

Por padrão, todos os campos pescáveis usam o [analisador Standard Lucene,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é agnóstico linguístico. Para ver a lista completa de analisadores suportados, consulte [os analisadores de linguagem Adicionar a um índice de Pesquisa Cognitiva Azure](index-add-language-analyzers.md).

No portal, os analisadores destinam-se a ser usados como-é. Se necessitar de personalização ou uma configuração específica de filtros e tokenizers, deverá [criar um analisador personalizado](index-add-custom-analyzers.md) em código. O portal não suporta a seleção ou configuração de analisadores personalizados.

## <a name="query-language-specific-fields"></a>Campos específicos da linguagem de consulta

Uma vez selecionado o analisador de idiomas para um campo, este será utilizado com cada pedido de indexação e pesquisa para esse campo. Quando uma consulta é emitida contra vários campos usando diferentes analisadores, a consulta será processada independentemente pelos analisadores atribuídos para cada campo.

Se o idioma do agente que emite uma consulta for conhecido, um pedido de pesquisa pode ser examinado para um campo específico usando o parâmetro de consulta **searchFields.** A seguinte consulta só será emitida contra a descrição em polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2020-06-30`

Pode consultar o seu índice a partir do portal, utilizando o [**Explorador de Pesquisa**](search-explorer.md) para colar numa consulta semelhante à acima mostrada.

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos da linguagem

Por vezes, a linguagem do agente que emite uma consulta não é conhecida, caso em que a consulta pode ser emitida contra todos os campos simultaneamente. Se necessário, a preferência pelos resultados numa determinada língua pode ser definida utilizando [perfis de pontuação](index-add-scoring-profiles.md). No exemplo abaixo, os jogos encontrados na descrição em inglês serão pontuados mais altos em relação aos jogos em polaco e francês:

```http
    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]
```

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2020-06-30`

## <a name="next-steps"></a>Passos seguintes

Se for um desenvolvedor .NET, note que pode configurar analisadores de linguagem usando a [propriedade Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) e a propriedade [Analyzer.](/dotnet/api/microsoft.azure.search.models.analyzer)