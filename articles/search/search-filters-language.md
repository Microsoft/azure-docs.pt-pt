---
title: Filtros de idioma para o conteúdo em vários idiomas num índice de pesquisa - Azure Search
description: Critérios para suportar a pesquisa de linguagem de transmissões, execução de consultas de âmbito para campos de idioma específico de filtro.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 695fdfba1573ff97b05f8e8b50a05bef9dbf48de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61289628"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Como filtrar pelo idioma no Azure Search 

Um requisito fundamental num aplicativo de pesquisa multilíngüe é a capacidade de procurar em e obter resultados no idioma do utilizador. No Azure Search, uma forma para cumprir os requisitos de idioma de um aplicativo multilíngüe é criar uma série de campos dedicados ao armazenamento de cadeias de caracteres num idioma específico e, em seguida, restringir a pesquisa em texto completo para apenas os campos no momento da consulta.

Parâmetros de consulta no pedido são utilizados para definir o âmbito da operação de pesquisa e, em seguida, corte os resultados de quaisquer campos que não fornecem conteúdo compatível com a experiência de pesquisa que deseja fornecer.

| Parâmetros | Objetivo |
|-----------|--------------|
| **searchFields** | Limites completas de pesquisa de texto à lista de campos nomeados. |
| **$select** | Corta a resposta para incluir apenas os campos que especificar. Por predefinição, são devolvidos todos os campos recuperáveis. O **$select** parâmetro permite-lhe escolher qual para retornar. |

O sucesso dessa técnica depende da integridade do conteúdo do campo. O Azure Search não traduzir as cadeias de caracteres ou executar a deteção de idioma. Cabe-lhe certificar-se de que os campos contêm as cadeias de caracteres esperado.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para o conteúdo em diferentes idiomas

No Azure Search, as consultas visam um único índice. Os desenvolvedores que desejam fornecer cadeias de caracteres específicas de idiomas numa experiência de pesquisa único normalmente definem campos dedicados para armazenar os valores: um campo para inglês cadeias de caracteres, um para o francês e assim por diante. 

Em nossos exemplos, incluindo o [exemplo de imóveis](search-get-started-portal.md) mostrado abaixo, pode ver as definições de campo semelhantes à seguinte captura de ecrã. Observe como este exemplo mostra o idioma atribuições de analisador para os campos neste índice. Campos que contenham as cadeias de caracteres têm um desempenho melhor na pesquisa em texto completo quando combinado com um analisador projetado para lidar com as regras de idioma de destino.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Para obter exemplos de código que mostra as definições de campo com analisadores de idiomas, consulte [definir um índice (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) e [definir um índice (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Um passo intermédio (e talvez óbvio) é que precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet) antes de formular uma consulta. Mencionamos este passo para ser completo. Uma forma de determinar se o índice está disponível é ao verificar a lista de índices [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>Restringir a consulta e limitar os resultados

Parâmetros da consulta são usados para limitar a pesquisa a campos específicos e, em seguida, corte os resultados de quaisquer campos não é útil para o seu cenário. Devido um objetivo de pesquisa omezující aos campos que contenham as cadeias de caracteres francês, usaria **searchFields** para direcionar a consulta em campos que contenham as cadeias de caracteres nesse idioma. 

Por predefinição, uma pesquisa devolve todos os campos que estão marcados como campo recuperável. Como tal, pode querer excluir campos que não estão em conformidade com a experiência de pesquisa de idioma específico que pretende fornecer. Especificamente, se limitado pesquisa a um campo com francês cadeias de caracteres, provavelmente desejará excluir campos com cadeias de caracteres em inglês de seus resultados. Utilizar o **$select** permite o parâmetro controle sobre quais os campos são retornados ao aplicativo de chamada de consulta.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Embora haja no $filter argumento na consulta, este caso de utilização é vivamente afiliado com conceitos de filtro, pelo que iremos apresentá-los como um cenário de filtragem.

## <a name="see-also"></a>Consulte também

+ [Filtros no Azure Search](search-filters.md)
+ [Analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md)
+ [Procurar nos documentos de REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

