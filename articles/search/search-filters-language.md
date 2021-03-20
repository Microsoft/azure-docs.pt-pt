---
title: Filtrar por linguagem num índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Filtrar critérios para apoiar a pesquisa em várias línguas, digitalizando a execução da consulta para campos específicos da linguagem.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 49a12203c833fc817b1898e6179d7f812d0a994e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002526"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Como filtrar por linguagem na Pesquisa Cognitiva do Azure 

Um requisito chave numa aplicação de pesquisa multilíngue é a capacidade de pesquisar e recuperar resultados no próprio idioma do utilizador. Na Pesquisa Cognitiva Azure, uma maneira de satisfazer os requisitos linguísticos de uma aplicação multilíngue é criar uma série de campos dedicados a armazenar cordas numa língua específica, e, em seguida, limitar a pesquisa completa de texto a esses campos apenas no momento da consulta.

Os parâmetros de consulta sobre o pedido são usados para tanto o âmbito da operação de pesquisa, como, em seguida, aparar os resultados de quaisquer campos que não forneçam conteúdo compatível com a experiência de pesquisa que pretende fornecer.

| Parâmetros | Objetivo |
|-----------|--------------|
| **searchFields** | Limite a pesquisa por texto na lista de campos nomeados. |
| **$select** | Apara a resposta para incluir apenas os campos especificados. Por predefinição, todos os campos recuperáveis são devolvidos. O **parâmetro $select** permite-lhe escolher quais os que deveresar. |

O sucesso desta técnica depende da integridade dos conteúdos de campo. A Azure Cognitive Search não traduz cordas nem executa a deteção de linguagem. Cabe-lhe a si certificar-se de que os campos contêm as cordas que se esperam.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para conteúdo em diferentes línguas

Na Pesquisa Cognitiva Azure, as consultas têm como alvo um único índice. Os desenvolvedores que querem fornecer cordas específicas da linguagem numa única experiência de pesquisa normalmente definem campos dedicados para armazenar os valores: um campo para cordas inglesas, um para francês, e assim por diante. 

O exemplo a seguir é da [amostra imobiliária](search-get-started-portal.md) que tem vários campos de cordas contendo conteúdo em diferentes línguas. Note as atribuições de analisadores de idioma para os campos neste índice. Os campos que contêm cordas funcionam melhor na pesquisa completa de texto quando emparelhado com um analisador projetado para lidar com as regras linguísticas da linguagem-alvo.

  ![Screenshot mostrando o ecrã fields da amostra imobiliária. Um grupo de campos é destacado para salientar como as atribuições de analisadores de idioma correspondem às línguas para os campos destacados.](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para exemplos de código que mostrem definições de campo com analisadores de línguas, consulte [definir um índice (.NET)](./search-get-started-dotnet.md) e [definir um índice (REST)](./search-get-started-powershell.md).

## <a name="build-and-load-an-index"></a>Construa e carregue um índice

Um passo intermédio (e talvez óbvio) é que é preciso [construir e preencher o índice](./search-get-started-dotnet.md) antes de formular uma consulta. Mencionamos este passo aqui para a completude. Uma forma de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Conter os resultados da consulta e do corte

Os parâmetros da consulta são usados para limitar a pesquisa a campos específicos e, em seguida, aparar os resultados de quaisquer campos que não sejam úteis ao seu cenário. Tendo em conta o objetivo de restringir a procura a campos que contenham cordas francesas, você usaria **searchFields** para direcionar a consulta em campos que contêm cordas nessa língua. 

Por predefinição, uma pesquisa devolve todos os campos que são marcados como recuperáveis. Como tal, pode querer excluir campos que não estejam em conformidade com a experiência de pesquisa específica da linguagem que pretende proporcionar. Especificamente, se você limitou a pesquisa a um campo com cordas francesas, provavelmente quer excluir campos com cordas inglesas dos seus resultados. A utilização do parâmetro de consulta **$select** dá-lhe controlo sobre quais os campos que são devolvidos à aplicação de chamada.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Embora não exista $filter argumento sobre a consulta, este caso de uso está fortemente associado a conceitos de filtro, por isso é apresentado como um cenário de filtragem.

## <a name="see-also"></a>Ver também

+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Analisadores de idiomas](/rest/api/searchservice/language-support)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)