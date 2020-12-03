---
title: 'Como: navegar no Catálogo de Dados'
description: Este artigo dá uma visão geral de como navegar no Catálogo de Dados Azure Purview com base no tipo de ativo.
author: hrasheed-msft
ms.author: hrasheed
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: b8cdbbc29472ae10920c347dde308c352bf0b68a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553607"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Navegue no catálogo de dados Azure Purview Data

Este artigo descreve como descobrir dados no seu Catálogo de Dados Azure Purview utilizando o espaço hierárquico de dados hierárquico.

## <a name="browse-experience"></a>Navegue pela experiência

Um consumidor de dados pode descobrir dados usando o espaço hierárquico familiar para cada uma das fontes de dados usando uma visão do explorador. Uma vez registada e digitalizada a fonte de dados, o mapa de Dados extrai informações sobre a estrutura (espaço hierárquico) da fonte de dados. Esta informação é usada para construir a experiência de navegação para a descoberta de dados.

Por exemplo, pode facilmente encontrar um conjunto de dados chamado *DateDimension* sob uma pasta chamada *Dimensions* in Azure Data lake Storage Gen 2. Pode utilizar a experiência 'Procurar por Tipo de Activo' para navegar na conta de armazenamento ADLS Gen 2 e, em seguida, navegar para o serviço > contentor > pasta para chegar à pasta *dimensões* específica e, em seguida, ver a tabela *DateDimension.*

Uma experiência de navegação nativa com espaço hierárquico é fornecida para cada uma das fontes de dados correspondentes.

## <a name="browse-the-data-catalog-by-asset-type"></a>Navegue no Catálogo de Dados por tipo de ativo

1. Pode navegar em ativos de dados, selecionando a **Navegação por tipo de ativo** na página inicial.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Página inicial de purview" border="true":::

1. Na página **de tipos de ativos Browse,** os azulejos são categorizados por fontes de dados. Para explorar ainda mais os ativos em cada fonte de dados, selecione o azulejo correspondente.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Navegue na página de tipos de ativos" border="true":::

1. Na página seguinte, os ativos de alto nível sob o seu tipo de dados escolhidos estão listados. Escolha um dos ativos para explorar ainda mais o seu conteúdo.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Página de navegação específica do tipo de ativo. Exemplo mostrado é a conta de armazenamento Azure" border="true":::

1. A vista do explorador abrir-se-á. Comece a navegar selecionando o ativo no painel esquerdo. Os bens das crianças serão listados no painel certo da página.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Vista do explorador" border="true":::

1. Para ver os detalhes de um ativo, selecione o nome ou o botão elipses na extrema-direita.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Selecione o botão elipses para ver a página de detalhes do ativo" border="true":::

## <a name="view-related-data-assets"></a>Ver recursos de dados relacionados

Uma vez que você está na página de detalhes do ativo, você também pode ver outros ativos de dados relacionados. Por exemplo, pode navegar para a pasta principal da *DateDimension* para ver a pasta *Dimensions* ou até mesmo navegar para o contentor para ver os ativos sob a hierarquia específica.

1. Na página de detalhes do ativo, selecione o **separador Relacionado** para ver outros ativos relacionados.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Separador relacionado com lançamento" border="true":::

1. A hierarquia completa do ativo atual será listada à esquerda.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Estrutura hierárquica" border="true":::

1. Selecione qualquer nível na hierarquia para listar os ativos imediatos abaixo desse nível.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Selecione diferente hierarquia" border="true":::

## <a name="next-steps"></a>Passos seguintes

- [Como criar, importar e exportar termos glossários](how-to-create-import-export-glossary.md)
- [Como gerir modelos de prazo para glossário de negócios](how-to-manage-term-templates.md)
