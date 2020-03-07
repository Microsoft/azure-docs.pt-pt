---
title: Atualizar uma atribuição existente a partir do portal
description: Conheça o mecanismo de atualização de uma atribuição de plantas existente a partir do portal em Plantas Azure.
ms.date: 11/21/2019
ms.topic: how-to
ms.openlocfilehash: 2c1a24399d8fa1529665bfa60d1b889a84a29a60
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364028"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de plantas existente

Quando uma planta é atribuída, a atribuição pode ser atualizada. Existem várias razões para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [o bloqueio de recursos](../concepts/resource-locking.md)
- Alterar o valor dos [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualize a atribuição para uma versão mais recente **publicada** do projeto

## <a name="updating-assignments"></a>Atribuição de atribuições

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione **as plantas atribuídas** a partir da página à esquerda.

1. Na lista de plantas, clique à esquerda na atribuição da planta. Em seguida, clique no botão de atribuição de **atualizações** OU clique à direita na atribuição da planta e selecione a **tarefa de atualização**.

   ![Atualizar uma atribuição de plantas existente](../media/update-existing-assignments/update-assignment.png)

1. A página de **design de atribuição** carregará pré-preenchida com todos os valores da atribuição original.
   Pode alterar a versão de definição de **planta,** o estado de Atribuição de **Bloqueio,** e qualquer um dos parâmetros dinâmicos que existem na definição de planta. Clique em **Atribuir** quando terminar a fazer alterações.

1. Na página de detalhes de atribuição atualizada, consulte o novo estado. Neste exemplo, adicionámos **o Locking** à atribuição.

   ![Atualizado uma atribuição de plantas existente - modo de bloqueio alterado](../media/update-existing-assignments/updated-assignment.png)

1. Explore detalhes sobre outras **operações** de Atribuição usando a entrega. A tabela de atualizações de **recursos geridos** através de uma operação de atribuição selecionada.

   ![Operações de atribuição de uma atribuição de plantas](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regras para a atualização de atribuições

A implantação das atribuições atualizadas segue algumas regras importantes. Estas regras determinam o que acontece aos recursos já implantados. A alteração solicitada e o tipo de recurso de artefacto que está a ser implantado ou atualizado determinam quais as ações que são tomadas.

- Atribuições de Funções
  - Se o papel ou o designado (utilizador, grupo ou app) mudar, é criada uma nova atribuição de funções. As atribuições de funções anteriormente implantadas são deixadas no lugar.
- Atribuições de Política
  - Se os parâmetros da atribuição de apólices forem alterados, a atribuição existente é atualizada.
  - Se a definição da atribuição de políticas for alterada, é criada uma nova atribuição de políticas.
    As atribuições políticas previamente implantadas são deixadas no lugar.
  - Se o artefacto de atribuição de políticas for removido do projeto, as atribuições políticas implementadas são deixadas no lugar.
- Modelos do Azure Resource Manager
  - O modelo é processado através do Gestor de Recursos como **PUT**. À medida que cada tipo de recurso lida com esta ação de forma diferente, reveja a documentação de cada recurso incluído para determinar o impacto desta ação quando executado por Blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros na atualização de atribuições

Ao atualizar as atribuições, é possível fazer alterações que rompam quando executadas. Um exemplo é mudar a localização de um grupo de recursos depois de já ter sido implantado. Qualquer alteração que seja suportada pelo Gestor de [Recursos do Azure](../../../azure-resource-manager/management/overview.md) pode ser feita, mas qualquer alteração que resulte num erro através do Gestor de Recursos do Azure também resultará na falha da atribuição.

Não há limite para quantas vezes uma missão pode ser atualizada. Se ocorrer um erro, determine o erro e faça outra atualização da atribuição.  Exemplo de erros:

- Um mau parâmetro
- Um objeto já existente
- Uma mudança não suportada pelo Gestor de Recursos azure

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).