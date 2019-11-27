---
title: Configurar seu ambiente para o operador Blueprint
description: Saiba como configurar seu ambiente do Azure para uso com a função de RBAC (controle de acesso baseado em função) interna do operador Blueprint.
ms.date: 08/26/2019
ms.topic: conceptual
ms.openlocfilehash: 671ac3aaf42bddb3e775baec6838e4c271c4e855
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406377"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurar o seu ambiente para um Operador Blueprint

O gerenciamento de suas definições de plantas e atribuições de Blueprint pode ser atribuído a equipes diferentes. É comum que um arquiteto ou uma equipe de governança seja responsável pelo gerenciamento do ciclo de vida de suas definições de plantas, enquanto uma equipe de operações é responsável por gerenciar atribuições dessas definições de Blueprints controladas centralmente.

O RBAC (controle de acesso baseado em função) interno do **operador Blueprint** é projetado especificamente para uso nesse tipo de cenário. A função permite que as equipes do tipo operações gerenciem a atribuição das definições do plano gráfico das organizações, mas não a capacidade de modificá-las. Isso requer algumas configurações em seu ambiente do Azure e este artigo explica as etapas necessárias.

## <a name="grant-permission-to-the-blueprint-operator"></a>Conceder permissão ao operador Blueprint

A primeira etapa é conceder a função **operador de Blueprint** para a conta ou grupo de segurança (recomendado) que vai atribuir plantas. Essa ação deve ser feita no nível mais alto da hierarquia do grupo de gerenciamento que abrange todos os grupos de gerenciamento e assinaturas aos quais a equipe de operações deve ter acesso de atribuição Blueprint. É recomendável seguir o princípio de privilégios mínimos ao conceder essas permissões.

1. Aconselhável [Criar um grupo de segurança e adicionar membros](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Adicionar uma atribuição de função](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) do **operador Blueprint** à conta ou ao grupo de segurança

## <a name="user-assign-managed-identity"></a>Identidade gerenciada de atribuição de usuário

Uma definição de Blueprint pode usar identidades gerenciadas atribuídas pelo sistema ou pelo usuário. No entanto, ao usar a função de **operador Blueprint** , a definição de Blueprint precisa ser configurada para usar uma identidade gerenciada atribuída pelo usuário. Além disso, a conta ou grupo de segurança que recebeu a função **operador de Blueprint** precisa receber a função **operador de identidade gerenciada** na identidade gerenciada atribuída pelo usuário. Sem essa permissão, as atribuições do Blueprint falham devido à falta de permissões.

1. [Criar uma identidade gerenciada atribuída pelo usuário](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para uso por um plano gráfico atribuído

1. [Adicione uma atribuição de função](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) do **operador de identidade gerenciada** à conta ou ao grupo de segurança. Escopo a atribuição de função para a nova identidade gerenciada atribuída pelo usuário.

1. Como o **operador Blueprint**, [atribua um plano gráfico](../create-blueprint-portal.md#assign-a-blueprint) que usa a nova identidade gerenciada atribuída pelo usuário.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).