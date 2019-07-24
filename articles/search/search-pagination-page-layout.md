---
title: Como trabalhar com os resultados da pesquisa-Azure Search
description: Estruturar e classificar resultados da pesquisa, obter uma contagem de documentos e adicionar navegação de conteúdo aos resultados da pesquisa em Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb86a75be464cb78a16170626bc96778d43bb8b6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974612"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Como trabalhar com resultados de pesquisa em Azure Search
Este artigo fornece orientação sobre como implementar elementos padrão de uma página de resultados da pesquisa, como contagens totais, recuperação de documentos, ordens de classificação e navegação. As opções relacionadas à página que contribuem com dados ou informações para os resultados da pesquisa são especificadas por meio das solicitações de [documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) enviadas ao seu serviço de Azure Search. 

Na API REST, as solicitações incluem um comando GET, um caminho e parâmetros de consulta que informam ao serviço o que está sendo solicitado e como formular a resposta. No SDK do .NET, a API equivalente é a [classe DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Vários exemplos de código incluem uma interface de front-end da Web, que você pode encontrar aqui: [Aplicativo de demonstração de trabalhos da cidade de Nova York](https://azjobsdemo.azurewebsites.net/) e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Uma solicitação válida inclui vários elementos, como uma URL de serviço e um caminho, um verbo http, `api-version`e assim por diante. Para resumir, nós filtramos os exemplos para realçar apenas a sintaxe que é relevante para a paginação. Para obter mais informações sobre a sintaxe de solicitação, consulte [Azure Search serviço REST](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Total de ocorrências e contagens de página

Mostrar o número total de resultados retornados de uma consulta e, em seguida, retornar esses resultados em partes menores, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

No Azure Search, você usa os `$count`parâmetros `$top`, e `$skip` para retornar esses valores. O exemplo a seguir mostra uma solicitação de exemplo para total de ocorrências em um índice chamado "online-Catalog `@odata.count`", retornado como:

    GET /indexes/online-catalog/docs?$count=true

Recupere documentos em grupos de 15 e também mostre o total de acertos, começando na primeira página:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Paginar resultados requer `$top` e `$skip`, onde `$top` especifica quantos itens retornar em um lote e `$skip` especifica quantos itens ignorar. No exemplo a seguir, cada página mostra os próximos 15 itens, indicados pelos saltos incrementais no `$skip` parâmetro.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Esquema

Em uma página de resultados da pesquisa, talvez você queira mostrar uma imagem em miniatura, um subconjunto de campos e um link para uma página completa do produto.

 ![][2]

No Azure Search, você usaria `$select` o e uma [solicitação de API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) para implementar essa experiência.

Para retornar um subconjunto de campos para um layout ao lado do xadrez:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Imagens e arquivos de mídia não são pesquisáveis diretamente e devem ser armazenados em outra plataforma de armazenamento, como o armazenamento de BLOBs do Azure, para reduzir os custos. No índice e nos documentos, defina um campo que armazene o endereço da URL do conteúdo externo. Em seguida, você pode usar o campo como uma referência de imagem. A URL para a imagem deve estar no documento.

Para recuperar uma página de descrição do produto  para um evento onclick, use o [documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) para passar a chave do documento a ser recuperado. O tipo de dados da chave é `Edm.String`. Neste exemplo, é *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Classificar por relevância, classificação ou preço

As ordens de classificação geralmente assumem como padrão a relevância, mas é comum tornar as ordens de classificação alternativas disponíveis imediatamente para que os clientes possam reembaralhar os resultados existentes rapidamente em uma ordem de classificação diferente.

 ![][3]

No Azure Search, a classificação é baseada na `$orderby` expressão, para todos os campos que são indexados `$orderby` como `"Sortable": true.` uma cláusula é uma expressão OData. Para obter informações sobre a sintaxe, consulte [sintaxe de expressão OData para filtros e cláusulas](query-odata-filter-orderby-syntax.md)de ordenação.

A relevância está fortemente associada aos perfis de pontuação. Você pode usar a pontuação padrão, que se baseia em análise de texto e estatísticas para ordenar todos os resultados, com pontuações mais altas, passando para documentos com correspondências mais ou mais fortes em um termo de pesquisa.

Ordens de classificação alternativas geralmente são associadas  a eventos onclick que retornam a um método que cria a ordem de classificação. Por exemplo, dado este elemento de página:

 ![][4]

Você criaria um método que aceita a opção de classificação selecionada como entrada e retorna uma lista ordenada para os critérios associados a essa opção.

 ![][5]

> [!NOTE]
> Embora a pontuação padrão seja suficiente para muitos cenários, é recomendável basear a relevância em um perfil de Pontuação personalizado em vez disso. Um perfil de Pontuação personalizado oferece uma maneira de impulsionar itens que são mais benéficos para seus negócios. Consulte [adicionar perfis de Pontuação](index-add-scoring-profiles.md) para obter mais informações.
>

## <a name="faceted-navigation"></a>Navegação por facetas

A navegação de pesquisa é comum em uma página de resultados, geralmente localizada no lado ou na parte superior de uma página. No Azure Search, a navegação facetada fornece pesquisa autodirigida com base em filtros predefinidos. Consulte [navegação facetada no Azure Search](search-faceted-navigation.md) para obter detalhes.

## <a name="filters-at-the-page-level"></a>Filtros no nível da página

Se o design da solução incluísse páginas de pesquisa dedicadas para tipos específicos de conteúdo (por exemplo, um aplicativo de varejo online que tenha departamentos listados na parte superior da página), você poderá inserir uma [expressão de filtro](search-filters.md) junto com um evento OnClick para  Abra uma página em um Estado previamente filtrado.

Você pode enviar um filtro com ou sem uma expressão de pesquisa. Por exemplo, a solicitação a seguir filtrará o nome da marca, retornando somente os documentos que correspondem a ele.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Consulte [Pesquisar documentos (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter mais informações `$filter` sobre expressões.

## <a name="see-also"></a>Consultar Também

- [API REST do serviço de Azure Search](https://docs.microsoft.com/rest/api/searchservice)
- [Operações de índice](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operações de documento](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Navegação facetada no Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
