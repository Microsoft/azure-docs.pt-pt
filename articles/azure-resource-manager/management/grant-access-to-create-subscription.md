---
title: Conceder acesso para criar assinaturas do Azure Enterprise
description: Saiba como dar a um usuário ou entidade de serviço a capacidade de criar programaticamente assinaturas do Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478880"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)

Como um cliente do Azure no [Enterprise Agreement (ea)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode conceder a outro usuário ou a permissão de entidade de serviço para criar assinaturas faturadas para sua conta. Neste artigo, você aprenderá a usar o [RBAC (controle de acesso baseado em função)](../../active-directory/role-based-access-control-configure.md) para compartilhar a capacidade de criar assinaturas e como auditar as criações de assinaturas. Você deve ter a função de proprietário na conta que deseja compartilhar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar assinaturas em uma conta de registro](programmatically-create-subscription.md), os usuários devem ter a [função de proprietário RBAC](../../role-based-access-control/built-in-roles.md#owner) nessa conta. Você pode conceder a um usuário ou grupo de usuários a função de proprietário RBAC em uma conta de registro seguindo estas etapas:

1. Obter a ID de objeto da conta de registro à qual você deseja conceder acesso

    Para conceder a outros usuários a função de proprietário RBAC em uma conta de registro, você deve ser o proprietário da conta ou um proprietário do RBAC da conta.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Solicitação para listar todas as contas de registro às quais você tem acesso:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    O Azure responde com uma lista de todas as contas de registro às quais você tem acesso:

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

    Use a propriedade `principalName` para identificar a conta à qual você deseja conceder acesso de proprietário do RBAC. Copie o `name` dessa conta. Por exemplo, se você quisesse conceder acesso de proprietário RBAC à conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Essa é a ID de objeto da conta de registro. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa como `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso. Selecione **experimentar** para abrir [Azure cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do mouse nas janelas do Shell e selecione **colar**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    O Azure responde com uma lista de contas de registro às quais você tem acesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Use a propriedade `principalName` para identificar a conta à qual você deseja conceder acesso de proprietário RBAC. Copie o `ObjectId` dessa conta. Por exemplo, se você quisesse conceder acesso de proprietário RBAC à conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole essa ID de objeto em algum lugar para que você possa usá-la na próxima etapa como a `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

    Use o comando [AZ billing Enrollment-Account List](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso. Selecione **experimentar** para abrir [Azure cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do mouse nas janelas do Shell e selecione **colar**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    O Azure responde com uma lista de contas de registro às quais você tem acesso:

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

    Use a propriedade `principalName` para identificar a conta à qual você deseja conceder acesso de proprietário do RBAC. Copie o `name` dessa conta. Por exemplo, se você quisesse conceder acesso de proprietário RBAC à conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Essa é a ID de objeto da conta de registro. Cole esse valor em algum lugar para que você possa usá-lo na próxima etapa como `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Obtenha a ID de objeto do usuário ou grupo ao qual você deseja conceder a função de proprietário RBAC

    1. Na portal do Azure, pesquise **Azure Active Directory**.
    1. Se você quiser conceder acesso a um usuário, clique em **usuários** no menu à esquerda. Se você quiser conceder acesso a um grupo, clique em **grupos**.
    1. Selecione o usuário ou grupo ao qual você deseja conceder a função de proprietário RBAC.
    1. Se você selecionou um usuário, encontrará a ID de objeto na página perfil. Se você selecionou um grupo, a ID do objeto estará na página Visão geral. Copie o **ObjectID** clicando no ícone à direita da caixa de texto. Cole isso em algum lugar para que você possa usá-lo na próxima etapa como `userObjectId`.

1. Conceder ao usuário ou grupo a função de proprietário RBAC na conta de registro

    Usando os valores que você coletou nas duas primeiras etapas, conceda ao usuário ou grupo a função de proprietário RBAC na conta de registro.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Execute o comando a seguir, substituindo ```<enrollmentAccountObjectId>``` pelo `name` que você copiou na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pela ID de objeto que você copiou da segunda etapa.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função de proprietário é atribuída com êxito no escopo da conta de registro, o Azure responde com as informações da atribuição de função:

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

    Execute o seguinte comando [New-AzRoleAssignment](../../active-directory/role-based-access-control-manage-access-powershell.md) , substituindo ```<enrollmentAccountObjectId>``` pelo `ObjectId` coletado na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pela ID de objeto coletada na segunda etapa.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[CLI do Azure](#tab/azure-cli-2)

    Execute o comando [AZ role Assignment Create](../../active-directory/role-based-access-control-manage-access-azure-cli.md) a seguir, substituindo ```<enrollmentAccountObjectId>``` pelo `name` que você copiou na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pela ID de objeto coletada na segunda etapa.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Quando um usuário se torna um proprietário RBAC para sua conta de registro, ele pode [criar inscrições programaticamente](programmatically-create-subscription.md) sob ele. Uma assinatura criada por um usuário delegado ainda tem o proprietário da conta original como administrador de serviços, mas também tem o usuário delegado como um proprietário do RBAC por padrão.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditar quem criou assinaturas usando logs de atividade

Para acompanhar as assinaturas criadas por meio dessa API, use a [API do log de atividades do locatário](/rest/api/monitor/tenantactivitylogs). No momento, não é possível usar o PowerShell, a CLI ou o portal do Azure para rastrear a criação da assinatura.

1. Como administrador do inquilino do Azure AD, [eleve o acesso](../../active-directory/role-based-access-control-tenant-admin-access.md) e, em seguida, atribua uma função de Leitor ao utilizador de auditoria no âmbito `/providers/microsoft.insights/eventtypes/management`.
1. Como o usuário de auditoria, chame a [API do log de atividades do locatário](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação da assinatura. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API a partir da linha de comandos, experimente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passos seguintes

* Agora que o usuário ou a entidade de serviço tem permissão para criar uma assinatura, você pode usar essa identidade para [criar programaticamente assinaturas do Azure Enterprise](programmatically-create-subscription.md).
* Para obter um exemplo de como criar assinaturas usando o .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre Azure Resource Manager e suas APIs, consulte [Azure Resource Manager visão geral](overview.md).
* Para saber mais sobre como gerenciar um grande número de assinaturas usando grupos de gerenciamento, consulte [organizar seus recursos com grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)
* Para ver uma abrangente orientação de práticas recomendadas para grandes organizações sobre governança de assinatura, consulte [Azure Enterprise Scaffold-governança de assinatura prescritiva](/azure/architecture/cloud-adoption-guide/subscription-governance)
