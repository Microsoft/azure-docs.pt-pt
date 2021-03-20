---
title: Aplicar classificações sobre ativos (pré-visualização)
description: Este documento descreve como aplicar classificações sobre ativos.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554870"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Aplicar classificações sobre ativos em Azure Purview

Este artigo discute como aplicar classificações sobre ativos.

## <a name="introduction"></a>Introdução

As classificações podem ser sistema ou tipos personalizados. As classificações do sistema estão presentes em Purview por padrão. As classificações personalizadas podem ser criadas com base num padrão de expressão regular. As classificações podem ser aplicadas aos ativos, quer automaticamente quer manualmente.

Este documento explica como aplicar classificações aos seus dados.

## <a name="prerequisites"></a>Pré-requisitos

- Crie classificações personalizadas com base na sua necessidade.
- Configurar as suas fontes de dados.

## <a name="apply-classifications"></a>Aplicar classificações
No Azure Purview, pode aplicar classificações de sistema ou personalizadas num ficheiro, tabela ou ativo de coluna. Este artigo descreve os passos para aplicar manualmente classificações nos seus bens.

### <a name="apply-classification-to-a-file-asset"></a>Aplicar classificação a um ativo de ficheiro
O Azure Purview pode digitalizar e classificar automaticamente ficheiros de documentação. Por exemplo, se tiver um ficheiro nomeado *multiple.docx* e tiver um número de Identificação Nacional no seu conteúdo, a Azure Purview adiciona a classificação **número de identificação nacional da UE** à página de detalhes do ativo do ficheiro.

Em alguns cenários, pode querer adicionar manualmente classificações ao seu ativo de ficheiro. Se tiver vários ficheiros agrupados num conjunto de recursos, adicione classificações ao nível definido por recursos.

Siga estes passos para adicionar uma classificação personalizada ou do sistema a um conjunto de recursos de partição:

1. Procure ou navegue na partição e navegue para a página de detalhes do ativo.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Screenshot mostrando a página de detalhes do ativo.":::

1. No **separador Visão** Geral, consulte a secção **classificações** para ver se existem classificações existentes. Selecione **Editar**.

1. Na lista de **classificados,** selecione as classificações específicas em que está interessado. Por exemplo, **Número de Cartão de Crédito**, que é uma classificação do sistema e **CustomerAccountID,** que é uma classificação personalizada.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Screenshot mostrando como selecionar classificações para adicionar a um ativo.":::

1. Selecione **Guardar**

1. No **separador Visão** Geral, confirme que as classificações selecionadas aparecem na secção **Classificações.**

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Screenshot mostrando como confirmar classificações foram adicionados a um ativo.":::

### <a name="apply-classification-to-a-table-asset"></a>Aplicar classificação a um ativo de tabela

Quando o Azure Purview analisa as suas fontes de dados, não atribui automaticamente classificações a ativos de tabela. Se quiser que o seu ativo de mesa tenha uma classificação, deve adicioná-la manualmente.

Para adicionar uma classificação a um ativo de tabela:

1. Encontre um ativo de mesa que lhe interesse. Por exemplo, tabela **de clientes.**

1. Confirme que não são atribuídas classificações à mesa. Selecione **Editar**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Screenshot mostrando como ver e editar as classificações de um ativo de tabela.":::

1. Da lista de **classificações,** selecione uma ou mais classificações. Este exemplo utiliza uma classificação personalizada chamada **CustomerInfo,** mas pode selecionar quaisquer classificações para este passo.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Screenshot mostrando como selecionar classificações para adicionar a um ativo de tabela.":::

1. **Selecione Guardar** para guardar as classificações.

1. Na página **'Visão Geral',** verifique se o Azure Purview adicionou as suas novas classificações.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Screenshot mostrando como verificar que as classificações foram adicionadas a um ativo de tabela.":::

### <a name="add-classification-to-a-column-asset"></a>Adicionar classificação a um ativo de coluna

A Azure Purview digitaliza automaticamente e adiciona classificações a todos os ativos da coluna. No entanto, se quiser alterar a classificação, pode fazê-lo ao nível da coluna.

Para adicionar uma classificação a uma coluna:

1. Encontre e selecione o ativo da coluna e, em seguida, **selecione Editar** a partir do **separador Visão** Geral.

1. Selecione o **separador Schema**

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Screenshot mostrando como editar o esquema de uma coluna.":::

1. Identifique as colunas em que está interessado e selecione **Adicione uma classificação.** Este exemplo adiciona uma classificação **de Palavras-Passe Comuns** à coluna **PasswordHash.**

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Screenshot mostrando como adicionar uma classificação a uma coluna.":::

1. Selecione **Guardar**

1. Selecione o **separador Schema** e confirme que a classificação foi adicionada à coluna.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Screenshot mostrando como confirmar que uma classificação foi adicionada a um esquema de coluna.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Impacto da rescanagem nas classificações existentes

As classificações são aplicadas pela primeira vez, com base no conjunto de amostras verificar os seus dados e compará-lo com o padrão regex definido. No momento do rescan, se novas classificações se aplicarem, a coluna obtém classificações adicionais. As classificações existentes permanecem na coluna e devem ser removidas manualmente.

## <a name="next-steps"></a>Passos seguintes
Para aprender a criar uma classificação personalizada, consulte [Criar uma classificação personalizada.](create-a-custom-classification-and-classification-rule.md)