---
title: Como atualizar uma atribuição existente no portal
description: Saiba mais sobre o mecanismo para a atualização de uma atribuição existente a partir do portal em esquemas do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c75bd8c3831bad0c8217f16315843cbe3824fe4d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792509"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma existente da atribuição do esquema

Quando lhe for atribuído um plano gráfico, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [bloqueio do recurso](../concepts/resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma mais recente **publicado** versão do esquema

## <a name="updating-assignments"></a>A atualizar as atribuições

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione **atribuído a planos gráficos** partir da página à esquerda.

1. Na lista de esquemas, left-click a atribuição do esquema. Em seguida, clique no **atualizar atribuição** ou a atribuição do esquema com o botão direito e selecione **atualizar atribuição**.

   ![Atualizar uma existente da atribuição do esquema](../media/update-existing-assignments/update-assignment.png)

1. O **esquema de atribuir** página será carregada previamente preenchido com todos os valores da atribuição original. Pode alterar o **versão da definição do esquema**, o **atribuição de bloqueio** estado e qualquer um dos parâmetros dinâmicos que existem na definição do esquema. Clique em **atribuir** quando terminar de efetuar alterações.

1. Na página de detalhes de atribuição atualizada, ver o estado de novo. Neste exemplo, adicionamos **bloqueio** para a atribuição.

   ![Atualizar uma atribuição de esquema existentes - alterado de modo de bloqueio](../media/update-existing-assignments/updated-assignment.png)

1. Para explorar detalhes sobre outras **operações de atribuição** usando a menu pendente. A tabela da **recursos gerenciados** atualizações por operação de atribuição selecionado.

   ![Operações de atribuição de uma atribuição do esquema](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regras para atualizar as atribuições

A implementação das atribuições atualizadas segue algumas regras importantes. Estas regras determinam o que acontece aos recursos já implementados. A alteração pedida e o tipo de recurso do artefacto a ser implementado ou atualizado determinam quais ações são executadas.

- Atribuições de Funções
  - Se a função ou o detentor de função (utilizador, grupo ou aplicação) for alterado, é criada uma nova atribuição de função. Atribuições de funções implementadas anteriormente são deixadas no lugar.
- Atribuições de Política
  - Se os parâmetros da atribuição de política são alterados, a atribuição existente é atualizada.
  - Se a definição de atribuição de política é alterada, é criada uma nova atribuição de política. Atribuições de política implementadas anteriormente são deixadas no lugar.
  - Se o artefacto de atribuição de política é removido do plano gráfico, implementado atribuições são deixadas no lugar de política.
- Modelos do Azure Resource Manager
  - O modelo é processado através do Resource Manager como um **colocar**. Cada tipo de recurso manipula esta ação de forma diferente, reveja a documentação para cada recurso incluído determinar o impacto desta ação quando executada pelo esquemas.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros sobre como atualizar atribuições

Ao atualizar as atribuições, é possível fazer alterações que não funcionam quando executado. Um exemplo é alterar a localização de um grupo de recursos, após já ter sido implementado. Qualquer alteração que são suportadas pelo [do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) podem ser feitas, mas qualquer alteração que seria resultarem num erro através do Gestor de recursos do Azure também irá resultar numa falha da atribuição.

Não existe nenhum limite no número de vezes que uma atribuição pode ser atualizado. Se ocorrer um erro, determinar o erro e faça outra atualização para a atribuição.  Os exemplos de cenários de erro:

- Um parâmetro incorreto
- Um objeto já existente
- Uma alteração não suportada pelo Azure Resource Manager

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida de um esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).