---
title: Como descobrir fontes de dados no Azure Data Catalog
description: Este artigo destaca como descobrir ativos de dados registados com o Azure Data Catalog, incluindo pesquisa e filtragem e usando as capacidades de destaque do portal Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52aaa11278e5bb523594936c75d6810c1638fa7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674942"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Como descobrir fontes de dados no Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução

O Azure Data Catalog é um serviço de nuvem totalmente gerido que serve como um sistema de registo e descoberta para fontes de dados empresariais. Por outras palavras, o Catálogo de Dados ajuda as pessoas a descobrir, compreender e usar fontes de dados. Ajuda as organizações a obter mais valor a partir dos seus dados existentes. Depois de uma fonte de dados ser registada no Catálogo de Dados, os seus metadados são indexados pelo serviço, para que possa facilmente pesquisar para descobrir os dados de que necessita.

## <a name="searching-and-filtering"></a>Pesquisa e filtragem

A Descoberta no Catálogo de Dados utiliza dois mecanismos primários: pesquisa e filtragem.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Pode selecionar características específicas, tais como especialistas, tipo de fonte de dados, tipo de objeto e etiquetas. Só pode visualizar os ativos de dados correspondentes e restringir os resultados da pesquisa aos ativos correspondentes.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente nas fontes de dados que foram registadas no Catálogo de Dados para descobrir as fontes de dados de que necessita.

## <a name="search-syntax"></a>Sintaxe de pesquisa

Embora a pesquisa por texto gratuita por defeito seja simples e intuitiva, também pode utilizar a sintaxe de pesquisa do Catálogo de Dados para um maior controlo sobre os resultados da pesquisa. A pesquisa do Catálogo de Dados suporta as seguintes técnicas:

| Técnica | Utilização | Exemplo |
| --- | --- | --- |
| Pesquisa básica |Pesquisa básica que utiliza um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados. |`sales data` |
| Escoagem de propriedade |Devolva apenas fontes de dados em que o termo de pesquisa é combinado com a propriedade especificada. |`name:finance` |
| Operadores booleanos |Alargar ou reduzir uma pesquisa utilizando operações booleanas. |`finance NOT corporate` |
| Agrupamento com parênteses |Use parênteses para agrupar partes da consulta para alcançar um isolamento lógico, especialmente em conjunto com os operadores booleanos. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operadores de comparação |Utilize comparações que não a igualdade para propriedades que tenham tipos de dados numéricos e data. |`modifiedTime > "11/05/2014"` |

Para obter mais informações sobre o Catálogo de Dados, consulte o artigo do [Catálogo de Dados do Azure.](/rest/api/datacatalog/#search-syntax-reference)

## <a name="hit-highlighting"></a>Detetor de ocorrências

Quando vê os resultados da pesquisa, quaisquer propriedades apresentadas que correspondam aos termos de pesquisa especificados (como o nome do ativo de dados, descrição e etiquetas) são destacadas para facilitar a identificação do motivo pelo qual um determinado ativo de dados foi devolvido por uma determinada pesquisa.

> [!NOTE]
> Para desativar o destaque de um ataque, utilize o interruptor **Highlight** no portal data catalog.

Quando vê os resultados da pesquisa, pode nem sempre ser óbvio por que um ativo de dados está incluído, mesmo com o destaque de sucesso ativado. Como todas as propriedades são pesquisadas por padrão, um ativo de dados pode ser devolvido por causa de uma correspondência numa propriedade de nível de coluna. E como vários utilizadores podem anotar os ativos de dados registados com as suas próprias etiquetas e descrições, nem todos os metadados são apresentados na lista de resultados de pesquisa.

Na vista de azulejos predefinidos, cada azulejo apresentado nos resultados da pesquisa inclui um ícone **de termos de pesquisa de visualização,** para que possa visualizar rapidamente o número de partidas e a sua localização, e saltar para eles se quiser.

 ![Acertar jogos de destaque e pesquisa no portal do Catálogo de Dados Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo

Uma vez que o registo de uma fonte de dados com o Catálogo de Dados copia metadados estruturais e descritivos da fonte de dados para o serviço de catálogo, a fonte de dados torna-se mais fácil de descobrir e compreender. Depois de ter registado uma fonte de dados, pode descobri-la utilizando a filtragem e pesquisa a partir do portal do Catálogo de Dados.

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes passo a passo sobre como descobrir fontes de dados, consulte [Get Started com O Catálogo de Dados do Azure](data-catalog-get-started.md).
