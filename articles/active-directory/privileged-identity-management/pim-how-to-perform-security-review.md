---
title: Examinar o acesso às funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como examinar o acesso das funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bd548041b086eef8d788a696497163c756fd5b7
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804419"
---
# <a name="review-access-to-azure-ad-roles-in-pim"></a>Examinar o acesso às funções do Azure AD no PIM

O Azure Active Directory (AD) Privileged Identity Management (PIM) simplifica o modo como as empresas gerenciam o acesso privilegiado a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.  

Se você estiver atribuído a uma função administrativa, o administrador de função com privilégios de sua organização poderá solicitar que você confirme regularmente que ainda precisa dessa função para seu trabalho. Você pode receber um email que inclui um link ou pode ir diretamente para a [portal do Azure](https://portal.azure.com). Siga as etapas neste artigo para executar uma autorevisão de suas funções atribuídas.

Se você for um administrador de função com privilégios ou administrador global interessado em revisões de acesso, obtenha mais detalhes em [como iniciar uma revisão de acesso](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Adicionar a aplicação Privileged Identity Management
Você pode usar o aplicativo Azure AD Privileged Identity Management (PIM) no [portal do Azure](https://portal.azure.com/) para executar sua análise.  Se você não tiver o aplicativo Azure AD Privileged Identity Management em seu portal, siga estas etapas para começar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você estará operando.
3. Selecione **Todos os serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso
Ao aprovar ou negar o acesso, você estará apenas informando ao revisor se você ainda usa essa função ou não. Escolha **aprovar** se você quiser permanecer na função ou **negar** se não precisar mais do acesso. Seu status não será alterado imediatamente, até que o revisor aplique os resultados.
Siga estas etapas para localizar e concluir a revisão de acesso:

1. No aplicativo PIM, selecione **revisar acesso privilegiado**. Se você tiver quaisquer análises de acesso pendentes, elas aparecerão na folha de revisões de acesso do Azure AD.
2. Selecione a revisão que você deseja concluir.
3. A menos que você tenha criado a revisão, você aparece como o único usuário na revisão. Selecione a marca de seleção ao lado do seu nome.
4. Escolha **aprovar** ou **negar**. Talvez seja necessário incluir um motivo para sua decisão na caixa de texto **fornecer um motivo** .  
5. Feche a folha **examinar funções do Azure ad** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes

- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
