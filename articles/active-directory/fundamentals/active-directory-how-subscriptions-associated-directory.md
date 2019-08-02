---
title: Adicionar uma subscrição do Azure existente ao seu inquilino - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar uma subscrição do Azure existente com o seu inquilino do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a64bad11f5b83ddd7f6d7236ffed4ff4a6e39c2c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561857"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma subscrição do Azure com o seu inquilino do Azure Active Directory

Uma assinatura do Azure tem uma relação de confiança com Azure Active Directory (Azure AD), o que significa que a assinatura confia no Azure AD para autenticar usuários, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório do Azure AD, mas cada subscrição apenas pode confiar num único diretório.

Se a sua subscrição expirar, perderá o acesso a todos os outros recursos associados à subscrição. No entanto, o diretório do Azure AD permanece no Azure, que lhe permite associar e gerir o diretório a utilizar uma subscrição do Azure diferente.

Todos os seus usuários têm um único diretório *base* para autenticação. No entanto, os usuários também podem ser convidados noutros diretórios. Pode ver os dois diretórios raiz e convidados para cada utilizador no Azure AD.

> [!Important]
> Quando você associa uma assinatura a um diretório diferente, os usuários que têm funções atribuídas usando o [controle de acesso baseado em função (RBAC)](../../role-based-access-control/role-assignments-portal.md) perderão seu acesso. Os administradores de assinatura clássicos (administrador de serviços e coadministradores) também perderão o acesso.
> 
> Além disso, mover o cluster AKS (serviço kubernetes do Azure) para uma assinatura diferente ou mover a assinatura proprietária do cluster para um novo locatário faz com que o cluster perca funcionalidade devido a atribuições de função perdidas e direitos de entidades de serviço. Para obter mais informações sobre AKS, consulte [serviço kubernetes do Azure (AKs)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Antes de começar

Antes de poder associar ou adicionar a sua subscrição, tem de efetuar as seguintes tarefas:

1. Examine a lista de alterações a seguir e como você pode ser afetado:

    - Os usuários aos quais foram atribuídas funções usando o RBAC perderão seu acesso
    - O administrador de serviços e coadministradores perderão o acesso
    - Se você tiver cofres de chaves, eles ficarão inacessíveis e você precisará corrigi-los após a associação
    - Se você tiver identidades gerenciadas para recursos como máquinas virtuais ou aplicativos lógicos, precisará reabilitar ou recriá-los após a associação
    - Se você tiver um Azure Stack registrado, será necessário registrá-lo novamente após a associação

1. Inicie sessão com uma conta que:
    - Tem uma atribuição de função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) para a assinatura. Para obter informações sobre como atribuir a função de proprietário, consulte [gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
    - Existe no diretório atual que está associada a subscrição e no novo diretório em que é onde deseja associar a subscrição no futuro. Para obter mais informações sobre como obter acesso a outro diretório, consulte [como os administradores do Azure Active Directory adicionar utilizadores de colaboração B2B?](../b2b/add-users-administrator.md).

1. Certifique-se de que não estiver a utilizar uma subscrição de fornecedores de serviços de Cloud do Azure (CSP) (MS-AZR - 0145p, MS - AZR - 0146p, MS - AZR - 159p), uma subscrição do Microsoft Internal (MS-AZR - 0015P) ou uma subscrição Microsoft Imagine (MS-AZR - 0144p).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Associar uma subscrição existente ao diretório do Azure AD

1. Iniciar sessão e selecione a subscrição que pretende utilizar a partir da [página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione **altere o diretório**.

    ![Página de subscrições, com a opção de diretório de alteração realçada](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Reveja os avisos que aparecem e, em seguida, selecionam **alteração**.

    ![Alterar a página do diretório, que mostra o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a subscrição e obtém uma mensagem de êxito.

    ![Mensagem de êxito sobre alteração de diretório](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Use o **seletor de diretório** para ir para o novo diretório. Pode levar várias horas para que tudo seja exibido corretamente. Se parecer que está demorando muito, verifique o **filtro de assinatura global** para a assinatura movida, para certificar-se de que ele não está simplesmente oculto.

    ![Página de seletor de diretório, com informações de exemplo](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Alterar o diretório de subscrição é uma operação de nível de serviço, para que não afeta a propriedade de faturação de subscrição. O administrador de conta pode ainda alterar o administrador de serviço a partir da [Centro de contas](https://account.azure.com/subscriptions). Para eliminar o diretório original, tem de transferir a subscrição de faturação a propriedade para um administrador de conta nova. Para obter mais informações sobre como transferir a propriedade da faturação, veja [Transferir a propriedade de uma subscrição do Azure para outra conta](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Etapas de associação post
Depois de associar uma assinatura a um diretório diferente, pode haver etapas adicionais que você deve executar para retomar as operações.

1. Se você tiver qualquer cofre de chaves, deverá alterar a ID de locatário do cofre de chaves. Para obter mais informações, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](../../key-vault/key-vault-subscription-move-fix.md).

2. Se você estivesse usando identidades gerenciadas atribuídas pelo sistema para recursos, você deve reabilitá-las. Se você estiver usando identidades gerenciadas atribuídas pelo usuário, deverá recriá-las. Depois de habilitar novamente ou recriar as identidades gerenciadas, você deve restabelecer as permissões atribuídas a essas identidades. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

3. Se você registrou um Azure Stack usando essa assinatura, deverá registrar novamente. Para obter mais informações, consulte [registrar Azure Stack com o Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Passos Seguintes

- Para criar um novo do Azure AD de inquilino, consulte [acesso do Azure Active Directory para criar um novo inquilino](active-directory-access-create-new-tenant.md)

- Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Para saber mais sobre como atribuir funções no Azure AD, veja [como atribuir funções de diretório para os utilizadores com o Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
