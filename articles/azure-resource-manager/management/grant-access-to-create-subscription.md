---
title: Conceder acesso para criar subscrições da Azure Enterprise
description: Saiba como dar a um utilizador ou ao principal de serviço a capacidade de criar programáticamente subscrições da Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 7feb49266a10b7423121dc5362b0bd6bda4d0e08
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824499"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar subscrições da Azure Enterprise (pré-visualização)

Como cliente da Azure no [Enterprise Agreement (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/)pode dar a outro utilizador ou principal de serviço permissão para criar subscrições faturadas na sua conta. Neste artigo, aprende-se a usar o [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) para partilhar a capacidade de criar subscrições e como auditar criações de subscrições. Deve ter o papel de Proprietário na conta que pretende partilhar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar subscrições numa conta de inscrição,](programmatically-create-subscription.md)os utilizadores devem ter a [função de Proprietário do RBAC](../../role-based-access-control/built-in-roles.md#owner) nessa conta. Pode conceder a um utilizador ou a um grupo de utilizadores a função rbac owner numa conta de inscrição seguindo estes passos:

1. Obtenha o iD do objeto da conta de inscrição a que pretende conceder acesso

    Para conceder a outros o papel de Proprietário do RBAC numa conta de inscrição, você deve ser o Proprietário da Conta ou um Proprietário da conta RBAC.

    # <a name="rest"></a>[REST](#tab/rest)

    Solicitação para listar todas as contas de inscrição a que tenha acesso:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    A azure responde com uma lista de todas as contas de inscrição a que tem acesso:

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

    Utilize a `principalName` propriedade para identificar a conta a que pretende conceder acesso ao RBAC Owner. Copie a `name` conta. Por exemplo, se quisesse conceder ao RBAC Acesso à SignUpEngineering@contoso.com conta de inscrição, ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` copiaria. Esta é a identificação do objeto da conta de inscrição. Cole este valor em algum lugar para que possa usá-lo no próximo passo como `enrollmentAccountObjectId` .

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Utilize o [Cmdlet Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de inscrição a que tenha acesso. Selecione **Experimente-o** para abrir [a Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito nas janelas da concha e na **pasta**selecionada .

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    A azure responde com uma lista de contas de inscrição a que tem acesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Utilize a `principalName` propriedade para identificar a conta a que pretende conceder acesso ao RBAC Owner. Copie a `ObjectId` conta. Por exemplo, se quisesse conceder ao RBAC Acesso à SignUpEngineering@contoso.com conta de inscrição, ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` copiaria. Cole este objeto de identificação em algum lugar para que possa usá-lo no próximo passo como `enrollmentAccountObjectId` .

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    Utilize o comando [da lista de inscrições de faturação az](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de inscrição a que tenha acesso. Selecione **Experimente-o** para abrir [a Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito nas janelas da concha e na **pasta**selecionada .

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    A azure responde com uma lista de contas de inscrição a que tem acesso:

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

    Utilize a `principalName` propriedade para identificar a conta a que pretende conceder acesso ao RBAC Owner. Copie a `name` conta. Por exemplo, se quisesse conceder ao RBAC Acesso à SignUpEngineering@contoso.com conta de inscrição, ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` copiaria. Esta é a identificação do objeto da conta de inscrição. Cole este valor em algum lugar para que possa usá-lo no próximo passo como `enrollmentAccountObjectId` .

1. <a id="userObjectId"></a>Obtenha o ID do objeto do utilizador ou grupo que deseja dar o papel do Proprietário RBAC para

    1. No portal Azure, procure no **Diretório Ativo Azure**.
    1. Se pretender conceder acesso a um utilizador, clique nos **Utilizadores** no menu à esquerda. Se quiser conceder acesso a um grupo, clique em **Grupos**.
    1. Selecione o Utilizador ou Grupo a quem pretende dar a função de Proprietário RBAC.
    1. Se selecionar um Utilizador, encontrará o ID do objeto na página 'Perfil'. Se selecionar um Grupo, o ID do objeto estará na página 'Vista Geral'. Copie o **ObjectID** clicando no ícone à direita da caixa de texto. Cole isto em algum lugar para que possa usá-lo no próximo passo como `userObjectId` .

1. Conceder ao utilizador ou grupo o papel de Proprietário do RBAC na conta de inscrição

    Utilizando os valores recolhidos nos dois primeiros passos, conceda ao utilizador ou grupo o papel de Proprietário RBAC na conta de inscrição.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Executar o seguinte comando, substituindo ```<enrollmentAccountObjectId>``` pelo `name` que copiou no primeiro passo ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` (). ```<userObjectId>```Substitua-o pelo ID do objeto que copiou a partir do segundo passo.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função Proprietário é atribuída com sucesso no âmbito da conta de inscrição, a Azure responde com informações sobre a atribuição de funções:

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

    Executar o seguinte comando [de Assinatura New-AzRole,](../../role-based-access-control/role-assignments-powershell.md) substituindo-o ```<enrollmentAccountObjectId>``` pelo recolhido no primeiro passo ( `ObjectId` ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ). ```<userObjectId>```Substitua-o pelo ID do objeto recolhido no segundo passo.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-2)

    Executar a [seguinte atribuição de função az criar](../../role-based-access-control/role-assignments-cli.md) comando, substituindo-o ```<enrollmentAccountObjectId>``` `name` copiado no primeiro passo ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` (). ```<userObjectId>```Substitua-o pelo ID do objeto recolhido no segundo passo.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Uma vez que um utilizador se torne um RbaC Owner para a sua conta de inscrição, eles podem [criar programáticamente subscrições](programmatically-create-subscription.md) sob ela. Uma subscrição criada por um utilizador delegado ainda tem o Titular original da Conta como Administrador de Serviço, mas também tem o utilizador delegado como Proprietário do RBAC por padrão.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditoria que criou subscrições usando registos de atividades

Para acompanhar as subscrições criadas através desta API, utilize a API de Registo de Atividades do [Arrendatário.](/rest/api/monitor/tenantactivitylogs) Atualmente não é possível usar o portal PowerShell, CLI ou Azure para acompanhar a criação de subscrições.

1. Como administrador do inquilino do Azure AD, [eleve o acesso](../../role-based-access-control/elevate-access-global-admin.md) e, em seguida, atribua uma função de Leitor ao utilizador de auditoria no âmbito `/providers/microsoft.insights/eventtypes/management`.
1. Como utilizador de auditoria, ligue para a API de [Registo de Atividades](/rest/api/monitor/tenantactivitylogs) do Inquilino para ver as atividades de criação de assinaturas. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API a partir da linha de comandos, experimente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passos seguintes

* Agora que o utilizador ou o principal de serviço tem permissão para criar uma subscrição, pode usar essa identidade para [criar programáticamente subscrições da Azure Enterprise](programmatically-create-subscription.md).
* Para um exemplo sobre a criação de subscrições utilizando .NET, consulte [o código de amostra no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager e as suas APIs, consulte [a visão geral do Azure Resource Manager](overview.md).
* Para saber mais sobre a gestão de um grande número de subscrições utilizando grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure](../../governance/management-groups/overview.md)
* Para ver uma orientação abrangente de boas práticas para grandes organizações sobre a governação de subscrições, consulte [o andaime da empresa Azure - governação prescritiva de assinaturas](/azure/architecture/cloud-adoption-guide/subscription-governance)
