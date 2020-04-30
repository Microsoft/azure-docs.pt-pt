---
title: Filtrar por linguagem num índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Filtrar critérios para apoiar a pesquisa multi-idioma, pesquisa de consultas para campos específicos da linguagem.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: b0ebbbb64e173e1501f08f8385b14c365759a804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116286"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Como filtrar por linguagem na Pesquisa Cognitiva Azure 

Um requisito chave numa aplicação de pesquisa multilingual é a capacidade de pesquisar e recuperar resultados no próprio idioma do utilizador. Na Pesquisa Cognitiva Azure, uma maneira de satisfazer os requisitos linguísticos de uma aplicação multilingual é criar uma série de campos dedicados a armazenar cordas numa linguagem específica, e depois limitar a pesquisa completa de texto apenas esses campos em tempo de consulta.

Os parâmetros de consulta no pedido são usados para examinar a operação de pesquisa e, em seguida, aparar os resultados de quaisquer campos que não forneçam conteúdo compatível com a experiência de pesquisa que pretende entregar.

| Parâmetros | Objetivo |
|-----------|--------------|
| **searchFields** | Limita a pesquisa completa de texto à lista de campos nomeados. |
| **$select** | Apara a resposta para incluir apenas os campos que especifica. Por defeito, todos os campos recuperáveis são devolvidos. O parâmetro **$select** permite escolher quais devolver. |

O sucesso desta técnica depende da integridade dos conteúdos de campo. A Pesquisa Cognitiva Azure não traduz cordas nem executa a deteção de linguagem. Cabe-lhe a si certificar-se de que os campos contêm as cordas que espera.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para conteúdos em diferentes idiomas

Na Pesquisa Cognitiva Azure, as consultas visam um único índice. Os desenvolvedores que querem fornecer cordas específicas da linguagem numa única experiência de pesquisa normalmente definem campos dedicados para armazenar os valores: um campo para cordas inglesas, um para francês, e assim por diante. 

O exemplo que se segue é da [amostra imobiliária](search-get-started-portal.md) que tem vários campos de cordas contendo conteúdo em diferentes línguas. Note as atribuições de analisadores linguísticos para os campos neste índice. Os campos que contêm cordas funcionam melhor na pesquisa de texto completo quando emparelhados com um analisador projetado para lidar com as regras linguísticas da linguagem-alvo.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para exemplos de código que mostrem definições de campo com analisadores de línguas, consulte [Definir um índice (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) e [definir um índice (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Construir e carregar um índice

Um passo intermédio (e talvez óbvio) é que você tem que [construir e povoar o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet) antes de formular uma consulta. Mencionamos este passo aqui para a completude. Uma forma de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Restrinja os resultados da consulta e do corte

Os parâmetros na consulta são usados para limitar a pesquisa a campos específicos e, em seguida, aparar os resultados de quaisquer campos não úteis para o seu cenário. Dado o objetivo de restringir a procura em campos que contenham cordas francesas, usaria **saques** para direcionar a consulta em campos que contenham cordas nessa língua. 

Por padrão, uma pesquisa devolve todos os campos que são marcados como recuperáveis. Como tal, pode querer excluir campos que não estejam em conformidade com a experiência de pesquisa específica da linguagem que pretende proporcionar. Especificamente, se limitou a pesquisa a um campo com cordas francesas, provavelmente quer excluir campos com cordas inglesas dos seus resultados. A utilização do parâmetro de consulta **$select** dá-lhe controlo sobre quais os campos devolvidos à aplicação de chamada.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Embora não haja argumentos $filter sobre a consulta, este caso de uso está fortemente afiliado a conceitos de filtro, por isso é apresentado como um cenário de filtragem.

## <a name="see-also"></a>Consulte também

+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST)

