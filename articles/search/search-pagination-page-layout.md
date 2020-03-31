---
title: Como trabalhar com resultados de pesquisa
titleSuffix: Azure Cognitive Search
description: Estrutura e classifica os resultados da pesquisa, obtenha uma contagem de documentos e adicione navegação de conteúdo aos resultados de pesquisa em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481607"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com os resultados da pesquisa em Pesquisa Cognitiva Azure
Este artigo fornece orientações sobre como implementar elementos padrão de uma página de resultados de pesquisa, tais como contagem total, recuperação de documentos, ordenar ordens e navegação. As opções relacionadas com a página que contribuem com dados ou informações para os resultados da sua pesquisa são especificadas através dos pedidos do Documento de [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) enviados para o seu serviço de Pesquisa Cognitiva Azure. 

Na API REST, os pedidos incluem um comando GET, caminho e parâmetros de consulta que informam o serviço o que está a ser solicitado, e como formular a resposta. No SDK .NET, o Equivalente API é [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Para gerar rapidamente uma página de pesquisa para o seu cliente, explore estas opções:

+ Utilize o gerador de [aplicação](search-create-app-portal.md) no portal para criar uma página HTML com uma barra de pesquisa, navegação faceada e área de resultados.
+ Siga a [sua primeira aplicação em C#](tutorial-csharp-create-first-app.md) tutorial para criar um cliente funcional.

Várias amostras de código incluem uma interface frontal web, que você pode encontrar aqui: [new York City Jobs demo app](https://aka.ms/azjobsdemo), Código de amostra [JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples), e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Um pedido válido inclui uma série de elementos, tais como `api-version`um URL de serviço e caminho, verbo HTTP, e assim por diante. Para a brevidade, aparamos os exemplos para destacar apenas a sintaxe que é relevante para a paginação. Para mais informações sobre a sintaxe de pedido, consulte [AFP](https://docs.microsoft.com/rest/api/searchservice)
>

## <a name="total-hits-and-page-counts"></a>Total de resultados e Contagens de Páginas

Mostrar o número total de resultados devolvidos de uma consulta, e depois devolver esses resultados em pedaços menores, é fundamental para praticamente todas as páginas de pesquisa.

![][1]

Na Pesquisa Cognitiva Azure, `$count` `$top`utiliza-se os , e `$skip` parâmetros para devolver estes valores. O exemplo seguinte mostra um pedido de amostra de acessototal `@odata.count`de acessos num índice chamado "catálogo online", devolvido como:

    GET /indexes/online-catalog/docs?$count=true

Recupere documentos em grupos de 15, e também mostre o total de acessos, a partir da primeira página:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Os resultados paginantes `$top` `$skip`requerem `$top` tanto e, quando especifica quantos itens `$skip` devem devolver num lote, e especifica quantos itens devem saltar. No exemplo seguinte, cada página mostra os próximos 15 itens, `$skip` indicados pelos saltos incrementais no parâmetro.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Esquema

Numa página de resultados de pesquisa, é melhor mostrar uma imagem de miniatura, um subconjunto de campos e um link para uma página completa do produto.

 ![][2]

Na Pesquisa Cognitiva Azure, `$select` utilizaria e um pedido de [Pesquisa API](https://docs.microsoft.com/rest/api/searchservice/search-documents) para implementar esta experiência.

Para devolver um subconjunto de campos para um layout em azulejo:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Imagens e ficheiros de mídia não são diretamente pesquisáveis e devem ser armazenados noutra plataforma de armazenamento, como o armazenamento do Azure Blob, para reduzir custos. No índice e nos documentos, defina um campo que armazene o endereço URL do conteúdo externo. Em seguida, pode utilizar o campo como referência de imagem. O URL da imagem deve estar no documento.

Para recuperar uma página de descrição do produto para um evento **onClick,** utilize o [Documento de Procuração](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) para passar na chave do documento para recuperar. O tipo de dados `Edm.String`da chave é . Neste exemplo, é *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Ordenar por relevância, classificação ou preço

Ordenar encomendas muitas vezes padrão à relevância, mas é comum disponibilizar as encomendas de classificação alternativas prontamente disponíveis para que os clientes possam rapidamente remodelar os resultados existentes numa ordem de classificação diferente.

 ![][3]

Na Pesquisa Cognitiva Azure, a `$orderby` triagem baseia-se na expressão, `"Sortable": true.` `$orderby` para todos os campos indexados como cláusula A é uma expressão OData. Para obter informações sobre sintaxe, consulte a [sintaxe de expressão OData para filtros e cláusulas de encomenda.](query-odata-filter-orderby-syntax.md)

A relevância está fortemente associada a perfis de pontuação. Pode utilizar a pontuação padrão, que se baseia na análise de texto e estatísticas para classificar todos os resultados, com pontuações mais altas a irem para documentos com jogos mais ou mais fortes num termo de pesquisa.

As ordens de classificação alternativas são tipicamente associadas a eventos **onClick** que chamam de volta a um método que constrói a ordem de classificação. Por exemplo, dado este elemento de página:

 ![][4]

Criaria um método que aceita a opção de classificação selecionada como entrada, e devolve uma lista ordenada para os critérios associados a essa opção.

 ![][5]

> [!NOTE]
> Embora a pontuação padrão seja suficiente para muitos cenários, recomendamos basear a relevância num perfil de pontuação personalizado. Um perfil de pontuação personalizado dá-lhe uma forma de impulsionar itens que são mais benéficos para o seu negócio. Consulte adicionar perfis de [pontuação](index-add-scoring-profiles.md) para obter mais informações.
>

## <a name="hit-highlighting"></a>Detetor de ocorrências

Pode aplicar formatação em termos correspondentes nos resultados da pesquisa, facilitando a identificação da partida. As instruções de destaque do hit são fornecidas no pedido de [consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

A formatação é aplicada a consultas de termo inteiros. Consultas em termos parciais, tais como pesquisa difusa ou pesquisa de wildcard que resultam em expansão de consulta no motor, não podem usar o destaque do hit.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> Os serviços criados após 15 de julho de 2020 proporcionarão uma experiência de destaque diferente. Os serviços criados antes dessa data não mudarão no seu comportamento de destaque. Com esta mudança, apenas frases que correspondam à consulta de frase completa serão devolvidas. Além disso, será possível especificar o tamanho do fragmento devolvido para o destaque.
>
> Quando estiver a escrever o código do cliente que implementa o destaque, esteja ciente desta mudança. Note que isso não irá impactá-lo a menos que crie um serviço de pesquisa completamente novo.

## <a name="faceted-navigation"></a>Navegação por facetas

A navegação de pesquisa é comum numa página de resultados, muitas vezes localizada na parte lateral ou no topo de uma página. Na Pesquisa Cognitiva Azure, a navegação facetada proporciona uma pesquisa auto-direcionada com base em filtros predefinidos. Consulte [a navegação facetada na Pesquisa Cognitiva Azure](search-faceted-navigation.md) para obter detalhes.

## <a name="filters-at-the-page-level"></a>Filtros ao nível da página

Se o seu design de solução incluir páginas de pesquisa dedicadas para tipos específicos de conteúdo (por exemplo, uma aplicação de retalho online que tem departamentos listados no topo da página), pode inserir uma expressão de [filtro](search-filters.md) ao lado de um evento **onClick** para abrir uma página em um estado pré-filtrado.

Pode enviar um filtro com ou sem expressão de pesquisa. Por exemplo, o seguinte pedido filtrar-se-á no nome da marca, devolvendo apenas os documentos que o correspondem.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Consulte documentos de [pesquisa (API de pesquisa cognitiva azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter mais informações sobre `$filter` expressões.

## <a name="see-also"></a>Veja também

- [API REST do Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice)
- [Operações indexadas](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operações documentais](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Navegação Faceted em Pesquisa Cognitiva Azure](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
