---
title: Rever o acesso às funções da Azure AD na PIM - Azure AD [ Microsoft Docs
description: Saiba como rever o acesso às funções de Diretório Ativo Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847096"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Rever o acesso a funções da Azure AD na Gestão de Identidade Privilegiada

A Privileged Identity Management (PIM) simplifica a forma como as empresas gerem o acesso privilegiado aos recursos em Azure Ative Directory (AD) e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune. Siga os passos deste artigo para se autorrever com sucesso as suas funções atribuídas.

Se for designado para um cargo administrativo, o administrador privilegiado da sua organização pode pedir-lhe que confirme regularmente que ainda precisa desse papel para o seu trabalho. Você pode obter um e-mail que inclui um link, ou você pode ir direto para o [portal Azure](https://portal.azure.com) e começar.

Se é um administrador privilegiado ou administrador global interessado em avaliações de acesso, obtenha mais detalhes em [Como iniciar uma revisão](pim-how-to-start-security-review.md)de acesso .

## <a name="add-a-pim-dashboard-tile"></a>Adicione um azulejo pim dashboard

Se não tiver o serviço de Gestão de Identidade Privilegiada Azure AD fixado no seu painel de instrumentos no seu portal Azure, siga estes passos para começar.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione o seu nome de utilizador no canto superior direito do portal Azure e selecione o diretório onde irá operar.
3. Selecione **Todos os serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.

## <a name="approve-or-deny-access"></a>Aprovar ou negar o acesso

Quando aprova ou nega o acesso, está apenas a dizer ao revisor se ainda usa este papel ou não. Escolha **Aprovar** se quiser permanecer no papel, ou **negá-lo** se já não precisar de acesso. O seu estado não mudará imediatamente, até que o revisor aplique os resultados.
Siga estes passos para encontrar e completar a revisão de acesso:

1. No serviço de Gestão de Identidade Privilegiada, selecione **Rever acesso privilegiado.** Se tiver alguma avaliação de acesso pendente, elas aparecem na página de comentários do Azure AD **Access.**
2. Selecione a avaliação que pretende completar.
3. A menos que tenha criado a revisão, aparece como o único utilizador na revisão. Selecione a marca de verificação ao lado do seu nome.
4. Escolha **aprovar** ou **negar**. Poderá ter de incluir uma razão para a sua decisão na caixa de texto **Provide uma razão.**  
5. Feche a lâmina de **funções da Review Azure AD.**

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes

- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
