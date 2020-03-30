---
title: Conceder acesso à criação de subscrições da Azure Enterprise
description: Aprenda a dar a um utilizador ou principal de serviço a capacidade de criar programaticamente subscrições da Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478880"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso à criação de subscrições da Azure Enterprise (pré-visualização)

Como cliente Azure no [Enterprise Agreement (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/)pode dar a outro utilizador ou serviço permissão principal para criar subscrições faturadas na sua conta. Neste artigo, aprende-se a utilizar o Controlo de [Acesso baseado em Funções (RBAC)](../../active-directory/role-based-access-control-configure.md) para partilhar a capacidade de criar subscrições e como auditar as criações de subscrição. Deve ter o papel de Proprietário na conta que deseja partilhar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar subscrições sob uma conta de inscrição,](programmatically-create-subscription.md)os utilizadores devem ter a [função de Proprietário RBAC](../../role-based-access-control/built-in-roles.md#owner) nessa conta. Pode conceder a um utilizador ou a um grupo de utilizadores a função RBAC Owner numa conta de inscrição seguindo estas etapas:

1. Obtenha a identificação do objeto da conta de inscrição a que pretende conceder acesso

    Para conceder a outros o papel de Proprietário RBAC numa conta de inscrição, deve ser o Proprietário da Conta ou um Proprietário RBAC da conta.

    # <a name="rest"></a>[REST](#tab/rest)

    Pedido para listar todas as contas de inscrição a que tem acesso:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    O Azure responde com uma lista de todas as contas de inscrição a que tem acesso:

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

    Utilize `principalName` a propriedade para identificar a conta a que pretende conceder acesso ao Proprietário RBAC. Copie `name` a conta. Por exemplo, se quisesse conceder ao Dono rBAC acesso SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```à conta de inscrição, copiaria . Esta é a identificação do objeto da conta de inscrição. Colar este valor em algum lugar para que possa `enrollmentAccountObjectId`usá-lo no próximo passo como .

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Utilize o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de inscrição a que tem acesso. Selecione **Experimente** para abrir a [Casca de Nuvem Azure](https://shell.azure.com/). Para colar o código, clique à direita nas janelas da concha e na **pasta**selecionada .

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    O Azure responde com uma lista de contas de inscrição a que tem acesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Utilize `principalName` a propriedade para identificar a conta a que pretende conceder acesso ao Proprietário RBAC. Copie `ObjectId` a conta. Por exemplo, se quisesse conceder ao Dono rBAC acesso SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```à conta de inscrição, copiaria . Repasse esta identificação do objeto em algum lugar para `enrollmentAccountObjectId`que possa usá-lo no próximo passo como .

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Utilize o comando da [lista de listas de inscrição az](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de inscrição a que tem acesso. Selecione **Experimente** para abrir a [Casca de Nuvem Azure](https://shell.azure.com/). Para colar o código, clique à direita nas janelas da concha e na **pasta**selecionada .

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    O Azure responde com uma lista de contas de inscrição a que tem acesso:

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

    Utilize `principalName` a propriedade para identificar a conta a que pretende conceder acesso ao Proprietário RBAC. Copie `name` a conta. Por exemplo, se quisesse conceder ao Dono rBAC acesso SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```à conta de inscrição, copiaria . Esta é a identificação do objeto da conta de inscrição. Colar este valor em algum lugar para que possa `enrollmentAccountObjectId`usá-lo no próximo passo como .

1. <a id="userObjectId"></a>Obtenha identificação do objeto do utilizador ou grupo que pretende dar ao Proprietário RBAC para

    1. No portal Azure, procure no **Azure Ative Directory.**
    1. Se pretender conceder acesso ao utilizador, clique em **Utilizadores** no menu à esquerda. Se quiser conceder acesso a um grupo, clique em **Grupos**.
    1. Selecione o Utilizador ou Grupo a que pretende dar a função de Proprietário RBAC.
    1. Se selecionar um Utilizador, encontrará o ID do objeto na página perfil. Se selecionar um Grupo, o ID do objeto estará na página 'Visão Geral'. Copie o **ObjectID** clicando no ícone à direita da caixa de texto. Cola isto em algum lugar para que possa `userObjectId`usá-lo no próximo passo como .

1. Conceda ao utilizador ou grupo o papel de Proprietário RBAC na conta de inscrição

    Utilizando os valores recolhidos nos dois primeiros passos, conceda ao utilizador ou grupo o papel do Proprietário RBAC na conta de inscrição.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Executar o seguinte comando, ```<enrollmentAccountObjectId>``` `name` substituindo pelo copiado```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```no primeiro passo ( ). Substitua ```<userObjectId>``` com o ID do objeto copiado do segundo passo.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função proprietário é atribuída com sucesso no âmbito da conta de inscrição, o Azure responde com informações sobre a atribuição de funções:

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

    Executar o seguinte comando [New-AzRoleAssignment,](../../active-directory/role-based-access-control-manage-access-powershell.md) substituindo-o ```<enrollmentAccountObjectId>``` `ObjectId` ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```pelo recolhido no primeiro passo ( ). Substitua-a ```<userObjectId>``` pelo ID do objeto recolhido no segundo passo.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Executar a seguinte atribuição de [funções az criar](../../active-directory/role-based-access-control-manage-access-azure-cli.md) comando, ```<enrollmentAccountObjectId>``` substituindo pelo `name` copiado no primeiro passo (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua-a ```<userObjectId>``` pelo ID do objeto recolhido no segundo passo.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Uma vez que um utilizador se torne um Proprietário RBAC para a sua conta de inscrição, eles podem [criar programáticamente subscrições](programmatically-create-subscription.md) sob a sua. Uma subscrição criada por um utilizador delegado ainda tem o Proprietário da Conta original como Administrador de Serviço, mas também tem o utilizador delegado como Proprietário RBAC por padrão.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditoria que criou subscrições usando registos de atividade

Para acompanhar as subscrições criadas através desta API, utilize a API de Registo de Atividades do [Arrendatário](/rest/api/monitor/tenantactivitylogs). Atualmente não é possível usar powerShell, CLI ou portal Azure para acompanhar a criação de subscrição.

1. Como administrador do inquilino do Azure AD, [eleve o acesso](../../active-directory/role-based-access-control-tenant-admin-access.md) e, em seguida, atribua uma função de Leitor ao utilizador de auditoria no âmbito `/providers/microsoft.insights/eventtypes/management`.
1. Como utilizador de auditoria, ligue para a [API](/rest/api/monitor/tenantactivitylogs) de Registo de Atividade do Inquilino para ver as atividades de criação de subscrição. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API a partir da linha de comandos, experimente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passos seguintes

* Agora que o utilizador ou diretor de serviço tem permissão para criar uma subscrição, pode utilizar essa identidade para [criar programaticamente subscrições da Azure Enterprise.](programmatically-create-subscription.md)
* Para um exemplo sobre a criação de subscrições usando .NET, consulte [o código de amostra no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Gestor de Recursos Azure e as suas APIs, consulte a [visão geral do Gestor de Recursos do Azure.](overview.md)
* Para saber mais sobre a gestão de um grande número de subscrições usando grupos de gestão, consulte Organizar os seus recursos com grupos de [gestão Azure](../../governance/management-groups/overview.md)
* Para ver uma orientação abrangente de boas práticas para as grandes organizações na governação de subscrições, consulte [o andaime da empresa Azure - governação prescritiva de subscrição](/azure/architecture/cloud-adoption-guide/subscription-governance)
