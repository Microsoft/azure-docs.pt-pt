---
title: Fases da implementação de um esquema
description: Conheça as etapas que os serviços de plano gráfico atravessa durante uma implantação.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: a3ab5589cd327b73f2e66540da5c49343c4449cd
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57999181"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fases da implementação de um esquema

Quando um plano gráfico é implementado, uma série de ações é que o serviço de esquemas do Azure para implementar os recursos definidos no esquema. Este artigo fornece detalhes sobre o que envolve a cada passo.

Implementação do esquema é acionada ao atribuir um plano gráfico para uma subscrição ou [a atualizar uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, planos gráficos realiza as seguintes etapas de alto nível:

> [!div class="checklist"]
> - Planos gráficos foram concedidos direitos de proprietário
> - O objeto de atribuição do esquema é criado
> - Opcional - esquemas cria **atribuído ao sistema** identidade gerida
> - A identidade gerida implementa artefactos de esquema
> - Esquema de serviço e **atribuído ao sistema** são revogados direitos de identidade gerida

## <a name="blueprints-granted-owner-rights"></a>Planos gráficos foram concedidos direitos de proprietário

O principal de serviço do Azure esquemas recebe direitos de proprietário para a subscrição atribuída ou subscrições. A função concedida permite planos gráficos criar e revogar mais tarde, o [atribuído ao sistema de identidade gerido](../../../active-directory/managed-identities-azure-resources/overview.md).

Os direitos são concedidos automaticamente se a atribuição é feita através do portal. No entanto, se a atribuição é feita através da API REST, conceder os direitos tem de ser com uma API separada chamar. O AppId de esquema do Azure é `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, mas o principal de serviço varia por inquilino. Uso [API do Azure Active Directory Graph](../../../active-directory/develop/active-directory-graph-api.md) e o ponto final REST [servicePrincipals](/graph/api/resources/serviceprincipal) para obter o principal de serviço. Em seguida, conceder os esquemas do Azure a _proprietário_ função através do [Portal](../../../role-based-access-control/role-assignments-portal.md), [da CLI do Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md), ou uma [modelo do Resource Manager](../../../role-based-access-control/role-assignments-template.md).

O serviço de planos gráficos diretamente não implementa os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição do esquema é criado

Um utilizador, grupo ou principal de serviço atribui um plano gráfico para uma subscrição. Existe o objeto de atribuição ao nível da subscrição em que o esquema foi atribuído. Recursos criados pela implementação não são feitos no contexto da entidade de implementação.

Ao criar a atribuição do esquema, o tipo de [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md) está selecionada. A predefinição é um **atribuído ao sistema** identidade gerida. R **atribuído ao utilizador** identidade gerida pode ser escolhida. Quando utilizar um **atribuído ao utilizador** geridos identidade, tem de ser definido e concedida permissões para que seja criada a atribuição do esquema.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcional - esquemas cria atribuído de sistema de identidade gerida

Quando [atribuído ao sistema de identidade gerido](../../../active-directory/managed-identities-azure-resources/overview.md) está selecionada durante a atribuição, esquemas cria a identidade e concede a identidade gerida a [proprietário](../../../role-based-access-control/built-in-roles.md#owner) função. Se um [atribuição existente é atualizada](../how-to/update-existing-assignments.md), esquemas utiliza a identidade gerida criada anteriormente.

A identidade gerida relacionados com a atribuição do esquema é utilizada para implementar ou voltar a implementar os recursos definidos no esquema. Este projeto evita as atribuições de inadvertidamente interferindo entre si.
Esta estrutura também suporta o [bloqueio do recurso](./resource-locking.md) funcionalidade ao controlar a segurança de cada recurso implementado do plano gráfico.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerida implementa artefactos de esquema

A identidade gerida, em seguida, aciona as implementações do Resource Manager dos artefactos no esquema a definidos no [ordem de sequenciamento](./sequencing-order.md). A ordem pode ser ajustada para garantir que os artefactos dependentes de outros artefatos são implementados na ordem correta.

Uma falha de acesso por uma implementação é, muitas vezes, o resultado do nível de acesso concedido à identidade gerida. O serviço de planos gráficos gere o ciclo de vida de segurança do **atribuído ao sistema** identidade gerida. No entanto, o utilizador é responsável por gerenciar os direitos e o ciclo de vida de um **atribuído ao utilizador** identidade gerida.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Serviço de esquema e os direitos de identidade gerida atribuído de sistema são revogados

Assim que as implementações são concluídas, esquemas revoga os direitos do **atribuído ao sistema** identidade gerida da subscrição. Em seguida, o serviço de planos gráficos revoga seus direitos da subscrição. Remoção de direitos impede que os esquemas de se tornar um proprietário permanente numa subscrição.

## <a name="next-steps"></a>Passos Seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).