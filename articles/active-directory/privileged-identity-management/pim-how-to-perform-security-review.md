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
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b9f16d6c7f738e64f154506228c832e31e0f5b
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809045"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Examinar o acesso às funções do Azure AD no Privileged Identity Management

O Privileged Identity Management (PIM) simplifica o modo como as empresas gerenciam o acesso privilegiado a recursos no Azure Active Directory (AD) e outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune. Siga as etapas neste artigo para revisar com êxito as funções atribuídas.

Se você estiver atribuído a uma função administrativa, o administrador de função com privilégios de sua organização poderá solicitar que você confirme regularmente que ainda precisa dessa função para seu trabalho. Você pode receber um email que inclui um link ou pode ir diretamente para a [portal do Azure](https://portal.azure.com) e começar.

Se você for um administrador de função com privilégios ou administrador global interessado em revisões de acesso, obtenha mais detalhes em [como iniciar uma revisão de acesso](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Adicionar um bloco do painel PIM

Se você não tiver o serviço Azure AD Privileged Identity Management fixado ao seu painel no seu portal do Azure, siga estas etapas para começar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione seu nome de usuário no canto superior direito do portal do Azure e selecione o diretório em que você estará operando.
3. Selecione **Todos os serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Ao aprovar ou negar o acesso, você estará apenas informando ao revisor se você ainda usa essa função ou não. Escolha **aprovar** se você quiser permanecer na função ou **negar** se não precisar mais do acesso. Seu status não será alterado imediatamente, até que o revisor aplique os resultados.
Siga estas etapas para localizar e concluir a revisão de acesso:

1. No serviço Privileged Identity Management, selecione **revisar acesso privilegiado**. Se você tiver quaisquer revisões de acesso pendentes, elas aparecerão na página de **revisões de acesso** do Azure AD.
2. Selecione a revisão que você deseja concluir.
3. A menos que você tenha criado a revisão, você aparece como o único usuário na revisão. Selecione a marca de seleção ao lado do seu nome.
4. Escolha **aprovar** ou **negar**. Talvez seja necessário incluir um motivo para sua decisão na caixa de texto **fornecer um motivo** .  
5. Feche a folha **examinar funções do Azure ad** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes

- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
