---
title: Examinar o acesso às funções de recurso do Azure no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como examinar o acesso das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804097"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Examinar o acesso às funções de recurso do Azure no PIM
O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) simplifica como as empresas gerenciam o acesso privilegiado aos recursos no Azure. 

Se você estiver atribuído a uma função administrativa, o administrador de função com privilégios de sua organização poderá solicitar que você confirme regularmente que ainda precisa dessa função para seu trabalho. Você pode receber um email que inclui um link ou pode ir diretamente para a [portal do Azure](https://portal.azure.com). Siga as etapas neste artigo para executar uma autorevisão de suas funções atribuídas.

Se você for um administrador de função com privilégios interessado em revisões de acesso, obtenha mais detalhes em [como iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Adicionar a aplicação Privileged Identity Management
Você pode usar o aplicativo PIM Azure Active Directory (Azure AD) no [portal do Azure](https://portal.azure.com/) para executar sua análise. Se você não tiver o aplicativo em seu portal, siga estas etapas para começar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você estará operando.
3. Selecione **todos os serviços**e use a caixa de **filtro** para procurar *Azure ad Privileged Identity Management*.
4. Marque **fixar no painel**e, em seguida, selecione **criar**. O aplicativo PIM é aberto.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso
Ao aprovar ou negar o acesso, você estará apenas informando ao revisor se você ainda usa essa função ou não. Escolha **aprovar** se você quiser permanecer na função ou **negar** se não precisar mais do acesso. Seu status é alterado somente quando o revisor aplica os resultados.

Siga estas etapas para localizar e concluir a revisão de acesso:
1. Navegue até o aplicativo PIM do Azure AD.
2. Selecione a folha **examinar acesso** .

   ![Captura de tela do aplicativo PIM, com a folha de acesso de revisão selecionada](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Selecione a revisão que você deseja concluir. 
4. Escolha **aprovar** ou **negar**. Na **caixa fornecer um motivo**, talvez seja necessário incluir um motivo para sua decisão.

   ![Captura de tela da página de detalhes da revisão](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passos Seguintes

- [Executar uma revisão de acesso de minhas funções do Azure AD no PIM](pim-how-to-perform-security-review.md)
