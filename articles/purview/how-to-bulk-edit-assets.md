---
title: Como editar em massa ativos para marcar classificações, termos glossários e modificar contactos
description: Aprenda ativos de edição em massa em Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 4dc1af590a1965c155a7af7b233b431f982a73d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024239"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>Como editar em massa ativos para anotar classificações, termos glossários e modificar contactos

Este artigo descreve como marcar vários termos glossários, classificações, proprietários e especialistas numa lista de ativos selecionados numa única ação.

### <a name="add-assets-to-view-selected-list-using-search"></a>Adicionar Ativos para Ver lista selecionada usando pesquisa

1. Procure no ativo de dados que pretende adicionar à lista de edição em massa.

2. Na página de resultados de pesquisa, paire sobre o ativo que pretende adicionar à lista **selecionada** de edição em massa para ver uma caixa de verificação.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Screenshot da caixa de verificação.":::

3. Selecione a caixa de verificação para adicioná-la à lista **selecionada de** edição em massa. Uma vez adicionado, verá o ícone de itens selecionados na parte inferior da página.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Screenshot da lista.":::

4. Repita os passos acima para adicionar todos os ativos de dados à lista.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Adicionar Ativos para Ver lista selecionada a partir da página de detalhes do ativo

1. Na página de detalhes do ativo, selecione a caixa de verificação no canto superior direito para adicionar o ativo à lista **selecionada de** edição em massa.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Screenshot do ativo.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Edição a granel na lista selecionada para adicionar, substituir ou remover termos glossários.

1. Um é feito com a identificação de todos os ativos de dados que precisam de ser editados a granel, **Selecione Ver** lista selecionada a partir da página de resultados de pesquisa ou página de detalhes do ativo.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Screenshot da vista.":::

2. Reveja a lista e **selecione Remover** se pretender remover quaisquer termos.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Screenshot da remoção.":::

3. Selecione a massa para adicionar, remover ou substituir termos glossários para todos os ativos selecionados.

4. Para adicionar termos glossários, selecione Operação como **Adicionar**. Selecione todos os termos glossários que pretende adicionar no valor Novo. Selecione Aplicar quando estiver concluído.
    - Adicionar a operação irá anexar Novo valor à lista de termos glossários já marcados para ativos de dados.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Screenshot do add.":::

5. Para substituir termos glossários selecione Operação como **Substitua por**. Selecione todos os termos glossários que pretende substituir no valor Novo. Selecione Aplicar quando estiver concluído.
    - Substituir a operação substituirá todos os termos glossários para os ativos de dados selecionados pelos termos selecionados em Novo Valor.
   
6. Para remover termos glossários selecione Operação como **Remover**. Selecione Aplicar quando estiver concluído.
    - A operação de remoção removerá todos os termos glossários para os ativos de dados selecionados.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Screenshot dos termos de remoção.":::

7. Repita o acima para classificações, proprietários e especialistas.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Screenshot das classificações e contactos.":::

8. Uma vez concluída, feche a lâmina de edição a granel selecionando **Fechar** ou **remover tudo e fechar**. O encerramento não removerá os ativos selecionados, enquanto que remover todos e fechar removerá todos os ativos selecionados.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Imagem de perto.":::

   > [!Important]
   > O número recomendado de ativos para edição a granel é de 25. A seleção de mais de 25 pode causar problemas de desempenho.
   > A caixa **Visualizada** só será visível se houver pelo menos um ativo selecionado.


Siga o [Tutorial: Crie e importe termos glossários](how-to-create-import-export-glossary.md) para saber mais.
