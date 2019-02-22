---
title: Como criar uma função de registo para o Azure Stack
description: Como criar uma função personalizada para evitar o uso de administrador global para o registo.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 4858e33023ff0f94bb9fb879b01846184df2491e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652149"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Criar uma função de registo para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Para cenários em que não pretende conceder permissões de proprietário na subscrição do Azure, pode criar uma função personalizada para atribuir permissões a uma conta de utilizador para registar o Azure Stack.

> [!WARNING]
> Não é uma funcionalidade da postura de segurança. Utilizá-lo em cenários onde pretende que as restrições para impedir alterações acidentais à subscrição do Azure. Quando um utilizador estiver direitos delegados para esta função personalizada, o utilizador tem direitos para editar as permissões e efetuar a elevação de direitos. Atribua apenas os utilizadores de que confiança para a função personalizada.

Ao registar o Azure Stack, a conta de registo requer as seguintes permissões do Azure Active Directory e permissões de subscrição do Azure:

* **Permissões de registo de aplicação no seu inquilino do Azure Active Directory:** Os administradores têm permissões de registo de aplicação. A permissão para usuários é uma definição global para todos os utilizadores no inquilino. Para ver ou alterar a definição, consulte [criar um Azure AD principal de aplicações e serviço que pode aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    O *utilizadores podem registar aplicações* definição tem de ser definida como **Sim** para a ativação de uma conta de utilizador para registar o Azure Stack. Se a definição dos registos da aplicação estiver definido como **não**, não é possível utilizar uma conta de utilizador e tem de utilizar uma conta de administrador global para registar o Azure Stack.

* **Um conjunto de permissões suficientes de subscrição do Azure:** Os utilizadores no grupo de proprietários tem permissões suficientes. Para outras contas, pode atribuir a permissão definida ao atribuir uma função personalizada, conforme descrito nas seções a seguir.

## <a name="create-a-custom-role-using-powershell"></a>Criar uma função personalizada com o PowerShell

Para criar uma função personalizada, tem de ter o `Microsoft.Authorization/roleDefinitions/write` permissão em todos os `AssignableScopes`, tal como [proprietário](../role-based-access-control/built-in-roles.md#owner) ou [administrador de acesso de utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator). Utilize o seguinte modelo JSON para simplificar a definir a função personalizada. O modelo cria uma função personalizada que permite a leitura necessária e o acesso de escrita para o registo do Azure Stack.

1. Crie um ficheiro JSON. Por exemplo,  `C:\CustomRoles\registrationrole.json`
2. Adicione o seguinte JSON ao ficheiro. Substitua <SubscriptionID> pelo seu ID da subscrição do Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. No PowerShell, ligue ao Azure para utilizar o Azure Resource Manager. Quando lhe for pedido, autenticar utilizando uma conta com permissões suficientes, tais como [proprietário](../role-based-access-control/built-in-roles.md#owner) ou [administrador de acesso de utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Para adicionar a função à subscrição, utilize **New-AzureRmRoleDefinition** especificando o ficheiro de modelo JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Atribuir um utilizador a função de registo

Depois da função personalizada do registo é criada, atribua funções de utilizador a registar o Azure Stack.

1. Inicie sessão com a conta com permissões suficientes na subscrição do Azure para delegar direitos - por exemplo, [proprietário](../role-based-access-control/built-in-roles.md#owner) ou [administrador de acesso de utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) .
2. Na **subscrições**, selecione **controlo de acesso (IAM) > Adicionar atribuição de função**.
3. Na **função**, selecione a função personalizada que criou *função de registo do Azure Stack*.
4. Selecione os utilizadores que pretende atribuir à função.
5. Selecione **guardar** para atribuir os utilizadores selecionados à função.

    ![Selecione os utilizadores para atribuir a função](media/azure-stack-registration-role/assign-role.png)

Para obter mais informações sobre como utilizar funções personalizadas, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

[Registar o Azure Stack com o Azure](azure-stack-registration.md)
