---
title: Indexação multi-idioma para consultas de pesquisa não inglesas
titleSuffix: Azure Cognitive Search
description: A Azure Cognitive Search suporta 56 idiomas, alavancando os analisadores linguísticos da tecnologia Lucene e Natural Language Processing da Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793599"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Como criar um índice para várias línguas na Pesquisa Cognitiva Azure

Os índices podem incluir campos que contenham conteúdo de várias línguas, por exemplo, criando campos individuais para cordas específicas da linguagem. Para obter os melhores resultados durante a indexação e consulta, atribua um analisador de linguagem que forneça as regras linguísticas apropriadas. 

A Azure Cognitive Search oferece uma grande seleção de analisadores de idiomas tanto da Lucene como da Microsoft que podem ser atribuídos a campos individuais usando a propriedade Do Analisador. Também pode especificar um analisador de línguas no portal, como descrito neste artigo.

## <a name="add-analyzers-to-fields"></a>Adicionar analisadores aos campos

Um analisador de linguagem é especificado quando um campo é criado. A adição de um analisador a uma definição de campo existente requer sobreposição (e recarga) do índice, ou criar um novo campo idêntico ao original, mas com uma atribuição de analisador. Em seguida, pode apagar o campo não utilizado à sua conveniência.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e encontre o seu serviço de pesquisa.
1. Clique em **Adicionar índice** na barra de comando na parte superior do painel de serviço para iniciar um novo índice, ou abra um índice existente para definir um analisador em novos campos que está adicionando a um índice existente.
1. Inicie uma definição de campo fornecendo um nome.
1. Escolha o tipo de dados Edm.String. Só os campos de cordas são pesquisáveis por texto.
1. Defino o atributo **Pesquisável** para ativar a propriedade Do Analisador. Um campo deve ser baseado em texto para fazer uso de um analisador de linguagem.
1. Escolha um dos analisadores disponíveis. 

![Atribuir analisadores de linguagem durante a definição de campo](media/search-language-support/select-analyzer.png "Atribuir analisadores de linguagem durante a definição de campo")

Por padrão, todos os campos pesquisáveis utilizam o [analisador Standard Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é linguagem-agnóstico. Para ver a lista completa de analisadores suportados, consulte [Adicionar analisadores de linguagem a um índice](index-add-language-analyzers.md)de Pesquisa Cognitiva Azure .

No portal, os analisadores destinam-se a ser usados como está. Se necessitar de personalização ou de uma configuração específica de filtros e tokenizers, deve [criar um analisador personalizado](index-add-custom-analyzers.md) em código. O portal não suporta selecionar ou configurar analisadores personalizados.

## <a name="query-language-specific-fields"></a>Campos específicos da linguagem de consulta

Uma vez selecionado o analisador de idiomas para um campo, este será utilizado com cada indexação e pedido de pesquisa para esse campo. Quando uma consulta é emitida contra vários campos usando diferentes analisadores, a consulta será processada independentemente pelos analisadores atribuídos para cada campo.

Se a linguagem do agente emitir uma consulta for conhecida, um pedido de pesquisa pode ser consultado para um campo específico utilizando o parâmetro de consulta **searchFields.** A seguinte consulta será emitida apenas contra a descrição em polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Pode consultar o seu índice a partir do portal, utilizando o explorador de [**pesquisa**](search-explorer.md) para colar numa consulta semelhante à que se pode apresentar acima.

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos da linguagem

Por vezes, a linguagem do agente que emite uma consulta não é conhecida, caso em que a consulta pode ser emitida contra todos os campos simultaneamente. Se necessário, a preferência pelos resultados de uma determinada língua pode ser definida utilizando [perfis de pontuação](index-add-scoring-profiles.md). No exemplo abaixo, os fósforos encontrados na descrição em inglês serão pontuados mais em relação aos jogos em polaco e francês:

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

Se for um desenvolvedor .NET, note que pode configurar os analisadores de idiomas utilizando a [Pesquisa Cognitiva Azure .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) e a propriedade [Do Analisador.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 