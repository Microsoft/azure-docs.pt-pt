---
title: Filtros de idioma para conteúdo multilíngue em um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Critérios de filtro para dar suporte à pesquisa em vários idiomas, escopo da execução da consulta em campos específicos do idioma.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2762ce42f0d3f5829682e0910c452746a65ef2f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792876"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Como filtrar por idioma no Azure Pesquisa Cognitiva 

Um requisito importante em um aplicativo de pesquisa multilíngüe é a capacidade de Pesquisar e recuperar resultados no próprio idioma do usuário. No Azure Pesquisa Cognitiva, uma maneira de atender aos requisitos de idioma de um aplicativo multilíngue é criar uma série de campos dedicados ao armazenamento de cadeias de caracteres em um idioma específico e, em seguida, restringir a pesquisa de texto completo apenas a esses campos no momento da consulta.

Os parâmetros de consulta na solicitação são usados para fazer o escopo da operação de pesquisa e, em seguida, aparar os resultados de todos os campos que não fornecem conteúdo compatível com a experiência de pesquisa que você deseja entregar.

| Parâmetros | Finalidade |
|-----------|--------------|
| **searchFields** | Limita a pesquisa de texto completo à lista de campos nomeados. |
| **$select** | Corta a resposta para incluir apenas os campos que você especificar. Por padrão, todos os campos recuperáveis são retornados. O parâmetro **$Select** permite que você escolha quais serão retornados. |

O sucesso dessa técnica depende da integridade do conteúdo do campo. O Azure Pesquisa Cognitiva não converte cadeias de caracteres nem executa a detecção de idioma. Cabe a você certificar-se de que os campos contêm as cadeias de caracteres esperadas.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para conteúdo em idiomas diferentes

No Azure Pesquisa Cognitiva, as consultas visam um único índice. Os desenvolvedores que desejam fornecer cadeias de caracteres específicas a um idioma em uma única experiência de pesquisa normalmente definem campos dedicados para armazenar os valores: um campo para cadeias de caracteres em inglês, um para francês e assim por diante. 

Em nossos exemplos, incluindo o [exemplo de espaço real](search-get-started-portal.md) mostrado abaixo, você pode ter visto definições de campo semelhantes à captura de tela a seguir. Observe como este exemplo mostra as atribuições do analisador de idioma para os campos neste índice. Os campos que contêm cadeias de caracteres têm melhor desempenho na pesquisa de texto completo quando emparelhados com um analisador projetado para lidar com as regras linguísticas do idioma de destino.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para obter exemplos de código mostrando definições de campo com analisadores de idiomas, consulte [definir um índice (.net)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) e [definir um índice (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet) antes de formular uma consulta. Mencionamos esta etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Restringir a consulta e cortar os resultados

Os parâmetros na consulta são usados para limitar a pesquisa a campos específicos e, em seguida, cortar os resultados de quaisquer campos que não sejam úteis para o seu cenário. Devido a uma meta de restringir a pesquisa a campos que contenham cadeias de caracteres francesas, você usaria **searchFields** para direcionar a consulta em campos que contêm cadeias de caracteres nesse idioma. 

Por padrão, uma pesquisa retorna todos os campos marcados como recuperáveis. Como tal, talvez você queira excluir campos que não estão em conformidade com a experiência de pesquisa específica a um idioma que você deseja fornecer. Especificamente, se você limitou a pesquisa a um campo com cadeias de caracteres francesas, provavelmente desejará excluir campos com cadeias de caracteres em inglês de seus resultados. Usar o parâmetro de consulta **$Select** fornece controle sobre quais campos são retornados para o aplicativo de chamada.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Embora não haja nenhum $filter argumento na consulta, esse caso de uso é altamente afiliado com conceitos de filtro e, portanto, o apresentamos como um cenário de filtragem.

## <a name="see-also"></a>Ver também

+ [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
+ [Analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Como funciona a pesquisa de texto completo no Azure Pesquisa Cognitiva](search-lucene-query-architecture.md)
+ [Pesquisar API REST de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)

