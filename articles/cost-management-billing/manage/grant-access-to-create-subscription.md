---
title: Conceder acesso para criar subscrições Enterprise do Azure
description: Saiba como dar a uma entidade principal de utilizador ou de serviço a capacidade de criar subscrições Enterprise do Azure através de programação.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: c13670df26e5d0f7774b5a2aac81f656de94c960
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844715"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar subscrições Enterprise do Azure (pré-visualização)

Na qualidade de cliente do Azure no [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), pode conceder a outra entidade principal de utilizador ou de serviço a permissão para criar subscrições faturadas para a sua conta. Neste artigo, aprenderá a usar o [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md) para partilhar a capacidade de criar subscrições e como auditar as criações de subscrições. Tem de ter a função de Proprietário na conta que pretende partilhar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar subscrições numa conta de inscrição](programmatically-create-subscription-enterprise-agreement.md), os utilizadores têm de ter a [função de Proprietário](../../role-based-access-control/built-in-roles.md#owner) do RBAC do Azure nessa conta. Pode conceder a um utilizador ou a um grupo de utilizadores a função de Proprietário de RBAC do Azure numa conta de inscrição ao seguir estes passos:

1. Obter o ID de objeto da conta de inscrição à qual pretende conceder acesso

    Para conceder a outros utilizadores a função de Proprietário de RBAC do Azure numa conta de inscrição, tem de ser o Proprietário da Conta ou um Proprietário de RBAC do Azure da conta.

    # <a name="rest"></a>[REST](#tab/rest)

    Pedido para listar todas as contas de inscrição às quais tem acesso:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    O Azure responde com uma lista de todas as contas de inscrição às quais tem acesso:

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

    Utilize a propriedade `principalName` para identificar a conta à qual pretende conceder acesso de Proprietário de RBAC do Azure. Copie o `name` dessa conta. Por exemplo, se quisesse conceder aceder acesso de Proprietário de RBAC do Azure à SignUpEngineering@contoso.comconta de inscrição, teria de copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Trata-se do ID de objeto da conta de inscrição. Cole este valor algures para que possa utilizá-lo no próximo passo como `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de inscrição às quais tem acesso. Selecione **Experimente** para abrir o [Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do rato nas janelas da shell e selecione **Colar**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    O Azure responde com uma lista de contas de inscrição às quais tem acesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Utilize a propriedade `principalName` para identificar a conta à qual pretende conceder acesso de Proprietário de RBAC do Azure. Copie o `ObjectId` dessa conta. Por exemplo, se quisesse conceder aceder acesso de Proprietário de RBAC do Azure à SignUpEngineering@contoso.comconta de inscrição, teria de copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole este ID de objeto algures para que possa utilizá-lo no próximo passo como o `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    Use o comando [az billing enrollment-account list](/cli/azure/billing) para listar todas as contas de inscrição às quais tem acesso. Selecione **Experimente** para abrir o [Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do rato nas janelas da shell e selecione **Colar**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    O Azure responde com uma lista de contas de inscrição às quais tem acesso:

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

    Utilize a propriedade `principalName` para identificar a conta à qual pretende conceder acesso de Proprietário de RBAC do Azure. Copie o `name` dessa conta. Por exemplo, se quisesse conceder aceder acesso de Proprietário de RBAC do Azure à SignUpEngineering@contoso.comconta de inscrição, teria de copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Trata-se do ID de objeto da conta de inscrição. Cole este valor algures para que possa utilizá-lo no próximo passo como `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Obtenha o ID de objeto do utilizador ou do grupo ao qual pretende conceder a função de Proprietário de RBAC do Azure

    1. No portal do Azure, pesquise no **Azure Active Directory**.
    1. Se quiser conceder acesso a um utilizador, selecione **Utilizadores** no menu à esquerda. Para conceder acesso a um grupo, selecione **Grupos**.
    1. Selecione o Utilizador ou Grupo ao qual pretende conceder a função de Proprietário de RBAC do Azure.
    1. Se selecionou um Utilizador, encontrará o ID de objeto na página Perfil. Se selecionou um Grupo, o ID do objeto estará na página Descrição Geral. Copie o **ObjectID** ao selecionar o ícone à direita da caixa de texto. Cole-o num local em que possa utilizá-lo no próximo passo como `userObjectId`.

1. Conceder ao utilizador ou ao grupo a função de Proprietário de RBAC do Azure na conta de inscrição

    Através dos valores que recolheu nos dois primeiros passos, conceda ao utilizador ou ao grupo a função de Proprietário de RBAC do Azure na conta de inscrição.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Execute o comando seguinte, ao substituir ```<enrollmentAccountObjectId>``` pelo `name` que copiou no primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pelo ID de objeto que copiou no segundo passo.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função de Proprietário é atribuída com êxito no âmbito da conta de inscrição, o Azure responde com as informações da atribuição de função:

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Execute o seguinte comando [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md), ao substituir ```<enrollmentAccountObjectId>``` pelo `ObjectId` obtido no primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pelo ID de objeto obtido no segundo passo.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-2)

    Execute o seguinte comando [az role assignment create](../../role-based-access-control/role-assignments-cli.md), ao substituir ```<enrollmentAccountObjectId>``` pelo `name` obtido no primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pelo ID de objeto obtido no segundo passo.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Quando um utilizador se torna Proprietário de RBAC do Azure da sua conta de inscrição, aquele pode [criar subscrições através de programação](programmatically-create-subscription-enterprise-agreement.md) na mesma. Uma subscrição criada por um utilizador delegado ainda tem o Proprietário da Conta original como Administrador de Serviço, mas também tem o utilizador delegado como Proprietário de RBAC do Azure por predefinição.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditar quem criou subscrições através de registos de atividades

Para monitorizar as subscrições criadas através desta API, utilize a [API do Registo de Atividades do Inquilino](/rest/api/monitor/tenantactivitylogs). Atualmente, não é possível utilizar o PowerShell, a CLI ou o portal do Azure para monitorizar a criação de subscrições.

1. Como administrador do inquilino do Azure AD, [eleve o acesso](../../role-based-access-control/elevate-access-global-admin.md) e, em seguida, atribua uma função de Leitor ao utilizador de auditoria no âmbito `/providers/microsoft.insights/eventtypes/management`. Esse acesso está disponível na função de [Leitor](../../role-based-access-control/built-in-roles.md#reader), na função [Contribuidor de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ou numa [função personalizada](../../role-based-access-control/custom-roles.md).
1. Como utilizador de auditoria, chame a [API do Registo de Atividades do Inquilino](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação de subscrições. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API a partir da linha de comandos, experimente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Próximos passos

* Agora que a entidade principal de utilizador ou de serviço tem permissão para criar uma subscrição, pode usar essa identidade para [Criar subscrições Enterprise do Azure através de programação](programmatically-create-subscription-enterprise-agreement.md).
* Para ver um exemplo de como criar subscrições com o .NET, veja [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager e as respetivas APIs, veja [Descrição geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Para obter mais informações sobre como gerir grandes números de subscrições com grupos de gestão, veja [Organizar recursos com os grupos de gestão do Azure](../../governance/management-groups/overview.md)
* Para ver uma orientação de melhores práticas abrangente para grandes organizações sobre governação de subscrições, veja [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Estrutura empresarial do Azure – governação de subscrição prescritiva)