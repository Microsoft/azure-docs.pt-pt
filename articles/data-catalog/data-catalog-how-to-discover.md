---
title: Como descobrir origens de dados no catálogo de dados do Azure
description: Este artigo destaca como detetar recursos de dados registados no catálogo de dados do Azure, incluindo a pesquisa e filtragem e utilizando as capacidades de realce acessos do portal do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: b21bf1b50152130d7b6edd227c87fcaca28c1e6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61001424"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Como descobrir origens de dados no catálogo de dados do Azure

## <a name="introduction"></a>Introdução

Catálogo de dados do Azure é um serviço cloud totalmente gerido que funciona como um sistema de registo e deteção de origens de dados empresariais. Em outras palavras, o catálogo de dados ajuda as pessoas a detetar, compreender e utilizar origens de dados. Ele ajuda as organizações a obter mais valor dos respetivos dados existentes. Depois de uma origem de dados é registada no catálogo de dados, seus metadados é indexado pelo serviço, para que pode pesquisar facilmente para detetar os dados que necessários.

## <a name="searching-and-filtering"></a>Procurar e filtrar

A deteção no catálogo de dados utiliza dois mecanismos principais: pesquisa e filtragem.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Pode selecionar características específicas, como especialistas, tipo de origem de dados, tipo de objeto e etiquetas. Pode ver apenas os recursos de dados correspondente e restringir os resultados da pesquisa a recursos correspondentes.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente as origens de dados que foram registadas no catálogo de dados para detetar as origens de dados que precisa.

## <a name="search-syntax"></a>Sintaxe de pesquisa

Embora a pesquisa de texto livre padrão é simples e intuitiva, também pode utilizar a sintaxe de pesquisa do catálogo de dados para maior controlo sobre os resultados da pesquisa. Pesquisa do catálogo de dados suporta as seguintes técnicas:

| Técnica | Utilização | Exemplo |
| --- | --- | --- |
| Pesquisa básica |Pesquisa básica que utiliza um ou mais termos de pesquisa. Os resultados são qualquer recurso que corresponda a qualquer propriedade com um ou mais dos termos especificados. |`sales data` |
| Âmbito de propriedade |Devolva apenas origens de dados em que o termo de pesquisa tem correspondência com a propriedade especificada. |`name:finance` |
| Operadores Booleanos |Ampliar ou reduzir uma pesquisa usando operações booleanas. |`finance NOT corporate` |
| Agrupar com parênteses |Utilize parênteses para agrupar partes da consulta para alcançar isolamento lógico, especialmente em conjunto com operadores booleanos. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operadores de Comparação |Utilize comparações além de igualdade para propriedades que têm tipos de dados numéricos e de data. |`modifiedTime > "11/05/2014"` |

Para obter mais informações sobre a pesquisa do catálogo de dados, consulte a [catálogo de dados do Azure](/rest/api/datacatalog/#search-syntax-reference) artigo.

## <a name="hit-highlighting"></a>Detetor de ocorrências

Quando exibir resultados de pesquisa, qualquer apresentadas propriedades que correspondem aos termos de pesquisa especificada (por exemplo, o nome do elemento de dados, descrição e marcas) estão realçadas para torná-lo mais fácil de identificar o motivo pelo qual um ativo de dados fornecido foi devolvido por uma determinada pesquisa.

> [!NOTE]
> Para desativar o detetor de ocorrências, utilize o **realçar** mudar no portal do catálogo de dados.

Quando exibir os resultados da pesquisa, talvez não sempre seja óbvio por que um recurso de dados é incluído, mesmo com o detetor de ocorrências ativada. Uma vez que todas as propriedades serão pesquisadas por predefinição, um recurso de dados pode ser retornado devido a uma correspondência numa propriedade de nível de coluna. E porque vários usuários podem anotar recursos de dados registados com suas próprias etiquetas e descrições, nem todos os metadados são apresentados na lista de resultados da pesquisa.

No padrão de vista de mosaico, cada mosaico apresentado nos resultados da pesquisa inclui um **corresponde a termo de pesquisa de vista** ícone, para que pode exibir rapidamente o número de correspondências e a respetiva localização e para saltar para-los, se desejar.

 ![Detetor e a pesquisa corresponder a no portal do catálogo de dados do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo

Como registar uma origem de dados no catálogo de dados copia os metadados estruturais e descritivo da origem de dados para o serviço de catálogo, a origem de dados torna-se mais fáceis de detetar e compreender. Depois de registar uma origem de dados, pode detetá-lo ao utilizar a filtragem e procurar a partir do portal do catálogo de dados.

## <a name="next-steps"></a>Passos Seguintes

* Para obter detalhes passo a passo sobre como detetar origens de dados, consulte [introdução ao catálogo de dados do Azure](data-catalog-get-started.md).
