---
title: Realizar uma revisão de acesso de minhas funções do Azure AD no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como realizar uma revisão de acesso de suas funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66f16e02716ceb94d2c8b10bb246a13dc566229c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578334"
---
# <a name="perform-an-access-review-of-my-azure-ad-roles-in-pim"></a>Realizar uma revisão de acesso de minhas funções do Azure AD no PIM
O Azure Active Directory (AD) Privileged Identity Management (PIM) simplifica como as empresas a gerir o acesso privilegiado a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se forem atribuídos a uma função administrativa, o administrador com função privilegiada da sua organização poderá pedir-lhe para confirmar regularmente que ainda tem essa função para a sua tarefa. Poderá receber um e-mail que inclui uma ligação, ou pode ir diretamente para o [portal do Azure](https://portal.azure.com). Siga os passos neste artigo para efetuar um Self-rever de suas funções atribuídas.

Se for um administrador com função privilegiada ou um administrador global interessado nas revisões de acesso, obter mais detalhes em [como iniciar uma revisão de acesso](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Adicionar a aplicação Privileged Identity Management
Pode utilizar a aplicação do Azure AD Privileged Identity Management (PIM) a [portal do Azure](https://portal.azure.com/) executar sua revisão.  Se não tiver a aplicação Azure AD Privileged Identity Management no seu portal, siga estes passos para começar a utilizar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione o nome de utilizador no canto superior direito do portal do Azure e selecione o diretório em que irá ser operacional.
3. Selecione **Todos os serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.

## <a name="approve-or-deny-access"></a>Aprovar ou negar o acesso
Quando aprova ou negar o acesso, está apenas dizendo ao revisor se continuar a utilizar esta função ou não. Escolher **aprovar** se pretender manter-se na função, ou **negar** se não precisar de mais o acesso. Seu estado não será alterada de imediato, até que o revisor aplica-se os resultados.
Siga estes passos para encontrar e concluir a revisão de acesso:

1. No aplicativo PIM, selecione **revisão de acesso privilegiado**. Se tiver quaisquer revisões de acesso pendente, são exibidos no painel de revisões de acesso do Azure AD.
2. Selecione a revisão de que pretende concluir.
3. A menos que criou a revisão, aparecem como o único utilizador da revisão. Selecione a marca de verificação junto ao seu nome.
4. Escolhem **aprovar** ou **negar**. Poderá ter de incluir um motivo para a sua decisão no **forneça um motivo** caixa de texto.  
5. Fechar o **funções de análise do Azure AD** painel.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos Seguintes

- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
