---
title: Filtros de idioma para conteúdo multilíngue em um índice de pesquisa-Azure Search
description: Critérios de filtro para dar suporte à pesquisa de vários idiomas, escopo da execução da consulta em campos específicos do idioma.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1eced868b180a916355d6f9fbfc8cd47a5d7d6e2
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649872"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Como filtrar por idioma no Azure Search 

Um requisito importante em um aplicativo de pesquisa multilíngüe é a capacidade de Pesquisar e recuperar resultados no próprio idioma do usuário. No Azure Search, uma maneira de atender aos requisitos de idioma de um aplicativo multilíngue é criar uma série de campos dedicados ao armazenamento de cadeias de caracteres em um idioma específico e, em seguida, restringir a pesquisa de texto completo apenas a esses campos no momento da consulta.

Os parâmetros de consulta na solicitação são usados para fazer o escopo da operação de pesquisa e, em seguida, aparar os resultados de todos os campos que não fornecem conteúdo compatível com a experiência de pesquisa que você deseja entregar.

| Parâmetros | Objetivo |
|-----------|--------------|
| **searchFields** | Limita a pesquisa de texto completo à lista de campos nomeados. |
| **$select** | Corta a resposta para incluir apenas os campos que você especificar. Por padrão, todos os campos recuperáveis são retornados. O parâmetro **$Select** permite que você escolha quais serão retornados. |

O sucesso dessa técnica depende da integridade do conteúdo do campo. Azure Search não converte cadeias de caracteres ou executa detecção de idioma. Cabe a você certificar-se de que os campos contêm as cadeias de caracteres esperadas.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para conteúdo em idiomas diferentes

No Azure Search, as consultas visam um único índice. Os desenvolvedores que desejam fornecer cadeias de caracteres específicas a um idioma em uma única experiência de pesquisa normalmente definem campos dedicados para armazenar os valores: um campo para cadeias de caracteres em inglês, um para francês e assim por diante. 

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

## <a name="see-also"></a>Consulte também

+ [Filtros no Azure Search](search-filters.md)
+ [Analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [Pesquisar API REST de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)

