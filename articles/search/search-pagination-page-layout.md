---
title: Como trabalhar com os resultados da pesquisa - Azure Search
description: Estruturar e ordenar os resultados da pesquisa, obter uma contagem de documentos e adicionar a navegação no conteúdo para os resultados da pesquisa no Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ef29dafe32c3c5988cd33f59c8436eeef4b45886
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849283"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Como trabalhar com a pesquisa resulta no Azure Search
Este artigo fornece orientações sobre como implementar elementos padrão de uma página de resultados de pesquisa, como contagens de totais, obtenção de documento, ordens de classificação e navegação. Opções relacionadas com a página que contribuem com dados ou informações para os resultados da pesquisa são especificadas através da [pesquisar no documento](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) pedidos enviados para o serviço de pesquisa do Azure. 

Na API do REST, pedidos de incluir um comando GET, caminho e os parâmetros de consulta que informam o serviço, o que está a ser requerido e como a formular a resposta. No SDK do .NET, é a API equivalente [DocumentSearchResult classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult?view=azure-dotnet).

Vários exemplos de código incluem uma interface de front-end da web, que pode ser encontrado aqui: [Aplicação de demonstração de tarefas da cidade de nova York](https://azjobsdemo.azurewebsites.net/) e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Um pedido válido inclui um número de elementos, como um URL do serviço e o caminho, verbo HTTP, `api-version`e assim por diante. Para fins de brevidade, podemos cortados os exemplos para realçar apenas a sintaxe que é relevante para paginação. Para obter mais informações sobre a sintaxe do pedido, consulte [API de REST do serviço do Azure Search](https://docs.microsoft.com/rest/api/searchservice). > 
> 

## <a name="total-hits-and-page-counts"></a>Total de resultados e contagens de página
Que mostra o número total de resultados devolvidos por uma consulta e, em seguida, retornar esses resultados em segmentos mais pequenos, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

Azure Search, vai utilizar o `$count`, `$top`, e `$skip` parâmetros para esses valores de retorno. O exemplo seguinte mostra um pedido de exemplo para cliques total num índice chamado "onlineCatalog" retornado como `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Obtenha documentos em grupos de 15 e também mostram as visitas total, começando na primeira página:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Paginação de resultados requer `$top` e `$skip`, onde `$top` Especifica quantos itens a devolver no lote, e `$skip` Especifica quantos itens para ignorar. No exemplo a seguir, cada página mostra os itens de 15 em seguida, indicado pelos saltos incrementais no `$skip` parâmetro.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Esquema
Numa página de resultados de pesquisa, pode querer mostrar uma imagem em miniatura, um subconjunto dos campos e uma ligação para uma página de produto completo.

 ![][2]

No Azure Search, usaria `$select` e um comando de pesquisa para implementar esta experiência.

Para retornar um subconjunto dos campos para um esquema de lado a lado:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Imagens e arquivos de suporte de dados não são diretamente pesquisáveis e devem ser armazenados em outra plataforma de armazenamento, como o armazenamento de Blobs do Azure, para reduzir os custos. O índice e documentos, defina um campo que armazena o endereço de URL do conteúdo externo. Em seguida, pode utilizar o campo como uma referência da imagem. O URL para a imagem deve ser o documento.

Para obter uma página de descrição de produto para um **onClick** evento, utilize [documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) passar a chave do documento para recuperar. O tipo de dados da chave é `Edm.String`. Neste exemplo, é *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Ordenar por relevância, classificação ou preço
Ordenação orders, muitas vezes, a predefinição para relevância, mas é comum para tornar ordenação alternativa ordens prontamente disponíveis para que os clientes podem rapidamente reshuffle resultados existentes numa ordem de classificação diferente.

 ![][3]

No Azure Search, classificação se baseia a `$orderby` expressão, para todos os campos que são indexados como `"Sortable": true.` um `$orderby` cláusula é uma expressão de OData. Para obter informações sobre a sintaxe, consulte [sintaxe da expressão OData para filtros e por ordem cláusulas](query-odata-filter-orderby-syntax.md).

Relevância é fortemente associada com perfis de classificação. Pode usar o padrão de classificação, que baseia-se na análise de texto e estatísticas para a ordem de classificação de todos os resultados, com pontuações mais altas acontecendo para documentos com mais fortes ou mais correspondências um termo de pesquisa.

Ordens de classificação alternativa estão normalmente associadas **onClick** eventos que uma chamada de retorno para um método que baseia-se a sequência de ordenação. Por exemplo, com este elemento de página:

 ![][4]

Deverá criar um método que aceita a opção de ordenação selecionada como entrada e retorna uma lista ordenada para os critérios associados essa opção.

 ![][5]

> [!NOTE]
> Embora o padrão de classificação seja suficiente para muitos cenários, é recomendável baseando relevância num perfil de classificação personalizado em vez disso. Um perfil de classificação personalizado dá-lhe uma forma de itens de boost que são mais úteis para o seu negócio. Ver [adicionar perfis de classificação](index-add-scoring-profiles.md) para obter mais informações. 
> 
> 

## <a name="faceted-navigation"></a>Navegação por facetas
Navegação de pesquisa é comum numa página de resultados, muitas vezes, localizada no lado ou da parte superior de uma página. No Azure Search, navegação por facetas fornece pesquisa orientada com base nos filtros predefinidos. Ver [navegação por facetas no Azure Search](search-faceted-navigation.md) para obter detalhes.

## <a name="filters-at-the-page-level"></a>Filtros ao nível da página
Se seu design de solução incluído páginas de pesquisa dedicadas para tipos específicos de conteúdo (por exemplo, um aplicativo de varejo online tem departamentos listados na parte superior da página), pode inserir um [expressão de filtro](search-filters.md) juntamente com um **onClick** evento para abrir uma página num estado pré-filtrada. 

Pode enviar um filtro com ou sem uma expressão de pesquisa. Por exemplo, o pedido seguinte irá filtrar no nome de marca, retornando apenas esses documentos que correspondam a ele.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Ver [documentos sobre pesquisa (API de pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter mais informações sobre `$filter` expressões.

## <a name="see-also"></a>Consultar Também
* [API de REST do serviço Azure Search](https://docs.microsoft.com/rest/api/searchservice)
* [Operações de índice](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operações de documento](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Navegação por facetas no Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
