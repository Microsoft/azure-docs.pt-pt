---
title: Atualizar uma atribuição existente a partir do portal
description: Conheça o mecanismo de atualização de uma atribuição de plantas existente a partir do portal em Azure Blueprints.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: c383ebedaf83b3a52062c91f98b816c3baf6618e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919398"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de projeto existente

Quando uma planta é atribuída, a atribuição pode ser atualizada. Existem várias razões para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [o bloqueio de recursos](../concepts/resource-locking.md)
- Alterar o valor dos [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualize a atribuição para uma versão **mais recente da** planta

## <a name="updating-assignments"></a>Atualização de atribuições

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione **as plantas atribuídas** da página à esquerda.

1. Na lista de plantas, selecione a atribuição do projeto. Em seguida, utilize o botão **de atribuição de atualização** OU clique à direita na atribuição de plantas e selecione **a atribuição de Atualização**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Screenshot da página de atribuição Blueprint com o botão 'Actualização' realçado." border="false":::

1. A página **de planta Atribui** cargas pré-preenchidas com todos os valores da atribuição original. Pode alterar a **versão de definição de planta,** o estado **de Lock Assignment** e qualquer um dos parâmetros dinâmicos que existem na definição do projeto. Selecione **Atribuir** quando terminar a escoação.

1. Na página de detalhes da atribuição atualizada, consulte o novo estado. Neste exemplo, adicionámos **Locking** à missão.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Screenshot de uma designação de planta atualizada mostrando o modo de bloqueio alterado." border="false":::

1. Explore detalhes sobre outras **operações de atribuição** utilizando o drop-down. A tabela de atualizações de **recursos geridos** através de uma operação de atribuição selecionada.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Screenshot de uma atribuição de planta atualizada mostrando as operações de atribuição e o seu estado." border="false":::

## <a name="rules-for-updating-assignments"></a>Regras de atualização das atribuições

A implementação das atribuições atualizadas segue algumas regras importantes. Estas regras determinam o que acontece aos recursos já utilizados. A alteração solicitada e o tipo de recurso de artefactos que estão a ser implantados ou atualizados determinam quais as ações que são tomadas.

- Atribuições de Funções
  - Se o papel ou o atributo de função (utilizador, grupo ou app) mudar, é criada uma nova atribuição de funções. As atribuições de funções previamente implantadas são deixadas no lugar.
- Atribuições de Política
  - Se os parâmetros da atribuição da apólice forem alterados, a atribuição existente é atualizada.
  - Se a definição da atribuição da política for alterada, é criada uma nova atribuição de políticas.
    As atribuições políticas previamente implementadas são deixadas no lugar.
  - Se o artefacto de atribuição de política for removido da planta, as atribuições de política implementadas são deixadas no lugar.
- Modelos de gestor de recursos Azure (modelos ARM)
  - O modelo é processado através do Gestor de Recursos como um **PUT**. À medida que cada tipo de recurso lida com esta ação de forma diferente, reveja a documentação de cada recurso incluído para determinar o impacto desta ação quando executado pela Blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros na atualização das atribuições

Ao atualizar as atribuições, é possível fazer alterações que se quebram quando executadas. Um exemplo é a alteração da localização de um grupo de recursos depois de já ter sido implementado. Qualquer alteração que seja suportada pelo [Resource Manager](../../../azure-resource-manager/management/overview.md) pode ser feita, mas qualquer alteração que resulte num erro através do Gestor de Recursos também resultará na falha da atribuição.

Não há limite para quantas vezes uma missão pode ser atualizada. Se ocorrer um erro, determine o erro e faça outra atualização para a atribuição.  Cenários de erro de exemplo:

- Um mau parâmetro
- Um objeto já existente
- Uma alteração não suportada pelo Gestor de Recursos

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).