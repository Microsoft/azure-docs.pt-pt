---
title: Como proteger a sua hierarquia de recursos - Azure Governance
description: Saiba como proteger a sua hierarquia de recursos com definições de hierarquia que incluem a definição do grupo de gestão padrão.
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: 2f8d3d7ff85552fe18a49137450aefe91d0d02c9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366777"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Como proteger a sua hierarquia de recursos

Os seus recursos, grupos de recursos, subscrições, grupos de gestão e inquilinos constituem colectivamente a sua hierarquia de recursos. As configurações no grupo de gestão de raízes, tais como funções personalizadas Azure ou atribuições de política da Azure Policy, podem ter impacto em todos os recursos da sua hierarquia de recursos. É importante proteger a hierarquia de recursos de mudanças que podem ter um impacto negativo em todos os recursos.

Os grupos de gestão têm agora configurações de hierarquia que permitem ao administrador do inquilino controlar estes comportamentos. Este artigo abrange cada uma das definições de hierarquia disponíveis e como defini-las.

## <a name="rbac-permissions-for-hierarchy-settings"></a>Permissões DO RBAC para configurações de hierarquia

A configuração de qualquer uma das definições da hierarquia requer as seguintes duas operações do RBAC no grupo de gestão de raízes:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Estas operações apenas permitem que um utilizador leia e atualize as definições da hierarquia. As operações não fornecem qualquer outro acesso à hierarquia do grupo de gestão ou recursos na hierarquia. Ambas as operações estão disponíveis no Administrador de **Definições da Hierarquia**da Azure.

## <a name="setting---default-management-group"></a>Definição - Grupo de gestão predefinido

Por padrão, uma nova subscrição adicionada dentro de um inquilino é adicionada como membro do grupo de gestão de raiz. Se as atribuições políticas, o controlo de acesso baseado em funções (RBAC) e outras construções de governação forem atribuídas ao grupo de gestão de raízes, elas efetuam imediatamente estas novas subscrições. Por esta razão, muitas organizações não aplicam estas construções no grupo de gestão de raízes, embora esse seja o local desejado para as atribuir. Noutros casos, é desejado um conjunto mais restritivo de controlos para novas subscrições, mas não deve ser atribuído a todas as subscrições. Esta definição suporta ambos os casos de utilização.

Ao permitir que o grupo de gestão padrão para novas subscrições seja definido, as construções de governação a nível da organização podem ser aplicadas no grupo de gestão de raiz, e um grupo de gestão separado com atribuições políticas ou atribuições de RBAC mais adequadas a uma nova subscrição pode ser definido.

Para configurar esta definição, o ponto final da API de [Definições de Hierarquia](/rest/api/resources/hierarchysettings) REST é chamado. Para tal, utilize o seguinte REST API URI e formato corporal. `{rootMgID}`Substitua-se pelo ID do seu grupo de gestão de raízes e `{defaultGroupID}` pelo ID do grupo de gestão para se tornar o grupo de gestão padrão:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Corpo do Pedido

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Para retorpar o grupo de gestão predefinido para o grupo de gestão de raiz, utilize o mesmo ponto final e desaponte o **Grupo de Gestão padrão** para um valor de `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Definição - Exigir autorização

Qualquer utilizador, por padrão, pode criar novos grupos de gestão dentro de um inquilino. Os administradores de um inquilino só podem desejar fornecer estas permissões a utilizadores específicos para manter a consistência e a conformidade na hierarquia do grupo de gestão. Se estiver ativado, um utilizador requer a `Microsoft.Management/managementGroups/write` operação no grupo de gestão de raízes para criar novos grupos de gestão de crianças.

Para configurar esta definição, o ponto final da API de [Definições de Hierarquia](/rest/api/resources/hierarchysettings) REST é chamado. Para tal, utilize o seguinte REST API URI e formato corporal. Este valor é um _booleano,_ por isso forneça **verdadeiro** ou **falso** para o valor. Um valor **verdadeiro** permite este método de proteger a sua hierarquia do grupo de gestão:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Corpo do Pedido

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Para desligar a regulação, utilize o mesmo ponto final e defina **o requerer a Autenticação para oGroupCreation** a um valor de **falso**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](../create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](../manage.md)
