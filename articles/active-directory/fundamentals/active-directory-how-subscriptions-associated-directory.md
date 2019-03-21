---
title: Adicionar uma subscrição do Azure existente ao seu inquilino - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar uma subscrição do Azure existente com o seu inquilino do Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c5cb04c17e508409e67f0441daee4bc44c29d5
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285011"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma subscrição do Azure com o seu inquilino do Azure Active Directory

Uma subscrição do Azure tem uma relação de confiança com o Azure Active Directory (Azure AD), que significa que a subscrição confianças de entidades do Azure AD para autenticar utilizadores, serviços e dispositivos. Várias subscrições podem confiar no mesmo diretório do Azure AD, mas cada subscrição apenas pode confiar num único diretório.

Se a sua subscrição expirar, perderá o acesso a todos os outros recursos associados à subscrição. No entanto, o diretório do Azure AD permanece no Azure, que lhe permite associar e gerir o diretório a utilizar uma subscrição do Azure diferente.

Todos os seus utilizadores com uma única *doméstica* diretório para a autenticação. No entanto, os usuários também podem ser convidados noutros diretórios. Pode ver os dois diretórios raiz e convidados para cada utilizador no Azure AD.

> [!Important]
> Quando associa uma subscrição para um diretório diferente, os utilizadores que têm funções atribuídas usando [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/role-assignments-portal.md) perderão o acesso. Os administradores de subscrição clássica (administrador de serviços e Coadministradores) também irão perder o acesso.

## <a name="before-you-begin"></a>Antes de começar

Antes de poder associar ou adicionar a sua subscrição, tem de efetuar as seguintes tarefas:

1. Reveja a lista seguinte de alterações e como poderá ser afetado:

    - Os utilizadores que foram atribuídos funções utilizando o RBAC perderão o acesso
    - Administrador de serviços e Coadministradores perderão o acesso
    - Se tiver quaisquer cofres de chaves, eles serão inacessíveis e terá que corrigi-los após a associação
    - Se tiver um registados do Azure Stack, terá de voltar a registar após a associação

1. Inicie sessão com uma conta que:
    - Tem um [proprietário](../../role-based-access-control/built-in-roles.md#owner) atribuição de função para a subscrição. Para obter informações sobre como atribuir a função de proprietário, consulte [gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
    - Existe no diretório atual que está associada a subscrição e no novo diretório em que é onde deseja associar a subscrição no futuro. Para obter mais informações sobre como obter acesso a outro diretório, consulte [como os administradores do Azure Active Directory adicionar utilizadores de colaboração B2B?](../b2b/add-users-administrator.md).

1. Certifique-se de que não estiver a utilizar uma subscrição de fornecedores de serviços de Cloud do Azure (CSP) (MS-AZR - 0145p, MS - AZR - 0146p, MS - AZR - 159p), uma subscrição do Microsoft Internal (MS-AZR - 0015P) ou uma subscrição Microsoft Imagine (MS-AZR - 0144p).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Associar uma subscrição existente ao diretório do Azure AD

1. Iniciar sessão e selecione a subscrição que pretende utilizar a partir da [página de subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione **altere o diretório**.

    ![Página de subscrições, com a opção de diretório de alteração realçada](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Reveja os avisos que aparecem e, em seguida, selecionam **alteração**.

    ![Alterar a página do diretório, que mostra o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a subscrição e obtém uma mensagem de êxito.

    ![Mensagem de êxito sobre alterações de diretório](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    
4. Utilize o **alternador de diretório** para aceder ao seu novo diretório. Poderá demorar até 10 minutos para que tudo seja apresentado corretamente.

    ![Página de alternador de diretório, com informações de exemplo](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Alterar o diretório de subscrição é uma operação de nível de serviço, para que não afeta a propriedade de faturação de subscrição. O administrador de conta pode ainda alterar o administrador de serviço a partir da [Centro de contas](https://account.azure.com/subscriptions). Para eliminar o diretório original, tem de transferir a subscrição de faturação a propriedade para um administrador de conta nova. Para obter mais informações sobre como transferir a propriedade da faturação, veja [Transferir a propriedade de uma subscrição do Azure para outra conta](../../billing/billing-subscription-transfer.md). 

## <a name="post-association-steps"></a>Passos de associação de postagem

Depois de associar uma subscrição para um diretório diferente, pode haver passos adicionais que tem de efetuar para retomar as operações.

1. Se tiver quaisquer cofres de chaves, tem de alterar o ID de inquilino do Cofre de chaves. Para obter mais informações, consulte [alterar um ID de inquilino do Cofre de chaves após a movimentação de uma subscrição](../../key-vault/key-vault-subscription-move-fix.md).

1. Se tiver registado um utilizar esta subscrição do Azure Stack, é necessário registrar novamente. Para obter mais informações, consulte [registar o Azure Stack com o Azure](../../azure-stack/azure-stack-registration.md).

## <a name="next-steps"></a>Passos Seguintes

- Para criar um novo do Azure AD de inquilino, consulte [acesso do Azure Active Directory para criar um novo inquilino](active-directory-access-create-new-tenant.md)

- Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte [Noções sobre o acesso aos recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Para saber mais sobre como atribuir funções no Azure AD, veja [como atribuir funções de diretório para os utilizadores com o Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
