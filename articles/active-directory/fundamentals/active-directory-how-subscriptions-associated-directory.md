---
title: Adicione uma subscrição Azure existente ao seu inquilino - Azure AD
description: Instruções sobre como adicionar uma assinatura Azure existente ao seu inquilino Azure Ative Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2737da028d8fea1c31b728c8e9864ad421d08ec
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309620"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory

Uma subscrição da Azure tem uma relação de confiança com o Azure Ative Directory (Azure AD). Uma subscrição confia no Azure AD para autenticar utilizadores, serviços e dispositivos.

Várias subscrições podem confiar no mesmo diretório AD Azure. Cada subscrição só pode confiar num único diretório.

Se a sua subscrição expirar, perde o acesso a todos os outros recursos associados à subscrição. No entanto, o diretório AD Azure permanece em Azure. Pode associar e gerir o diretório utilizando uma subscrição Azure diferente.

Todos os seus utilizadores têm um único *diretório caseiro* para autenticação. Os seus utilizadores também podem ser convidados em outros diretórios. Você pode ver tanto os diretórios de casa como os convidados para cada utilizador em Azure AD.

> [!Important]
> Quando associa uma subscrição a um diretório diferente, os utilizadores que têm funções atribuídas usando [o controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdem o seu acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
>
> As Atribuições de Políticas também são removidas de uma subscrição quando a subscrição é associada a um diretório diferente.
>
> Mover o seu cluster Azure Kubernetes Service (AKS) para uma subscrição diferente, ou mover a subscrição proprietária do cluster para um novo inquilino, faz com que o cluster perca a funcionalidade devido às atribuições de papéis perdidas e aos direitos do diretor de serviço. Para obter mais informações sobre a AKS, consulte [o Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Antes de começar

Antes de poder associar ou adicionar a sua subscrição, faça as seguintes tarefas:

- Reveja a seguinte lista de alterações que ocorrerão após associar ou adicionar a sua subscrição e como poderá ser afetado:

  - Os utilizadores que tenham sido atribuídos funções usando o RBAC perderão o seu acesso
  - Administrador de Serviço e Coadministradores perderão acesso
  - Se tiveres cofres chave, eles ficarão inacessíveis e terás de os arranjar depois da associação.
  - Se tiver identidades geridas para recursos como Máquinas Virtuais ou Aplicações Lógicas, deve ree capacitá-las ou recriá-las após a associação
  - Se tiver um Azure Stack registado, terá de re-registrá-lo após associação

- Inscreva-se usando uma conta que:

  - Tem uma atribuição de função [proprietário](../../role-based-access-control/built-in-roles.md#owner) para a subscrição. Para obter informações sobre como atribuir a função de Proprietário, consulte Gerir o [acesso aos recursos do Azure utilizando o RBAC e o portal Azure.](../../role-based-access-control/role-assignments-portal.md)
  - Existe tanto no diretório atual como no novo diretório. O diretório atual está associado à subscrição. Vai associar o novo diretório à assinatura. Para obter mais informações sobre o acesso a outro diretório, consulte [os utilizadores de colaboração do Add Azure Ative Directory B2B no portal Azure](../b2b/add-users-administrator.md).

- Certifique-se de que não está a utilizar uma subscrição dos Fornecedores de Serviços em Nuvem (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma subscrição da Microsoft Internal (MS-AZR-0015P), ou uma subscrição da Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associar uma subscrição a um diretório<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para associar uma subscrição existente ao seu diretório AD Azure, siga estes passos:

1. Faça o súbs e selecione a subscrição que pretende utilizar a partir da [página Subscrições no portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecione **Diretório de Alteração**.

    ![Página de subscrições, com opção de diretório change em destaque](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Reveja quaisquer avisos que apareçam e, em seguida, selecione **Alterar**.

    ![Alterar a página do diretório, mostrando o diretório a mudar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Depois de o diretório ser alterado para a subscrição, receberá uma mensagem de sucesso.

  1. Selecione **os diretórios** da Switch na página de subscrição para ir ao seu novo diretório. 

      ![Página do comutador de diretório, com informações de amostra](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

      Pode levar várias horas para que tudo apareça corretamente. Se parecer estar a demorar muito tempo, verifique o **filtro de subscrição Global**. Certifique-se de que a subscrição em 2019 não está escondida. Poderá ter de assinar fora do portal Azure e voltar a entrar para ver o novo diretório.

Mudar o diretório de subscrição é uma operação de nível de serviço, por isso não afeta a propriedade da faturação de subscrição. O Administrador de Conta ainda pode alterar o Administrador de Serviço do Centro de [Contas.](https://account.azure.com/subscriptions) Para eliminar o diretório original, tem de transferir a propriedade da faturação por subscrição para um novo Administrador de Conta. Para saber mais sobre a transferência de propriedade de faturação, consulte [a propriedade de transferência de uma subscrição do Azure para outra conta.](../../cost-management-billing/manage/billing-subscription-transfer.md)

## <a name="post-association-steps"></a>Etapas pós-associação

Depois de associar uma subscrição a um diretório diferente, poderá ter de fazer as seguintes tarefas para retomar as operações:

- Se tem cofres chave, tem de mudar a identificação do inquilino do cofre. Para obter mais informações, consulte [Change a key vault tenant ID after a subscription move](../../key-vault/general/subscription-move-fix.md).

- Se utilizou identidades geridas atribuídas pelo sistema para recursos, deve ree capacitar estas identidades. Se utilizar identidades geridas atribuídas pelo utilizador, deve recriar estas identidades. Após a re-habilitação ou recriação das Identidades Geridas, deve restabelecer as permissões atribuídas a essas identidades. Para mais informações, veja [o que são identidades geridas para os recursos da Azure?](../managed-identities-azure-resources/overview.md)

- Se registou uma Stack Azure usando esta subscrição, deve voltar a registar-se. Para mais informações, consulte [o Register Azure Stack com Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Passos seguintes

- Para criar um novo inquilino AZure AD, consulte [Quickstart: Criar um novo inquilino no Azure Ative Directory](active-directory-access-create-new-tenant.md).

- Para saber mais sobre como o Microsoft Azure controla o acesso a recursos, consulte [as funções de administrador de subscrição Clássica, funções de administrador do Azure RBAC e funções de administrador de AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Para saber mais sobre como atribuir funções em Azure AD, consulte [funções de administrador e não administrador para utilizadores com Diretório Ativo Azure](active-directory-users-assign-role-azure-portal.md).
