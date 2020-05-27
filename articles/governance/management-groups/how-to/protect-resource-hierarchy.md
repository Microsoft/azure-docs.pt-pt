---
title: Como proteger a sua hierarquia de recursos - Azure Governance
description: Aprenda a proteger a sua hierarquia de recursos com configurações de hierarquia que incluem a definição do grupo de gestão padrão.
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: 60c184d176ae62c1af525db656c56a83422cb94a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837417"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Como proteger a sua hierarquia de recursos

Os seus recursos, grupos de recursos, subscrições, grupos de gestão e inquilinos constituem colectivamente a sua hierarquia de recursos. As configurações do grupo de gestão de raízes, tais como funções rBAC personalizadas ou atribuições políticas de Política Azure, podem ter impacto em cada recurso na sua hierarquia de recursos. É importante proteger a hierarquia de recursos de mudanças que podem ter um impacto negativo em todos os recursos.

Os grupos de gestão têm agora configurações de hierarquia que permitem ao administrador inquilino controlar estes comportamentos. Este artigo abrange cada uma das definições da hierarquia disponível e como defini-las.

## <a name="rbac-permissions-for-hierarchy-settings"></a>Permissões RBAC para configurações de hierarquia

Configurar qualquer uma das definições da hierarquia requer as seguintes duas operações RBAC no grupo de gestão de raiz:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Estas operações apenas permitem que um utilizador leia e atualize as definições da hierarquia. As operações não fornecem qualquer outro acesso à hierarquia ou recursos do grupo de gestão na hierarquia. Ambas as operações estão disponíveis no administrador de **definições**de hierarquia de funções RBAC incorporado.

## <a name="setting---default-management-group"></a>Definição - Grupo de gestão padrão

Por padrão, uma nova subscrição adicionada dentro de um inquilino é adicionada como membro do grupo de gestão de raiz. Se as atribuições políticas, o controlo de acesso baseado em funções (RBAC) e outras construções de governação forem atribuídas ao grupo de gestão de raízes, efetuam imediatamente estas novas subscrições. Por esta razão, muitas organizações não aplicam estas construções no grupo de gestão de raízes, mesmo que esse seja o local desejado para as atribuir. Noutros casos, é desejado um conjunto de controlos mais restritivo para novas subscrições, mas não deve ser atribuído a todas as subscrições. Esta definição suporta ambos os casos de utilização.

Ao permitir que o grupo de gestão padrão para novas subscrições seja definido, as construções de governação a nível organizacional podem ser aplicadas no grupo de gestão de raiz, e um grupo de gestão separado com atribuições políticas ou atribuições RBAC mais adequadas a uma nova subscrição pode ser definido.

Para configurar esta definição, chama-se o ponto final da API de [Definições de Hierarquia](/rest/api/resources/hierarchysettings) REST API. Para tal, utilize o seguinte formato REST API URI e body. `{rootMgID}`Substitua-a pela identificação do seu grupo de gestão de raiz e `{defaultGroupID}` com a identificação do grupo de gestão para se tornar o grupo de gestão padrão:

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

Para definir o grupo de gestão padrão de volta ao grupo de gestão de raiz, use o mesmo ponto final e **depreite o Grupo de Gestão** padrão para um valor de `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Definição - Exigir autorização

Qualquer utilizador, por padrão, pode criar novos grupos de gestão dentro de um inquilino. Os administradores de um inquilino só podem desejar fornecer estas permissões a utilizadores específicos para manter a consistência e conformidade na hierarquia do grupo de gestão. Se ativado, um utilizador necessita da operação no grupo de gestão de `Microsoft.Management/managementGroups/write` raízes para criar novos grupos de gestão de crianças.

Para configurar esta definição, chama-se o ponto final da API de [Definições de Hierarquia](/rest/api/resources/hierarchysettings) REST API. Para tal, utilize o seguinte formato REST API URI e body. Este valor é uma _booleana,_ por isso, forneça **verdadeiroou** **falso** para o valor. Um valor **verdadeiro** permite que este método proteja a sua hierarquia de grupo de gestão:

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

Para desligar a desativação, utilize o mesmo ponto final e **defina a Criação DeAutorizações ForGroup** a um valor de **falso**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os grupos de gestão, veja:

- [Criar grupos de gestão para organizar recursos do Azure](../create.md)
- [Como alterar, eliminar ou gerir os seus grupos de gestão](../manage.md)
