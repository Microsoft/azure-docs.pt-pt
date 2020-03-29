---
title: Configurar o seu ambiente para o Operador de Plantas
description: Aprenda a configurar o seu ambiente Azure para utilização com a função de controlo de acesso baseado em funções (RBAC) do Operador de Plantas.
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873221"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurar o seu ambiente para um Operador Blueprint

A gestão das definições de projeto e atribuições de plantas pode ser atribuída a diferentes equipas. É comum que um arquiteto ou uma equipa de governação seja responsável pela gestão do ciclo de vida das definições do seu projeto, enquanto uma equipa de operações é responsável pela gestão de atribuições dessas definições de plantas controladas centralmente.

O controlo de acesso incorporado ao Operador de **Plantas** (RBAC) foi concebido especificamente para ser utilizado neste tipo de cenário. O papel permite que as equipas do tipo operações gerem a atribuição das definições de plantas das organizações, mas não a capacidade de as modificar. Fazê-lo requer alguma configuração no seu ambiente Azure e este artigo explica os passos necessários.

## <a name="grant-permission-to-the-blueprint-operator"></a>Conceda autorização ao Operador de Plantas

O primeiro passo é atribuir o papel do Operador de **Plantas** à conta ou grupo de segurança (recomendado) que vai atribuir plantas. Esta ação deve ser feita ao mais alto nível na hierarquia do grupo de gestão que engloba todos os grupos de gestão e subscrições a que a equipa de operações deve ter acesso à atribuição de projetos. Recomenda-se seguir o princípio do menor privilégio ao conceder estas permissões.

1. (Recomendado) [Criar um grupo de segurança e adicionar membros](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Adicione uma atribuição](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) de funções do **Operador de Plantas** à conta ou grupo de segurança

## <a name="user-assign-managed-identity"></a>Identidade gerida de atribuição de utilizador

Uma definição de planta pode usar identidades geridas atribuídas pelo sistema ou atribuídas ao utilizador. No entanto, ao utilizar a função de Operador de **Plantas,** a definição de projeto tem de ser configurada para utilizar uma identidade gerida atribuída ao utilizador. Além disso, a conta ou grupo de segurança que recebe a função **de Operador de Projeto** deve ser concedida a função de Operador de Identidade **Gerida** na identidade gerida pelo utilizador. Sem esta permissão, as tarefas de plantas falham por falta de permissões.

1. [Criar uma identidade gerida atribuída ao utilizador](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para utilização por uma planta atribuída

1. [Adicione uma atribuição](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) de funções de **Operador de Identidade Gerida** à conta ou grupo de segurança. Abra a atribuição de funções à nova identidade gerida atribuída ao utilizador.

1. Como Operador de **Plantas,** [atribua uma planta](../create-blueprint-portal.md#assign-a-blueprint) que utiliza a nova identidade gerida atribuída pelo utilizador.

## <a name="next-steps"></a>Passos seguintes

- Conheça o ciclo de vida da [planta.](../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).