---
title: Adicionar uma assinatura do Azure existente ao seu locatário-Azure AD
description: Instruções sobre como adicionar uma subscrição do Azure existente com o seu inquilino do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8f44e9a1e43da2b9ce6c817898c1722fba715c4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978965"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma subscrição do Azure com o seu inquilino do Azure Active Directory

Uma assinatura do Azure tem uma relação de confiança com Azure Active Directory (Azure AD). Uma assinatura confia no Azure AD para autenticar usuários, serviços e dispositivos.

Várias assinaturas podem confiar no mesmo diretório do AD do Azure. Cada assinatura só pode confiar em um único diretório.

Se a sua subscrição expirar, perderá o acesso a todos os outros recursos associados à subscrição. No entanto, o diretório do AD do Azure permanece no Azure. Você pode associar e gerenciar o diretório usando uma assinatura do Azure diferente.

Todos os seus usuários têm um único diretório *base* para autenticação. Os usuários também podem ser convidados em outros diretórios. Pode ver os dois diretórios raiz e convidados para cada utilizador no Azure AD.

> [!Important]
> Quando você associa uma assinatura a um diretório diferente, os usuários que têm funções atribuídas usando o [controle de acesso baseado em função (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdem seu acesso. Os administradores de assinatura clássicos, incluindo o administrador de serviços e coadministradores, também perdem o acesso.
>
> As atribuições de política também são removidas de uma assinatura quando a assinatura é associada a um diretório diferente.
>
> Mover o cluster AKS (serviço kubernetes do Azure) para uma assinatura diferente ou mover a assinatura proprietária do cluster para um novo locatário faz com que o cluster perca a funcionalidade devido a atribuições de função perdidas e direitos da entidade de serviço. Para obter mais informações sobre AKS, consulte [serviço kubernetes do Azure (AKs)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Antes de começar

Para poder associar ou adicionar sua assinatura, execute as seguintes tarefas:

- Examine a lista de alterações a seguir e como você pode ser afetado:

  - Os usuários aos quais foram atribuídas funções usando o RBAC perderão seu acesso
  - O administrador de serviços e coadministradores perderão o acesso
  - Se você tiver cofres de chaves, eles ficarão inacessíveis e você precisará corrigi-los após a associação
  - Se você tiver identidades gerenciadas para recursos como máquinas virtuais ou aplicativos lógicos, será necessário reabilitá-los ou recriá-los após a associação
  - Se você tiver um Azure Stack registrado, será necessário registrá-lo novamente após a associação

- Inicie sessão com uma conta que:

  - Tem uma atribuição de função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) para a assinatura. Para obter informações sobre como atribuir a função de proprietário, consulte [gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
  - Existe no diretório atual e no novo diretório. O diretório atual está associado à assinatura. Você associará o novo diretório à assinatura. Para obter mais informações sobre como obter acesso a outro diretório, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../b2b/add-users-administrator.md).

- Certifique-se de que não estiver a utilizar uma subscrição de fornecedores de serviços de Cloud do Azure (CSP) (MS-AZR - 0145p, MS - AZR - 0146p, MS - AZR - 159p), uma subscrição do Microsoft Internal (MS-AZR - 0015P) ou uma subscrição Microsoft Imagine (MS-AZR - 0144p).

## Associar uma assinatura a um diretório<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para associar uma assinatura existente ao seu diretório do Azure AD, siga estas etapas:

1. Iniciar sessão e selecione a subscrição que pretende utilizar a partir da [página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecione **altere o diretório**.

    ![Página de subscrições, com a opção de diretório de alteração realçada](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Reveja os avisos que aparecem e, em seguida, selecionam **alteração**.

    ![Alterar a página do diretório, que mostra o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a subscrição e obtém uma mensagem de êxito.

    ![Mensagem de êxito sobre alteração de diretório](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Use o **diretório de comutador** para ir para o novo diretório. Pode levar várias horas para que tudo seja exibido corretamente. Se parecer que está demorando muito, verifique o **filtro de assinatura global**. Verifique se a assinatura movida não está oculta. Talvez seja necessário sair do portal do Azure e entrar novamente para ver o novo diretório.

![Página de seletor de diretório, com informações de exemplo](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Alterar o diretório de subscrição é uma operação de nível de serviço, para que não afeta a propriedade de faturação de subscrição. O administrador de conta pode ainda alterar o administrador de serviço a partir da [Centro de contas](https://account.azure.com/subscriptions). Para excluir o diretório original, você deve transferir a propriedade de cobrança da assinatura para um novo administrador da conta. Para saber mais sobre como transferir a propriedade de cobrança, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Etapas pós-Associação

Depois de associar uma assinatura a um diretório diferente, talvez seja necessário realizar as seguintes tarefas para retomar as operações:

- Se você tiver qualquer cofre de chaves, deverá alterar a ID de locatário do cofre de chaves. Para obter mais informações, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](../../key-vault/key-vault-subscription-move-fix.md).

- Se você usou identidades gerenciadas atribuídas pelo sistema para recursos, você deve reabilitar essas identidades. Se você usou identidades gerenciadas atribuídas pelo usuário, deverá recriar essas identidades. Depois de habilitar novamente ou recriar as identidades gerenciadas, você deve restabelecer as permissões atribuídas a essas identidades. Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

- Se você tiver registrado um Azure Stack usando essa assinatura, deverá registrar novamente. Para obter mais informações, consulte [registrar Azure Stack com o Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Passos seguintes

- Para criar um novo locatário do Azure AD, consulte [início rápido: criar um novo locatário no Azure Active Directory](active-directory-access-create-new-tenant.md).

- Para saber mais sobre como Microsoft Azure controla o acesso aos recursos, consulte [funções de administrador da assinatura clássica, funções do RBAC do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Para saber mais sobre como atribuir funções no Azure AD, consulte [atribuir funções de administrador e não administrador a usuários com Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
