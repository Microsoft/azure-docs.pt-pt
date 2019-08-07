---
title: Como descobrir fontes de dados no catálogo de dados do Azure
description: Este artigo realça como descobrir ativos de dados registrados com o catálogo de dados do Azure, incluindo Pesquisar e filtrar e usar os recursos de realce de visita do portal do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736381"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Como descobrir fontes de dados no catálogo de dados do Azure

## <a name="introduction"></a>Introdução

O catálogo de dados do Azure é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e descoberta para fontes de dados empresariais. Em outras palavras, o catálogo de dados ajuda as pessoas a descobrir, entender e usar fontes de dados. Ele ajuda as organizações a obter mais valor de seus dados existentes. Depois que uma fonte de dados é registrada no catálogo de dados, seus metadados são indexados pelo serviço, para que você possa Pesquisar facilmente para descobrir os dados de que precisa.

## <a name="searching-and-filtering"></a>Pesquisa e filtragem

A descoberta no catálogo de dados usa dois mecanismos principais: pesquisa e filtragem.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Você pode selecionar características específicas, como especialistas, tipo de fonte de dados, tipo de objeto e marcas. Você pode exibir somente os ativos de dados correspondentes e restringir os resultados da pesquisa aos ativos correspondentes.

Usando uma combinação de pesquisa e filtragem, você pode navegar rapidamente pelas fontes de dados que foram registradas com o catálogo de dados para descobrir as fontes de dados de que precisa.

## <a name="search-syntax"></a>Sintaxe de pesquisa

Embora a pesquisa de texto livre padrão seja simples e intuitiva, você também pode usar a sintaxe de pesquisa do catálogo de dados para obter maior controle sobre os resultados da pesquisa. A pesquisa do catálogo de dados dá suporte às seguintes técnicas:

| Técnica | Utilização | Exemplo |
| --- | --- | --- |
| Pesquisa básica |Pesquisa básica que usa um ou mais termos de pesquisa. Os resultados são quaisquer ativos que correspondam a qualquer propriedade com um ou mais dos termos especificados. |`sales data` |
| Escopo da propriedade |Retornar apenas as fontes de dados em que o termo de pesquisa é correspondido com a propriedade especificada. |`name:finance` |
| Operadores Booleanos |Amplie ou restrinja uma pesquisa usando operações booleanas. |`finance NOT corporate` |
| Agrupamento com parênteses |Use parênteses para agrupar partes da consulta para obter isolamento lógico, especialmente em conjunto com operadores boolianos. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operadores de Comparação |Use comparações diferentes de igualdade para propriedades que têm tipos de dados numéricos e de data. |`modifiedTime > "11/05/2014"` |

Para obter mais informações sobre a pesquisa do catálogo de dados, consulte o artigo [Catálogo de dados do Azure](/rest/api/datacatalog/#search-syntax-reference) .

## <a name="hit-highlighting"></a>Detetor de ocorrências

Quando você exibe os resultados da pesquisa, todas as propriedades exibidas que correspondem aos termos de pesquisa especificados (como o nome do ativo de dados, a descrição e as marcas) são realçadas para facilitar a identificação do motivo pelo qual um determinado ativo de dados foi retornado por uma determinada pesquisa.

> [!NOTE]
> Para desativar o realce de botão, use a opção de realce no portal do catálogo de dados.

Quando você exibe os resultados da pesquisa, nem sempre pode ser óbvio por que um ativo de dados está incluído, mesmo com o realce de pressionamento de clique habilitado. Como todas as propriedades são pesquisadas por padrão, um ativo de dados pode ser retornado devido a uma correspondência em uma propriedade em nível de coluna. E como vários usuários podem anotar ativos de dados registrados com suas próprias marcas e descrições, nem todos os metadados são exibidos na lista de resultados da pesquisa.

No modo de exibição de bloco padrão, cada bloco exibido nos resultados da pesquisa inclui um ícone **Exibir correspondências de termo de pesquisa** , para que você possa exibir rapidamente o número de correspondências e sua localização e ir para eles, se desejar.

 ![Realce de ocorrência e correspondências de pesquisa no portal do catálogo de dados do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo

Como o registro de uma fonte de dados com o catálogo de dados copia metadados estruturais e descritivos da fonte de dados para o serviço de catálogo, a fonte de dados torna-se mais fácil de descobrir e entender. Depois de registrar uma fonte de dados, você pode descobri-la usando a filtragem e a pesquisa de dentro do portal do catálogo de dados.

## <a name="next-steps"></a>Passos Seguintes

* Para obter detalhes passo a passo sobre como descobrir fontes de dados, consulte Introdução [ao catálogo de dados do Azure](data-catalog-get-started.md).
