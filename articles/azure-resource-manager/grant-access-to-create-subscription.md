---
title: Conceder acesso de criar subscrições do Azure Enterprise | Documentos da Microsoft
description: Saiba como conceder a um utilizador ou principal de serviço a capacidade de criar subscrições do Azure Enterprise programaticamente.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 81f3edcfcffad1b9b6d0ab4b49769601196049ad
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238046"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso a criar subscrições do Azure Enterprise (pré-visualização)

Como cliente do Azure no [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), pode dar outro utilizador ou serviço principal permissão para criar subscrições faturadas à sua conta. Neste artigo, irá aprender a utilizar [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md) para partilhar a capacidade de criar subscrições e como efetuar a auditoria de criações de subscrição. Tem de ter a função de proprietário da conta que pretende partilhar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar subscrições numa conta de inscrição](programmatically-create-subscription.md), os utilizadores têm de ter o [função de proprietário do RBAC](../role-based-access-control/built-in-roles.md#owner) nessa conta. Pode conceder a um utilizador ou um grupo de utilizadores a função de proprietário do RBAC numa conta de inscrição, seguindo estes passos:

1. Obter o ID de objeto da conta de inscrição que pretende conceder acesso a

    Para conceder a outros utilizadores a função de proprietário do RBAC numa conta de inscrição, tem de estar o proprietário da conta ou um proprietário do RBAC da conta.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Pedido para listar todas as contas de inscrição que tem acesso a:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure responde com uma lista de todas as contas de inscrição que tem acesso a:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Utilize o `principalName` propriedade para identificar a conta que pretende conceder acesso de proprietário do RBAC a. Copiar o `name` dessa conta. Por exemplo, se quisesse conceder o acesso de proprietário do RBAC para o SignUpEngineering@contoso.com conta de inscrição, poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Este é o ID de objeto da conta de inscrição. Cole este valor, em algum lugar, para que pode usá-lo no próximo passo como `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Utilize o [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet para listar todas as contas de inscrição que tem acesso. Selecione **experimentá-la** para abrir [Azure Cloud Shell](https://shell.azure.com/). Colar o código, com o botão direito dos windows de shell e selecione **colar**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure responde com uma lista de contas de inscrição que tem acesso a:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Utilize o `principalName` propriedade para identificar a conta que pretende conceder acesso de proprietário do RBAC a. Copiar o `ObjectId` dessa conta. Por exemplo, se quisesse conceder o acesso de proprietário do RBAC para o SignUpEngineering@contoso.com conta de inscrição, poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole este ID de objeto, em algum lugar, para que pode usá-lo no próximo passo como o `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

    Utilize o [lista de conta de inscrição de faturação az](https://aka.ms/EASubCreationPublicPreviewCLI) comando para listar todas as contas de inscrição que tem acesso. Selecione **experimentá-la** para abrir [Azure Cloud Shell](https://shell.azure.com/). Colar o código, com o botão direito dos windows de shell e selecione **colar**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure responde com uma lista de contas de inscrição que tem acesso a:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Utilize o `principalName` propriedade para identificar a conta que pretende conceder acesso de proprietário do RBAC a. Copiar o `name` dessa conta. Por exemplo, se quisesse conceder o acesso de proprietário do RBAC para o SignUpEngineering@contoso.com conta de inscrição, poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Este é o ID de objeto da conta de inscrição. Cole este valor, em algum lugar, para que pode usá-lo no próximo passo como `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Obter ID de objeto do utilizador ou grupo que pretende conceder a função de proprietário do RBAC para

    1. No portal do Azure, uma procura **do Azure Active Directory**.
    1. Se quiser conceder um acesso de utilizador, clique em **utilizadores** no menu à esquerda. Se quiser conceder acesso a um grupo, clique em **grupos**.
    1. Selecione o utilizador ou grupo que pretende conceder a função de proprietário do RBAC para.
    1. A página de perfil, se tiver selecionado um usuário, encontrará o ID de objeto. Se tiver selecionado um grupo, o ID de objeto será na página de descrição geral. Copiar o **ObjectID** ao clicar no ícone à direita da caixa de texto. Cole este em algum lugar, para que pode usá-lo no próximo passo como `userObjectId`.

1. Conceder ao utilizador ou a função de proprietário do RBAC na conta de inscrição de grupo

    Utilizar os valores recolhidos nos dois primeiros passos, conceder ao utilizador ou a função de proprietário do RBAC na conta de inscrição de grupo.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Execute o seguinte comando, substituindo ```<enrollmentAccountObjectId>``` com o `name` que copiou na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` com o ID de objeto que copiou do segundo passo.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função de proprietário é atribuída com êxito no âmbito da conta de inscrição, o Azure responde com as informações de atribuição de função:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Execute o seguinte [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) comando, substituindo ```<enrollmentAccountObjectId>``` com o `ObjectId` recolhidos na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` com o objeto ID recolhidos na segunda etapa.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[CLI do Azure](#tab/azure-cli-2)

    Execute o seguinte [criação da atribuição de função de az](../active-directory/role-based-access-control-manage-access-azure-cli.md) comando, substituindo ```<enrollmentAccountObjectId>``` com o `name` copiados na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` com o objeto ID recolhidos na segunda etapa.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Assim que um utilizador for um proprietário do RBAC para a sua conta de inscrição, pode [programaticamente criar subscrições](programmatically-create-subscription.md) sob a mesma. Uma subscrição criada pelo utilizador delegado ainda tem o proprietário original da conta Administrador de serviços, mas ela também tem o utilizador delegado como um proprietário do RBAC por predefinição.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditoria que criou as subscrições com os registos de atividade

Para controlar as subscrições criadas através desta API, utilize o [API do Log de atividade de inquilino](/rest/api/monitor/tenantactivitylogs). Atualmente não é possível utilizar o PowerShell, CLI ou o portal do Azure para controlar a criação da subscrição.

1. Como administrador do inquilino do Azure AD, [eleve o acesso](../active-directory/role-based-access-control-tenant-admin-access.md) e, em seguida, atribua uma função de Leitor ao utilizador de auditoria no âmbito `/providers/microsoft.insights/eventtypes/management`.
1. Como o utilizador de auditoria, chamar o [API do Log de atividade de inquilino](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação de subscrição. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API a partir da linha de comandos, experimente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passos Seguintes

* Agora que o utilizador ou principal de serviço tem permissão para criar uma subscrição, pode utilizar essa identidade para [programaticamente criar subscrições do Azure Enterprise](programmatically-create-subscription.md).
* Para obter um exemplo sobre como criar subscrições através do .NET, consulte [código no GitHub de exemplo](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager e das respetivas APIs, veja [descrição geral do Azure Resource Manager](resource-group-overview.md).
* Para saber mais sobre como gerir um grande número de subscrições com grupos de gestão, veja [organizar os recursos com grupos de gestão do Azure](management-groups-overview.md)
* Para ver uma abrangente diretrizes de práticas recomendadas para grandes organizações na governação de subscrições, veja [scaffold enterprise do Azure - governação de subscrições prescritiva](/azure/architecture/cloud-adoption-guide/subscription-governance)
