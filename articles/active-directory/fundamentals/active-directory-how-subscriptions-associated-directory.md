---
title: Adicione uma subscrição Azure existente ao seu inquilino - Azure AD
description: Instruções sobre como adicionar uma subscrição Azure existente ao seu inquilino Azure Ative (Azure AD).
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594636"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory

Uma subscrição da Azure tem uma relação de confiança com o Azure Ative Directory (Azure AD). Uma subscrição confia no Azure AD para autenticar utilizadores, serviços e dispositivos.

Várias subscrições podem confiar no mesmo diretório AD Azure. Cada subscrição só pode confiar num único diretório.

Uma ou mais subscrições da Azure podem estabelecer uma relação de confiança com um caso de Azure Ative Directory (Azure AD) a fim de autenticar e autorizar os principais e dispositivos de segurança contra os serviços Azure.  Quando uma subscrição expira, a instância fidedigna do serviço Azure AD permanece, mas os principais de segurança perdem o acesso aos recursos da Azure.

Quando um utilizador se inscreve para um serviço de cloud da Microsoft, é criado um novo inquilino AZure AD e o utilizador é nomeado membro da função Ded administrador global. No entanto, quando um proprietário de uma subscrição se junta à sua subscrição a um inquilino existente, o proprietário não é designado para o papel de Administrador Global.

Todos os seus utilizadores têm um único *diretório caseiro* para autenticação. Os seus utilizadores também podem ser convidados em outros diretórios. Você pode ver tanto os diretórios de casa como os convidados para cada utilizador em Azure AD.

> [!Important]
> Quando associa uma subscrição a um diretório diferente, os utilizadores que têm funções atribuídas através do [controlo de acesso baseado em funções Azure](../../role-based-access-control/role-assignments-portal.md) perdem o seu acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
>
> Mover o seu cluster Azure Kubernetes Service (AKS) para uma subscrição diferente, ou mover a subscrição proprietária do cluster para um novo inquilino, faz com que o cluster perca a funcionalidade devido às atribuições de papéis perdidas e aos direitos do diretor de serviço. Para obter mais informações sobre a AKS, consulte [o Serviço Azure Kubernetes (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Antes de começar

Antes de poder associar ou adicionar a sua subscrição, faça as seguintes tarefas:

- Reveja a seguinte lista de alterações que ocorrerão após associar ou adicionar a sua subscrição e como poderá ser afetado:

  - Os utilizadores que tenham sido atribuídos funções usando o Azure RBAC perderão o seu acesso.
  - O Administrador de Serviço e Co-Administrators perderão o acesso.
  - Se tiveres cofres chave, eles ficam inacessíveis e terás de os arranjar depois da associação.
  - Se tiver identidades geridas para recursos como Máquinas Virtuais ou Aplicações Lógicas, deve ree capacitá-las ou recriá-las após a associação.
  - Se tiver um Azure Stack registado, terá de re-registrá-lo após associação.
  - Para obter mais informações, veja [Transferir uma subscrição do Azure para outro diretório do Azure AD](../../role-based-access-control/transfer-subscription.md).

- Inscreva-se usando uma conta que:

  - Tem uma atribuição de função [proprietário](../../role-based-access-control/built-in-roles.md#owner) para a subscrição. Para obter informações sobre como atribuir a função de Proprietário, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).
  - Existe tanto no diretório atual como no novo diretório. O diretório atual está associado à subscrição. Vai associar o novo diretório à assinatura. Para obter mais informações sobre o acesso a outro diretório, consulte [os utilizadores de colaboração do Add Azure Ative Directory B2B no portal Azure](../external-identities/add-users-administrator.md).

- Certifique-se de que não está a utilizar uma subscrição dos Fornecedores de Serviços em Nuvem (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma subscrição da Microsoft Internal (MS-AZR-0015P), ou uma subscrição do Microsoft Azure for Students Starter (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associar uma subscrição a um diretório<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para associar uma subscrição existente ao seu diretório AD Azure, siga estes passos:

1. Faça o súbs e selecione a subscrição que pretende utilizar a partir da [página Subscrições no portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecione **Diretório de Alteração**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Screenshot que mostra a página de Subscrições, com a opção de diretório Change em destaque.":::

1. Reveja quaisquer avisos que apareçam e, em seguida, selecione **Alterar**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Screenshot que mostra a página Change the directy com um diretório de amostra e o botão Mudar realçado.":::

   Depois de o diretório ser alterado para a subscrição, receberá uma mensagem de sucesso.

1. Selecione **os diretórios** da Switch na página de subscrição para ir ao seu novo diretório.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Screenshot que mostra a página do comutador de diretório com informações de amostra.":::

   Pode levar várias horas para que tudo apareça corretamente. Se parecer estar a demorar muito tempo, verifique o **filtro de subscrição Global**. Certifique-se de que a subscrição em 2019 não está escondida. Poderá ter de assinar fora do portal Azure e voltar a entrar para ver o novo diretório.

Mudar o diretório de subscrição é uma operação de nível de serviço, por isso não afeta a propriedade da faturação de subscrição. Para eliminar o diretório original, tem de transferir a propriedade da faturação por subscrição para um novo Administrador de Conta. Para saber mais sobre a transferência de propriedade de faturação, consulte [a propriedade de transferência de uma subscrição do Azure para outra conta.](../../cost-management-billing/manage/billing-subscription-transfer.md)

## <a name="post-association-steps"></a>Etapas pós-associação

Depois de associar uma subscrição a um diretório diferente, poderá ter de fazer as seguintes tarefas para retomar as operações:

- Se tem cofres chave, tem de mudar a identificação do inquilino do cofre. Para obter mais informações, consulte [Change a key vault tenant ID after a subscription move](../../key-vault/general/move-subscription.md).

- Se utilizou identidades geridas atribuídas pelo sistema para recursos, deve ree capacitar estas identidades. Se utilizar identidades geridas atribuídas pelo utilizador, deve recriar estas identidades. Após a re-habilitação ou recriação das Identidades Geridas, deve restabelecer as permissões atribuídas a essas identidades. Para obter mais informações, veja [O que são identidades geridas para os recursos do Azure?](../managed-identities-azure-resources/overview.md).

- Se registou uma Stack Azure usando esta subscrição, deve voltar a registar-se. Para mais informações, consulte [Register Azure Stack Hub com Azure](/azure-stack/operator/azure-stack-registration).

- Para obter mais informações, veja [Transferir uma subscrição do Azure para outro diretório do Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Passos seguintes

- Para criar um novo inquilino AZure AD, consulte [Quickstart: Criar um novo inquilino no Azure Ative Directory](active-directory-access-create-new-tenant.md).

- Para saber mais sobre como o Microsoft Azure controla o acesso a recursos, consulte [as funções de administrador de subscrição Clássica, funções de Azure e administrador ad AZure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Para saber mais sobre como atribuir funções em Azure AD, consulte [funções de administrador e não administrador para utilizadores com Diretório Ativo Azure](active-directory-users-assign-role-azure-portal.md).
