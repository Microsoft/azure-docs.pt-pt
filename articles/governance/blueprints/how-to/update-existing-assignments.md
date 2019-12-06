---
title: Atualizar uma atribuição existente do portal
description: Saiba mais sobre o mecanismo de atualização de uma atribuição de Blueprint existente do portal em plantas do Azure.
ms.date: 11/21/2019
ms.topic: how-to
ms.openlocfilehash: 3cce84127475b2f11388b4ad4eb48dedec19cd1d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873170"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de plano gráfico existente

Quando um plano gráfico é atribuído, a atribuição pode ser atualizada. Há várias razões para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [bloqueio de recursos](../concepts/resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **publicada** mais recente do Blueprint

## <a name="updating-assignments"></a>Atualizando atribuições

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione **plantas atribuídas** na página à esquerda.

1. Na lista de plantas, clique com o botão esquerdo do mouse na atribuição Blueprint. Em seguida, clique no botão **Atualizar atribuição** ou clique com o botão direito do mouse na atribuição Blueprint e selecione **Atualizar atribuição**.

   ![Atualizar uma atribuição de Blueprint existente](../media/update-existing-assignments/update-assignment.png)

1. A página **atribuir Blueprint** será carregada previamente preenchida com todos os valores da atribuição original.
   Você pode alterar a **versão de definição do Blueprint**, o estado de **atribuição de bloqueio** e qualquer um dos parâmetros dinâmicos existentes na definição do Blueprint. Clique em **atribuir** quando terminar de fazer alterações.

1. Na página detalhes de atribuição atualizados, consulte o novo status. Neste exemplo, adicionamos o **bloqueio** à atribuição.

   ![Atualizou uma atribuição de Blueprint existente – modo de bloqueio alterado](../media/update-existing-assignments/updated-assignment.png)

1. Explore os detalhes sobre outras **operações de atribuição** usando a lista suspensa. A tabela de atualizações de **recursos gerenciados** por operação de atribuição selecionada.

   ![Operações de atribuição de uma atribuição de Blueprint](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regras para atualizar atribuições

A implantação das atribuições atualizadas segue algumas regras importantes. Essas regras determinam o que acontece porque os recursos já foram implantados. A alteração solicitada e o tipo de recurso de artefato que está sendo implantado ou atualizado determinam quais ações são executadas.

- Atribuições de Funções
  - Se a função ou o destinatário da função (usuário, grupo ou aplicativo) for alterado, uma nova atribuição de função será criada. As atribuições de função implantadas anteriormente são deixadas em vigor.
- Atribuições de Política
  - Se os parâmetros da atribuição de política forem alterados, a atribuição existente será atualizada.
  - Se a definição da atribuição de política for alterada, uma nova atribuição de política será criada.
    As atribuições de política implantadas anteriormente são deixadas em vigor.
  - Se o artefato de atribuição de política for removido do plano gráfico, as atribuições de política implantadas serão deixadas em vigor.
- Modelos do Azure Resource Manager
  - O modelo é processado por meio do Gerenciador de recursos como um **Put**. Como cada tipo de recurso manipula essa ação de forma diferente, examine a documentação de cada recurso incluído para determinar o impacto dessa ação quando executado por plantas.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros na atualização de atribuições

Ao atualizar atribuições, é possível fazer alterações que sejam interrompidas quando executadas. Um exemplo é alterar o local de um grupo de recursos depois que ele já tiver sido implantado. Qualquer alteração compatível com [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) pode ser feita, mas qualquer alteração que resulte em um erro por meio de Azure Resource Manager também resultará na falha da atribuição.

Não há limite de quantas vezes uma atribuição pode ser atualizada. Se ocorrer um erro, determine o erro e faça outra atualização para a atribuição.  Cenários de erro de exemplo:

- Um parâmetro inadequado
- Um objeto já existente
- Uma alteração sem suporte pelo Azure Resource Manager

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).