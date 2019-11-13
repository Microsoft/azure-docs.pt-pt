---
title: Fases da implementação de um esquema
description: Conheça as etapas pelas quais os serviços de Azure Blueprint passam durante uma implantação.
ms.date: 03/14/2019
ms.topic: conceptual
ms.openlocfilehash: d0d97ed01c4ae2ef96da151e1ab4ddc13a4b1d3e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960529"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fases da implementação de um esquema

Quando um plano gráfico é implantado, uma série de ações é realizada pelo serviço de plantas do Azure para implantar os recursos definidos no plano gráfico. Este artigo fornece detalhes sobre o que cada etapa envolve.

A implantação Blueprint é disparada atribuindo um plano gráfico a uma assinatura ou [atualizando uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, os planos gráficos assumem as seguintes etapas de alto nível:

> [!div class="checklist"]
> - Direitos de proprietário concedidos aos plantas
> - O objeto de atribuição Blueprint é criado
> - Opcional-os planos gráficos criam identidade gerenciada **atribuída pelo sistema**
> - A identidade gerenciada implanta artefatos do Blueprint
> - O serviço Blueprint e direitos de identidade gerenciada **atribuídos pelo sistema** são revogados

## <a name="blueprints-granted-owner-rights"></a>Direitos de proprietário concedidos aos plantas

A entidade de serviço de plantas do Azure recebe direitos de proprietário para a assinatura ou assinaturas atribuídas. A função concedida permite que plantas criem e, posteriormente, revogam a [identidade gerenciada atribuída pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md).

Os direitos serão concedidos automaticamente se a atribuição for feita por meio do Portal. No entanto, se a atribuição for feita por meio da API REST, a concessão dos direitos precisará ser feita com uma chamada à API separada. O Azure Blueprint AppId é `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, mas a entidade de serviço varia de acordo com o locatário. Use [Azure Active Directory API do Graph](../../../active-directory/develop/active-directory-graph-api.md) e os serviços [de ponto de](/graph/api/resources/serviceprincipal) extremidade REST para obter a entidade de serviço. Em seguida, conceda ao Azure plantas a função de _proprietário_ por meio do [portal](../../../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [API REST](../../../role-based-access-control/role-assignments-rest.md)ou um [modelo do Resource Manager](../../../role-based-access-control/role-assignments-template.md).

O serviço de plantas não implanta diretamente os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição Blueprint é criado

Um usuário, grupo ou entidade de serviço atribui um plano gráfico a uma assinatura. O objeto de atribuição existe no nível de assinatura em que o plano gráfico foi atribuído. Os recursos criados pela implantação não são feitos no contexto da entidade de implantação.

Ao criar a atribuição Blueprint, o tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionado. O padrão é uma identidade gerenciada **atribuída pelo sistema** . Uma identidade gerenciada **atribuída pelo usuário** pode ser escolhida. Ao usar uma identidade gerenciada **atribuída pelo usuário** , ela deve ser definida e ter permissões concedidas antes que a atribuição Blueprint seja criada.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcional-os planos gráficos criam identidade gerenciada atribuída pelo sistema

Quando a [identidade gerenciada atribuída pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionada durante a atribuição, plantas cria a identidade e concede à identidade gerenciada a função de [proprietário](../../../role-based-access-control/built-in-roles.md#owner) . Se uma [atribuição existente for atualizada](../how-to/update-existing-assignments.md), os planos gráficos usarão a identidade gerenciada criada anteriormente.

A identidade gerenciada relacionada à atribuição Blueprint é usada para implantar ou reimplantar os recursos definidos no plano gráfico. Esse design evita que as atribuições interfiram inadvertidamente entre si.
Esse design também dá suporte ao recurso de [bloqueio de recursos](./resource-locking.md) controlando a segurança de cada recurso implantado do plano gráfico.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerenciada implanta artefatos do Blueprint

Em seguida, a identidade gerenciada dispara as implantações do Resource Manager dos artefatos dentro do plano gráfico na [ordem de sequenciamento](./sequencing-order.md)definida. A ordem pode ser ajustada para garantir que os artefatos dependentes de outros artefatos sejam implantados na ordem correta.

Uma falha de acesso por uma implantação é geralmente o resultado do nível de acesso concedido à identidade gerenciada. O serviço de plantas gerencia o ciclo de vida de segurança da identidade gerenciada **atribuída pelo sistema** . No entanto, o usuário é responsável por gerenciar os direitos e o ciclo de vida de uma identidade gerenciada **atribuída pelo usuário** .

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>O serviço Blueprint e direitos de identidade gerenciada atribuídos pelo sistema são revogados

Depois que as implantações forem concluídas, as plantas revogarão os direitos da identidade gerenciada **atribuída pelo sistema** da assinatura. Em seguida, o serviço de plantas revoga seus direitos da assinatura. A remoção de direitos impede que plantas se tornem um proprietário permanente em uma assinatura.

## <a name="next-steps"></a>Passos seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).