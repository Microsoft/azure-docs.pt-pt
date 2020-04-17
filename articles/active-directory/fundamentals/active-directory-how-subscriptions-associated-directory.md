---
title: Adicione uma subscrição Azure existente ao seu inquilino - Azure AD
description: Instruções sobre como adicionar uma subscrição Azure existente ao seu inquilino do Diretório Ativo Azure.
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
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457931"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory

Uma subscrição azure tem uma relação de confiança com o Azure Ative Directory (Azure AD). Uma subscrição confia na Azure AD para autenticar utilizadores, serviços e dispositivos.

Várias subscrições podem confiar no mesmo diretório Azure AD. Cada subscrição só pode confiar num único diretório.

Se a sua subscrição expirar, perde acesso a todos os outros recursos associados à subscrição. No entanto, o diretório Azure AD permanece em Azure. Pode associar e gerir o diretório utilizando uma subscrição Azure diferente.

Todos os seus utilizadores têm um único diretório *para* autenticação. Os seus utilizadores também podem ser convidados de outras direções. Pode ver os diretórios de casa e de hóspedes para cada utilizador em Azure AD.

> [!Important]
> Quando associa uma subscrição a um diretório diferente, os utilizadores que possuem funções atribuídas utilizando [o controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdem o seu acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
>
> As Atribuições de Políticas também são removidas de uma subscrição quando a subscrição é associada a um diretório diferente.
>
> Mover o seu cluster Azure Kubernetes Service (AKS) para uma subscrição diferente, ou mover a subscrição proprietária do cluster para um novo inquilino, faz com que o cluster perca a funcionalidade devido a atribuições de funções perdidas e direitos do diretor de serviço. Para mais informações sobre a AKS, consulte [o Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Antes de começar

Antes de poder associar ou adicionar a sua subscrição, faça as seguintes tarefas:

- Reveja a seguinte lista de alterações e como poderá ser afetado:

  - Os utilizadores que tenham sido atribuídos funções usando o RBAC perderão o seu acesso
  - Administrador de Serviços e Coadministradores perderão acesso
  - Se tiver cofres chave, eles serão inacessíveis e terá que consertá-los após associação.
  - Se tiver alguma identidade gerida para recursos como Máquinas Virtuais ou Aplicações Lógicas, deve reativar ou recriá-las após a associação
  - Se tiver um Azure Stack registado, terá de o reregistar após associação

- Inscreva-se usando uma conta que:

  - Tem uma atribuição de função [de proprietário](../../role-based-access-control/built-in-roles.md#owner) para a subscrição. Para obter informações sobre como atribuir a função Proprietário, consulte [Gerir o acesso aos recursos do Azure utilizando o RBAC e o portal Azure.](../../role-based-access-control/role-assignments-portal.md)
  - Existe tanto no atual diretório como no novo diretório. O atual diretório está associado à subscrição. Associará o novo diretório à subscrição. Para obter mais informações sobre o acesso a outro diretório, consulte [add Azure Ative Directory B2B no portal Azure](../b2b/add-users-administrator.md).

- Certifique-se de que não está a utilizar uma subscrição de Fornecedores de Serviços em Nuvem Azure (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma subscrição Interna da Microsoft (MS-AZR-0015P) ou uma subscrição da Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associar uma subscrição a um diretório<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para associar uma subscrição existente ao seu diretório Azure AD, siga estes passos:

1. Faça o sessão e selecione a subscrição que pretende utilizar na [página de Subscrições no portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecione **Diretório de Alteração**.

    ![Página de assinaturas, com opção de diretório Alterar em destaque](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Reveja quaisquer avisos que apareçam e, em seguida, selecione **Alterar**.

    ![Mude a página de diretório, mostrando o diretório a mudar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a subscrição e recebe uma mensagem de sucesso.

    ![Mensagem de sucesso sobre mudança de diretório](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Use o **diretório da Switch** para ir ao seu novo diretório. Pode levar várias horas para que tudo apareça corretamente. Se parece estar a demorar muito tempo, verifique o filtro de **subscrição Global.** Certifique-se de que a subscrição em movido não está escondida. Poderá ter de assinar o portal Azure e voltar a entrar para ver o novo diretório.

![Página do comutador de diretório, com informações de amostra](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Mudar o diretório de subscrição é uma operação de nível de serviço, por isso não afeta a propriedade da faturação de assinaturas. O Administrador de Conta ainda pode alterar o Administrador de Serviço do Centro de [Conta.](https://account.azure.com/subscriptions) Para eliminar o diretório original, deve transferir a propriedade de faturação de subscrição para um novo Administrador de Conta. Para saber mais sobre a transferência de propriedade de faturação, consulte a [propriedade da Transferência de uma subscrição Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Passos pós-associação

Depois de associar uma subscrição a um diretório diferente, poderá ter de fazer as seguintes tarefas para retomar as operações:

- Se tiver cofres chave, tem de mudar a identificação do inquilino do cofre. Para mais informações, consulte Alterar um ID de [inquilino chave cofre após um movimento de subscrição](../../key-vault/general/subscription-move-fix.md).

- Se utilizou identidades geridas atribuídas pelo sistema para recursos, deve reativar essas identidades. Se utilizou identidades geridas atribuídas ao utilizador, tem de recriar estas identidades. Depois de reativar ou recriar as Identidades Geridas, deve restabelecer as permissões atribuídas a essas identidades. Para mais informações, veja [o que é gerida identidades para os recursos do Azure?](../managed-identities-azure-resources/overview.md)

- Se registou um Azure Stack utilizando esta subscrição, tem de voltar a registar-se. Para mais informações, consulte [Register Azure Stack com Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Passos seguintes

- Para criar um novo inquilino da Azure AD, consulte [Quickstart: Crie um novo inquilino no Azure Ative Directory.](active-directory-access-create-new-tenant.md)

- Para saber mais sobre como o Microsoft Azure controla o acesso aos recursos, consulte funções de administrador de [subscrição Classic, funções de Azure RBAC e funções de administrador da AD Azure.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Para saber mais sobre como atribuir funções em Azure AD, consulte funções de [administrador e não administrador para utilizadores com Diretório Ativo Azure](active-directory-users-assign-role-azure-portal.md).
