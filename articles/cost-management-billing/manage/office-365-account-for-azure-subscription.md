---
title: Inscrever-se no Azure com a conta do Office 365 | Microsoft Docs
description: Saiba como criar uma subscrição do Azure com uma conta do Office 365
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 612c9d3e66a54e248c011e8dbedec2e2291b6a8f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988126"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Inscrever-se para uma subscrição do Azure com a sua conta do Office 365
Se tiver uma subscrição do Office 365, pode usar a sua conta do Office 365 para criar uma subscrição do Azure. Inicie sessão no [portal do Azure](https://portal.azure.com/) com o seu nome de utilizador e palavra-passe do Office 365. Se quiser configurar máquinas virtuais ou usar outros serviços do Azure, tem de se inscrever para uma subscrição do Azure. Pode partilhar a sua subscrição do Azure com outras pessoas e [usar o Controlo de Acesso Baseado em Funções para gerir o acesso à sua subscrição e aos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Se já tiver uma conta do Office 365 e uma subscrição do Azure, veja [Associar um inquilino do Office 365 a uma subscrição do Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obter uma subscrição do Azure com a sua conta do Office 365

Poupe tempo e evite a proliferação de contas inscrevendo-se no Azure com o seu nome de utilizador e palavra-passe do Office 365.

1. Inscreva-se em [azure.microsoft.com/pt-pt/](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Inicie sessão com o seu nome de utilizador e palavra-passe do Office 365. A conta usada não precisa de ter permissões de administrador. Se tiver mais de uma conta do Office 365, confirma que utiliza as credenciais da conta do Office 365 que pretende associar à sua subscrição do Azure.

   ![Captura de ecrã que mostra a página de início de sessão.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Introduza as informações necessárias e conclua o processo de inscrição. Algumas informações poderão não ser necessárias se já tiver uma conta do Office 365.

    ![Captura de ecrã que mostra o formulário de inscrição.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se precisar de adicionar outras pessoas na sua organização à subscrição do Azure, veja [Introdução à gestão de acesso no portal do Azure](../../role-based-access-control/overview.md).

## <a id="more-about-subs">Mais informações sobre as subscrição do Azure e do Office 365</a>
O Office 365 e o Azure usam o serviço do AAD para gerir utilizadores e subscrições. O diretório do Azure é como um contentor no qual pode agrupar utilizadores e subscrições. Para usar as mesmas contas de utilizador para as suas subscrições do Azure e do Office 365, precisa de se certificar de que as subscrições do Azure são criadas no mesmo diretório que as subscrições do Office 365. Tenha em consideração os seguintes pontos:

* Uma subscrição é criada num diretório
* Os utilizadores pertencem a diretórios
* Uma subscrição chega ao diretório do utilizador que cria a subscrição. Portanto, a sua subscrição do Office 365 está vinculada à mesma conta que a sua subscrição do Azure.
* As subscrições do Azure pertencem a utilizadores individuais no diretório
* As subscrições do Office 365 pertencem ao próprio diretório. Os utilizadores com as permissões corretas no diretório podem gerir estas subscrições.

![Captura de ecrã que mostra a relação do diretório, dos utilizadores e das subscrições.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Para obter mais informações, veja [Como estão associadas as subscrições do Azure ao AAD](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
