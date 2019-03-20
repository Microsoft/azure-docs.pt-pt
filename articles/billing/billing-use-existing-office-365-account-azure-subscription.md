---
title: Inscrever-se para o Azure com a conta do Office 365 | Documentos da Microsoft
description: Saiba como criar uma subscrição do Azure, utilizando uma conta do Office 365
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 44ff08c84b3e95a5b598ebf4279fc2ffc46313e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863445"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Inscreva-se uma subscrição do Azure com a sua conta do Office 365
Se tiver uma subscrição do Office 365, pode utilizar a sua conta do Office 365 para criar uma subscrição do Azure. Inicie sessão para o [portal do Azure](https://portal.azure.com/) com o seu nome de utilizador do Office 365 e a palavra-passe. Se pretender configurar máquinas virtuais ou utilizar outros serviços do Azure, deve inscrever-se para uma subscrição do Azure. Pode partilhar a sua subscrição do Azure com outras pessoas e [utilizar o controlo de acesso baseado em funções para gerir o acesso à sua subscrição do Azure e recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Se já tiver uma conta do Office 365 e uma subscrição do Azure, veja [associar um inquilino do Office 365 para uma subscrição do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obter uma subscrição do Azure com a sua conta do Office 365

Poupe tempo e evitar a proliferação de conta ao inscrever-se para o Azure com o seu nome de utilizador do Office 365 e a palavra-passe. 

1. Inscreva-se em [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Inicie sessão com o seu nome de utilizador do Office 365 e a palavra-passe. A conta que utiliza não precisa de ter permissões de administrador. Se tiver mais de uma conta do Office 365, certifique-se de que utilizar as credenciais da conta do Office 365 que pretende associar a sua subscrição do Azure. 

   ![Captura de ecrã que mostra a página de início de sessão.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Introduza as informações necessárias e conclua o processo de inscrição. Algumas informações não podem ser necessárias se já tiver uma conta do Office 365.

    ![Captura de ecrã que mostra o formulário de inscrição.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se precisar de adicionar outras pessoas na sua organização para a subscrição do Azure, veja [introdução à gestão de acesso no portal do Azure](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Mais sobre as subscrições do Azure e o Office 365</a>
Office 365 e o Azure, utilize o serviço do Azure AD para gerir utilizadores e subscrições. Diretório do Azure é como um contentor no qual pode agrupar utilizadores e subscrições. Para utilizar as mesmas contas de utilizador para as suas subscrições do Azure e o Office 365, terá de certificar-se de que as subscrições do Azure são criadas no mesmo diretório que as subscrições do Office 365. Tenha em atenção os seguintes pontos:

* Uma subscrição é criada num diretório
* Os utilizadores que pertencem aos diretórios
* Uma subscrição que chegam no diretório do utilizador que cria a subscrição. Então, sua subscrição do Office 365 está associada a mesma conta que a sua subscrição do Azure.
* As subscrições do Azure pertencentes a utilizadores individuais no diretório
* Subscrições do Office 365 são propriedade o próprio diretório. Os utilizadores com as permissões corretas no diretório podem gerir estas subscrições.

![Captura de ecrã que mostra a relação do diretório, os utilizadores e subscrições.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para obter mais informações, consulte [subscrições do Azure como estão associadas com o Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).