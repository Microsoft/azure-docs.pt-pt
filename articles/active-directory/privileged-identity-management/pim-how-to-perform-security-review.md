---
title: Reveja o acesso às funções de Azure AD na PIM - Azure AD ! Microsoft Docs
description: Saiba como rever o acesso às funções de Diretório Ativo Azure em Gestão de Identidade Privilegiada Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd0b63e08f44167b89b6e2d491722f3708b4036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055440"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Rever acesso a funções de Azure AD em Gestão de Identidade Privilegiada

A Gestão de Identidade Privilegiada (PIM) simplifica a forma como as empresas gerem o acesso privilegiado a recursos em Azure Ative Directory (AD) e outros serviços online da Microsoft, como o Microsoft 365 ou o Microsoft Intune. Siga os passos deste artigo para autorrever com sucesso as suas funções atribuídas.

Se for designado para um cargo administrativo, o administrador privilegiado da sua organização poderá pedir-lhe que confirme regularmente que ainda precisa desse papel para o seu trabalho. Você pode obter um e-mail que inclui um link, ou você pode ir diretamente para o [portal Azure](https://portal.azure.com) e começar.

Se você é um administrador privilegiado ou administrador global interessado em avaliações de acesso, obtenha mais detalhes sobre [Como iniciar uma revisão de acesso](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Adicione um azulejo do painel PIM

Se não tiver o serviço privilegiado de Gestão de Identidade fixado no seu painel de instrumentos no seu portal Azure, siga estes passos para começar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione o seu nome de utilizador no canto superior direito do portal Azure e selecione a organização AZure AD onde irá operar.
3. Selecione **Todos os serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Quando aprova ou nega o acesso, está apenas a dizer ao revisor se ainda usa este papel ou não. Escolha **Aprovar** se quiser continuar no papel, ou **negue** se já não precisar do acesso. O seu estado não mudará imediatamente, até que o revisor aplique os resultados.
Siga estes passos para encontrar e completar a revisão de acesso:

1. No serviço de Gestão de Identidade Privilegiada, selecione **o acesso privilegiado de Revisão.** Se tiver alguma avaliação de acesso pendente, elas aparecem na página de comentários do Azure AD **Access.**
2. Selecione a avaliação que pretende completar.
3. A menos que tenha criado a revisão, aparece como o único utilizador na revisão. Selecione a marca de verificação ao lado do seu nome.
4. Escolha **aprovar** ou **negar**. Poderá ter de incluir uma razão para a sua decisão na caixa de texto **'Fornecer'.**  
5. Feche a lâmina de **funções Azure AD de revisão.**

## <a name="next-steps"></a>Passos seguintes

- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
