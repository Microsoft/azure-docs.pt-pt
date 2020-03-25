---
title: Inscrever-se no Office 365 com a conta do Azure
description: Saiba como criar uma subscrição do Office 365 com uma conta do Azure
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: banders
ms.openlocfilehash: 736009a88f309e0a38402222668a9609f844552f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204822"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Inscreva-se para uma subscrição do Office 365 com a sua conta do Azure
Se for subscritor do Azure, pode usar a sua conta do Azure para se inscrever para uma subscrição do Office 365. Se fizer parte de uma organização que tem uma subscrição do Azure, pode criar subscrições do Office 365 para utilizadores no seu Azure Active Directory existente (AAD). Inscreva-se no Office 365 com uma conta que tenha permissões de Administrador Global ou de Administrador de Faturação no seu inquilino do Azure Active Directory. Para obter mais informações, veja [Verificar as minhas permissões de conta no AAD](#RoleInAzureAD) e [Atribuir funções de administrador no Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Se já tiver uma conta do Office 365 e uma subscrição do Azure, pode [Associar um inquilino do Office 365 a uma subscrição do Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Obtenha uma subscrição do Office 365 com a sua conta do Azure

1. Vá para a [página de produto do Office 365](https://products.office.com/business) e selecione um plano.
2. Clique em **Iniciar sessão** no canto superior direito da página.

    ![captura de ecrã da página de avaliação do Office 365](./media/azure-account-for-office-365-subscription/12-office-365-trial-page.png)
3. Inicie sessão com as suas credenciais de conta do Azure. Se estiver a criar uma subscrição para a sua organização, use uma conta do Azure que seja membro da função de diretório Administrador Global ou Administrador de Faturação no seu inquilino do Azure Active Directory.

    ![Captura de ecrã do início de sessão no Office 365](./media/azure-account-for-office-365-subscription/13-office-365-sign-in.png)
4. Clique em **Experimentar agora**.

    ![Captura de ecrã que confirma o seu pedido para o Office 365.](./media/azure-account-for-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na página do recibo do pedido, clique em **Continuar**.

    ![Captura de ecrã do recibo do pedido do Office 365](./media/azure-account-for-office-365-subscription/15-office-365-order-receipt.png)

Já está tudo pronto.
Se criou a subscrição do Office 365 para a sua organização, use os passos a seguir para verificar se os utilizadores do AAD já estão no Office 365.

1. Abra o centro de administração do Microsoft 365.
2. Expanda **UTILIZADORES** e, em seguida, clique em **Utilizadores ativos**.

    ![Captura de ecrã dos utilizadores no centro de administração do Microsoft 365](./media/azure-account-for-office-365-subscription/16-microsoft-365-admin-center-users.png)

Depois de se inscrever, a subscrição do Office 365 é adicionada à mesma instância do Azure Active Directory à qual a sua subscrição do Azure pertence. Para obter mais informações, veja [Mais sobre as subscrições do Azure e do Office 365](office-365-account-for-azure-subscription.md#more-about-subs) e [Como estão associadas as subscrições do Azure ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Verificar as permissões da minha conta no AAD
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Todos os serviços** e, em seguida, procure **Active Directory**.

    ![Captura de ecrã do Active Directory Domain Services no portal do Azure](./media/azure-account-for-office-365-subscription/billing-more-services-active-directory.png)
3. Clique em **Utilizadores e grupos** > **Todos os utilizadores**.
4. Selecione o nome do utilizador.

    ![Captura de ecrã que mostra os utilizadores do Azure Active Directory](./media/azure-account-for-office-365-subscription/billing-users-groups.png)

5. Clique em **Função de diretório**.

    ![Captura de ecrã que mostra a função de diretório do portal do Azure](./media/azure-account-for-office-365-subscription/billing-user-directory-role.png)
6.  É necessária a função **Administrador Global** ou **Administrador Limitado** > **Administrador de Faturação** para criar uma subscrição do Office 365 para utilizadores no seu Azure Active Directory existente.

    ![Captura de ecrã que mostra a função de diretório Administrador de Faturação do portal do Azure](./media/azure-account-for-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
