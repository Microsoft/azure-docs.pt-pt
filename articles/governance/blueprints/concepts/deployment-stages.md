---
title: Fases da implementação de um esquema
description: Aprenda as etapas relacionadas com a segurança e artefactos que os serviços Azure Blueprints passam enquanto criam uma atribuição de plantas.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: daa7722fa37547929aa21b76b870f70143ae71ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156629"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fases da implementação de um esquema

Quando uma planta é implementada, uma série de ações é tomada pelo serviço Azure Blueprints para implementar os recursos definidos no projeto. Este artigo fornece detalhes sobre o que cada passo envolve.

A implementação do projeto é desencadeada atribuindo uma planta a uma subscrição ou [atualizando uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, as Plantas toma os seguintes passos de alto nível:

> [!div class="checklist"]
> - Plantas concedidas direitos de proprietário
> - O objeto de atribuição de plantas é criado
> - Opcional - Plantas criam identidade gerida **atribuída pelo sistema**
> - A identidade gerida implementa artefactos de plantas
> - O serviço de plantas e os direitos de identidade geridos **atribuídos** pelo sistema são revogados

## <a name="blueprints-granted-owner-rights"></a>Plantas concedidas direitos de proprietário

O diretor de serviço seletiva azure é concedido direitos de proprietário para a subscrição ou subscrições atribuídas quando uma identidade gerida por identidade gerida [pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é utilizada. O papel concedido permite que as Plantas criem, e mais tarde, a identidade gerida atribuída pelo **sistema.** Se utilizar uma identidade gerida atribuída ao **utilizador,** o diretor de serviço seletiva do Azure Blueprints não obtém e não precisa dos direitos do proprietário na subscrição.

Os direitos são concedidos automaticamente se a atribuição for feita através do portal. No entanto, se a atribuição for feita através da API REST, a concessão dos direitos deve ser feita com uma chamada separada da API. O Azure Blueprints `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`AppId é, mas o diretor de serviço varia de acordo com o inquilino. Utilize o [Azure Ative Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) e os [serviços](/graph/api/resources/serviceprincipal) endpoint REST para obter o diretor de serviço. Em seguida, conceda ao Azure Blueprints a função _Proprietário_ através do [Portal](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI,](../../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [REST API,](../../../role-based-access-control/role-assignments-rest.md)ou um modelo de Gestor de [Recursos](../../../role-based-access-control/role-assignments-template.md).

O serviço Blueprints não implanta diretamente os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição de plantas é criado

Um utilizador, grupo ou diretor de serviço atribui uma planta a uma subscrição. O objeto de atribuição existe ao nível da subscrição onde a planta foi atribuída. Os recursos criados pela implantação não são feitos no contexto da entidade de implantação.

Ao criar a atribuição da planta, o tipo de [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionado. O padrão é uma identidade gerida **atribuída pelo sistema.** Pode ser escolhida uma identidade gerida atribuída ao **utilizador.** Ao utilizar uma identidade gerida atribuída ao **utilizador,** deve ser definida e concedida permissões antes da criação da atribuição do projeto. Tanto as funções [de Proprietário](../../../role-based-access-control/built-in-roles.md#owner) como de Operador de [Plantas](../../../role-based-access-control/built-in-roles.md#blueprint-operator) têm a permissão necessária `blueprintAssignment/write` para criar uma atribuição que utilize uma identidade gerida atribuída ao **utilizador.**

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcional - Plantas criam identidade gerida atribuída pelo sistema

Quando a [identidade gerida atribuída](../../../active-directory/managed-identities-azure-resources/overview.md) ao sistema é selecionada durante a atribuição, as Plantas criam a identidade e concedem à identidade gerida a função do [proprietário.](../../../role-based-access-control/built-in-roles.md#owner) Se uma [atribuição existente for atualizada,](../how-to/update-existing-assignments.md)as Plantas utilizam a identidade gerida anteriormente criada.

A identidade gerida relacionada com a atribuição do projeto é utilizada para implantar ou redistribuir os recursos definidos no projeto. Este desenho evita atribuições inadvertidamente interferindo entre si.
Este design também suporta a funcionalidade de bloqueio de [recursos](./resource-locking.md) controlando a segurança de cada recurso implantado a partir da planta.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerida implementa artefactos de plantas

A identidade gerida desencadeia então as implantações do Gestor de Recursos dos artefactos dentro da planta na ordem de [sequenciação](./sequencing-order.md)definida . A ordem pode ser ajustada para garantir que os artefactos dependentes de outros artefactos sejam implantados na ordem correta.

Uma falha de acesso por uma implantação é muitas vezes o resultado do nível de acesso concedido à identidade gerida. O serviço Blueprints gere o ciclo de vida de segurança da identidade gerida atribuída pelo **sistema.** No entanto, o utilizador é responsável pela gestão dos direitos e ciclo de vida de uma identidade gerida atribuída ao **utilizador.**

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>O serviço de plantas e os direitos de identidade geridos atribuídos pelo sistema são revogados

Uma vez concluídas as implementações, as Plantas revogam os direitos da identidade gerida atribuída pelo **sistema** a partir da subscrição. Em seguida, o serviço Blueprints revoga os seus direitos da subscrição. A remoção de direitos impede que as Plantas se tornem proprietárias permanentes numa subscrição.

## <a name="next-steps"></a>Passos seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).
