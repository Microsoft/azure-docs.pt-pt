---
title: Criar uma revisão de acesso às funções de recurso azure na PIM - Azure AD [ Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções de recurso Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847076"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções de recurso Azure na Gestão de Identidade Privilegiada

O acesso a funções privilegiadas de recursos Azure para colaboradores muda ao longo do tempo. Para reduzir o risco associado a atribuições de funções velhas, deve rever regularmente o acesso. Pode utilizar o Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) para criar avaliações de acesso para funções privilegiadas de recursos Azure. Também pode configurar avaliações de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais avaliações de acesso para funções privilegiadas de recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador privilegiado](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Avaliações de acesso aberto

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro do papel de Administrador de Funções Privilegiadas.

1. Open **Azure AD Privileged Identity Management.**

1. No menu esquerdo, selecione **recursos Azure.**

1. Selecione o recurso que pretende gerir, como uma subscrição ou grupo de gestão.

1. Em 'Gerir', selecione **comentários de acesso**.

    ![Recursos Azure - Lista de avaliações de acesso que mostra o estado de todas as avaliações](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as definições para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso aparecerá na sua lista com um indicador do seu estado.

![Lista de avaliações de acesso mostrando o estado da revisão iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Por padrão, a Azure AD envia um e-mail aos revisores pouco depois do início da revisão. Se optar por não enviar o e-mail do Azure AD, certifique-se de informar os revisores de que uma revisão de acesso está à espera que sejam concluídas. Pode mostrar-lhes as instruções para rever o [acesso às funções](pim-resource-roles-perform-access-review.md)de recurso Azure .

## <a name="manage-the-access-review"></a>Gerir a revisão de acesso

Pode acompanhar o progresso à medida que os revisores completam as suas avaliações na página **de visão geral** da revisão de acesso. Não são alterados direitos de acesso no diretório até que a [revisão esteja concluída](pim-resource-roles-complete-access-review.md).

![Aceder a opiniões de opinião geral mostrando os detalhes da revisão](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se se trata de uma revisão única, depois de terminado o período de revisão de acesso ou se o administrador parar a revisão do acesso, siga os passos em [Completar uma revisão de acesso das funções de recurso azure](pim-resource-roles-complete-access-review.md) para ver e aplicar os resultados.  

Para gerir uma série de avaliações de acesso, navegue para a revisão de acesso e encontrará as próximas ocorrências em comentários agendados e editará a data de fim ou adicione/remova os revisores em conformidade.

Com base nas suas seleções nas **definições de conclusão,** a aplicação automática será executada após a data de fim da revisão ou quando parar manualmente a revisão. O estado da revisão passará de **Concluído** através de estados intermédios, como **a Aplicação** e, finalmente, o Estado **Aplicado.** Deve esperar ver os utilizadores negados, caso exista, a serem removidos das funções em poucos minutos.

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso às funções de recurso azure](pim-resource-roles-perform-access-review.md)
- [Complete uma revisão de acesso das funções de recurso Azure](pim-resource-roles-complete-access-review.md)
- [Criar uma revisão de acesso das funções da Azure AD](pim-how-to-start-security-review.md)
