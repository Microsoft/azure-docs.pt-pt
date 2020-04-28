---
title: Como descobrir fontes de dados no Catálogo de Dados do Azure
description: Este artigo destaca como descobrir os ativos de dados registados com o Catálogo de Dados Azure, incluindo pesquisa e filtragem e utilização das capacidades de destaque do portal Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68736381"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Como descobrir fontes de dados no Catálogo de Dados do Azure

## <a name="introduction"></a>Introdução

O Azure Data Catalog é um serviço de cloud totalmente gerido que serve como um sistema de registo e descoberta para fontes de dados da empresa. Por outras palavras, o Data Catalog ajuda as pessoas a descobrir, compreender e usar fontes de dados. Ajuda as organizações a obter mais valor a partir dos seus dados existentes. Depois de uma fonte de dados estar registada no Data Catalog, os seus metadados são indexados pelo serviço, para que possa facilmente pesquisar para descobrir os dados de que necessita.

## <a name="searching-and-filtering"></a>Pesquisa e filtragem

A Discovery in Data Catalog utiliza dois mecanismos primários: pesquisa e filtragem.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Pode selecionar características específicas, tais como especialistas, tipo de fonte de dados, tipo de objeto e etiquetas. Pode ver apenas os ativos de dados correspondentes e limitar os resultados de pesquisa a ativos correspondentes.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente nas fontes de dados que foram registadas no Data Catalog para descobrir as fontes de dados de que necessita.

## <a name="search-syntax"></a>Sintaxe de pesquisa

Embora a pesquisa de texto gratuita por predefinido seja simples e intuitiva, também pode utilizar a sintaxe de pesquisa do Data Catalog para um maior controlo sobre os resultados da pesquisa. A pesquisa do Catálogo de Dados suporta as seguintes técnicas:

| Técnica | Utilizar | Exemplo |
| --- | --- | --- |
| Pesquisa básica |Pesquisa básica que utiliza um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados. |`sales data` |
| Scoping de propriedade |Devolva apenas fontes de dados quando o termo de pesquisa for compatível com a propriedade especificada. |`name:finance` |
| Operadores Booleanos |Alargar ou restringir uma busca utilizando operações booleanas. |`finance NOT corporate` |
| Agrupamento com parênteses |Utilize parênteses para agrupar partes da consulta para alcançar o isolamento lógico, especialmente em conjunto com os operadores booleanos. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operadores de Comparação |Utilize comparações que não a igualdade para propriedades que tenham tipos de dados numéricos e datados. |`modifiedTime > "11/05/2014"` |

Para mais informações sobre a pesquisa do Data Catalog, consulte o artigo do Catálogo de [Dados do Azure.](/rest/api/datacatalog/#search-syntax-reference)

## <a name="hit-highlighting"></a>Detetor de ocorrências

Quando visualiza os resultados da pesquisa, são destacadas quaisquer propriedades apresentadas que correspondam aos termos de pesquisa especificados (como o nome, descrição e etiquetas do ativo de dados) para facilitar a identificação do motivo pelo qual um determinado ativo de dados foi devolvido por uma determinada pesquisa.

> [!NOTE]
> Para desativar o destaque do hit, utilize o interruptor **Highlight** no portal Data Catalog.

Quando vê resultados de pesquisa, pode nem sempre ser óbvio por que um ativo de dados está incluído, mesmo com o destaque de sucesso ativado. Como todas as propriedades são pesquisadas por padrão, um ativo de dados pode ser devolvido por causa de uma correspondência em uma propriedade de nível de coluna. E como vários utilizadores podem anotar os ativos de dados registados com as suas próprias etiquetas e descrições, nem todos os metadados são apresentados na lista de resultados de pesquisa.

Na vista de azulejos predefinidos, cada azulejo apresentado nos resultados da pesquisa inclui um ícone de correspondência de prazo de **pesquisa View,** para que possa ver rapidamente o número de partidas e a sua localização, e saltar para eles se quiser.

 ![Jogos de destaque e pesquisa no portal Do Catálogo de Dados Do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo

Uma vez que registar uma fonte de dados com data Catalog copia metadados estruturais e descritivos da fonte de dados para o serviço de catálogo, a fonte de dados torna-se mais fácil de descobrir e compreender. Depois de ter registado uma fonte de dados, pode descobri-lo utilizando a filtragem e a pesquisa a partir do portal Data Catalog.

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes passo a passo sobre como descobrir fontes de dados, consulte [Start Started with Azure Data Catalog](data-catalog-get-started.md).
