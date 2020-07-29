---
title: Configurar o seu ambiente para o Operador de Blueprint
description: Aprenda a configurar o seu ambiente Azure para utilização com a função de controlo de acesso incorporado (RBAC) do Operador De Plantas.
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: a61997f5e3247e5b42d5e2f07c0fc38cc1b5114d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864016"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurar o seu ambiente para um Operador Blueprint

A gestão das definições do seu projeto e atribuições de projetos pode ser atribuída a diferentes equipas. É comum que um arquiteto ou equipa de governação seja responsável pela gestão do ciclo de vida das suas definições de projeto, enquanto uma equipa de operações é responsável pela gestão de atribuições dessas definições de planta controladas centralmente.

O comando de acesso incorporado ao **operador de** plantas (RBAC) foi concebido especificamente para ser utilizado neste tipo de cenário. O papel permite que as equipas de tipo operações gerem a atribuição das definições de plantação das organizações, mas não a capacidade de modificá-las. Ao fazê-lo, requer alguma configuração no seu ambiente Azure e este artigo explica os passos necessários.

## <a name="grant-permission-to-the-blueprint-operator"></a>Conceder permissão ao operador de plantas

O primeiro passo é conceder a função de **Operador De Blueprint** à conta ou grupo de segurança (recomendado) que vai atribuir plantas. Esta ação deve ser feita ao mais alto nível na hierarquia do grupo de gestão que engloba todos os grupos de gestão e subscrições a que a equipa de operações deve ter acesso à atribuição de projetos. Recomenda-se seguir o princípio do menor privilégio ao conceder estas permissões.

1. (Recomendado) [Criar um grupo de segurança e adicionar membros](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Adicione uma atribuição](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) de papel do **Operador Blueprint** à conta ou grupo de segurança

## <a name="user-assign-managed-identity"></a>Identidade gerida atribuída ao utilizador

Uma definição de planta pode usar identidades geridas atribuídas pelo sistema ou atribuídas pelo utilizador. No entanto, ao utilizar a função **de Operador de Plantas,** a definição de planta precisa de ser configurada para utilizar uma identidade gerida atribuída pelo utilizador. Além disso, a conta ou grupo de segurança que recebe a função **de Operador de Blueprint** deve ser concedida a função de Operador de Identidade **Gerida** na identidade gerida atribuída pelo utilizador. Sem esta permissão, as atribuições de plantas falham por falta de permissões.

1. [Criar uma identidade gerida atribuída pelo utilizador](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para utilização por uma planta atribuída

1. [Adicione uma atribuição](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) de função do Operador de **Identidade Gerida** à conta ou grupo de segurança. Âmbito da atribuição de funções à nova identidade gerida atribuída pelo utilizador.

1. Como **Operador de Blueprint,** [atribua uma planta](../create-blueprint-portal.md#assign-a-blueprint) que utilize a nova identidade gerida atribuída pelo utilizador.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).