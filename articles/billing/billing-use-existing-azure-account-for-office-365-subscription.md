---
title: Inscreva-se para o Office 365 com a conta do Azure | Documentos da Microsoft
description: Saiba como criar uma subscrição do Office 365, utilizando uma conta do Azure
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 5ac05991bc79dcfd24a527a13084b3a9383b4839
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903998"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Inscreva-se para uma subscrição do Office 365 com sua conta do Azure
Se for assinante do Azure, pode utilizar a sua conta do Azure para se inscrever numa subscrição do Office 365. Se for parte de uma organização que tenha uma subscrição do Azure, pode criar subscrições do Office 365 para os utilizadores no existente do Azure Active Directory (Azure AD). Inscreva-se ao Office 365 através de uma conta que tenha permissões de Administrador Global ou administrador de faturação no seu inquilino do Azure Active Directory. Para obter mais informações, consulte [verificar as minhas permissões de conta no Azure AD](#RoleInAzureAD) e [atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Se já tiver uma conta do Office 365 e uma subscrição do Azure, pode [associar um inquilino do Office 365 para uma subscrição do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Obtenha uma subscrição do Office 365, utilizando a sua conta do Azure

1. Vá para o [página de produto do Office 365](https://products.office.com/business)e selecione um plano.
2. Clique em **iniciar sessão** no canto superior direito da página.

    ![captura de ecrã da página de avaliação do Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Inicie sessão com as credenciais da conta do Azure. Se estiver a criar uma subscrição para a sua organização, utilize uma conta do Azure que seja membro da função de diretório do Administrador Global ou administrador de faturação no seu inquilino do Azure Active Directory.

    ![Captura de ecrã do Office 365 início de sessão](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Clique em **Experimente agora o**.

    ![Captura de ecrã que confirme o seu pedido para o Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Na página de recibo do pedido, clique em **continuar**.

    ![Captura de ecrã do recibo do pedido do Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Agora está tudo pronto. Se tiver criado a subscrição do Office 365 para a sua organização, utilize os seguintes passos para verificar que os utilizadores do Azure AD estão agora no Office 365.

1. Abra o Centro de administração do Office 365.
2. Expanda **USUÁRIOS**e, em seguida, clique em **utilizadores ativos**.

    ![Captura de ecrã dos utilizadores de centro de administração do Office 365](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

Depois de se inscrever, a subscrição do Office 365 é adicionada para a mesma instância do Azure Active Directory que pertence a sua subscrição do Azure. Para obter mais informações, consulte [mais sobre as subscrições do Azure e o Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) e [subscrições do Azure como estão associadas com o Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Verifique as minhas permissões de conta no Azure AD
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **todos os serviços**e, em seguida, procure **do Active Directory**.

    ![Captura de ecrã do Active Directory no portal do Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Clique em **utilizadores e grupos** > **todos os utilizadores**.
4. Selecione o nome de utilizador. 

    ![Captura de ecrã que mostra os utilizadores do Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Clique em **função de diretório**.
  
    ![Captura de ecrã que mostra a função de diretório de portal do Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  A função **Administrador Global** ou **administrador limitado** > **administrador de faturação** é necessária para criar uma subscrição do Office 365 para os utilizadores em o Azure Active Directory existente.

    ![Captura de ecrã que mostra a função de diretório de portal do Azure administrador de faturação](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458). 
