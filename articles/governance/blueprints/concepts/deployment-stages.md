---
title: Fases da implementação de um esquema
description: Aprenda os passos relacionados com a segurança e artefactos que os serviços da Azure Blueprints passam enquanto criam uma atribuição de projeto.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: e686dec1c9d79e42dafee17d8a937284aac4fdd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918556"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fases da implementação de um esquema

Quando uma planta é implementada, uma série de ações é tomada pelo serviço Azure Blueprints para implementar os recursos definidos na planta. Este artigo fornece detalhes sobre o que cada passo envolve.

A implementação do plano é desencadeada atribuindo uma planta a uma subscrição ou [atualizando uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, a Azure Blueprints toma os seguintes passos de alto nível:

> [!div class="checklist"]
> - AZure Blueprints concedeu direitos ao proprietário
> - O objeto de atribuição de plantas é criado
> - Opcional - Azure Blueprints cria identidade gerida **atribuída ao sistema**
> - A identidade gerida implanta artefactos de planta
> - O serviço Azure Blueprints e os direitos de identidade geridos **atribuídos pelo sistema** são revogados

## <a name="azure-blueprints-granted-owner-rights"></a>AZure Blueprints concedeu direitos ao proprietário

O principal do serviço Azure Blueprints recebe direitos de proprietário sobre a subscrição ou subscrições atribuídas quando é utilizada uma identidade [gerida pelo sistema.](../../../active-directory/managed-identities-azure-resources/overview.md) O papel concedido permite que a Azure Blueprints crie, e mais tarde revogue, a identidade gerida **atribuída pelo sistema.** Se utilizar uma identidade gerida **atribuída pelo utilizador,** o diretor de serviço da Azure Blueprints não obtém e não necessita de direitos de proprietário sobre a subscrição.

Os direitos são concedidos automaticamente se a atribuição for feita através do portal. No entanto, se a atribuição for feita através da API REST, a concessão dos direitos tem de ser feita com uma chamada separada da API. O Azure Blueprints AppId `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` é, mas o principal serviço varia por inquilino. Utilize o serviço [Azure Ative Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) e REST endpointPrincipals para obter o principal serviço. [](/graph/api/resources/serviceprincipal) Em seguida, conceda ao Azure Blueprints a função _de Proprietário_ através do [Portal](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI,](../../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [REST API,](../../../role-based-access-control/role-assignments-rest.md)ou um [modelo de Gestor de Recursos Azure](../../../role-based-access-control/role-assignments-template.md).

O serviço Azure Blueprints não implementa diretamente os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição de plantas é criado

Um utilizador, grupo ou diretor de serviço atribui uma planta a uma subscrição. O objeto de atribuição existe no nível de subscrição onde a planta foi atribuída. Os recursos criados pela implantação não são feitos no contexto da entidade de implantação.

Ao criar a atribuição da planta, o tipo de [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionado. O padrão é uma identidade gerida **atribuída pelo sistema.** Uma identidade gerida **atribuída ao utilizador** pode ser escolhida. Ao utilizar uma identidade gerida **atribuída pelo utilizador,** deve ser definida e concedida permissões antes da criação da designação. Tanto as funções incorporadas [do Proprietário](../../../role-based-access-control/built-in-roles.md#owner) como do Operador [de Blueprint](../../../role-based-access-control/built-in-roles.md#blueprint-operator) têm a permissão necessária para criar uma atribuição que utilize uma identidade gerida atribuída `blueprintAssignment/write` pelo **utilizador.**

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Opcional - Azure Blueprints cria identidade gerida atribuída ao sistema

Quando [a identidade gerida atribuída pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionada durante a atribuição, a Azure Blueprints cria a identidade e concede à identidade gerida a função de [proprietário.](../../../role-based-access-control/built-in-roles.md#owner) Se uma [atribuição existente for atualizada, a](../how-to/update-existing-assignments.md)Azure Blueprints utiliza a identidade gerida previamente criada.

A identidade gerida relacionada com a atribuição do projeto é utilizada para implantar ou redistribuir os recursos definidos na planta. Este design evita atribuições inadvertidamente interferindo entre si.
Este design também suporta a [função de bloqueio de recursos](./resource-locking.md) controlando a segurança de cada recurso implantado a partir da planta.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerida implanta artefactos de planta

A identidade gerida ativa então desencadeia as implementações do Gestor de Recursos dos artefactos dentro da planta na ordem de [sequenciação](./sequencing-order.md)definida . A ordem pode ser ajustada para garantir que artefactos dependentes de outros artefactos sejam implantados na ordem correta.

Uma falha de acesso por uma implantação é muitas vezes o resultado do nível de acesso concedido à identidade gerida. O serviço Azure Blueprints gere o ciclo de vida de segurança da identidade gerida **atribuída pelo sistema.** No entanto, o utilizador é responsável pela gestão dos direitos e ciclo de vida de uma identidade gerida **atribuída pelo utilizador.**

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>O serviço de blueprint e os direitos de identidade geridos atribuídos pelo sistema são revogados

Uma vez concluídas as implementações, a Azure Blueprints revoga os direitos da identidade gerida **atribuída pelo sistema** a partir da subscrição. Em seguida, o serviço Azure Blueprints revoga os seus direitos da subscrição. A remoção de direitos impede que a Azure Blueprints se torne proprietária permanente de uma subscrição.

## <a name="next-steps"></a>Próximos passos

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](./sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](./resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).
