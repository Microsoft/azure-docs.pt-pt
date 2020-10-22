---
title: Crie uma revisão de acesso das funções da AD Azure na PIM - Azure AD ! Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções Azure AD em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9fdc44681c8773d7b6f724174a74e76e57939e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369765"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções AZURE AD na Gestão de Identidade Privilegiada

Para reduzir o risco associado a atribuições de funções velhas, deve rever regularmente o acesso. Você pode usar Azure AD Gestão de Identidade Privilegiada (PIM) para criar avaliações de acesso para funções privilegiadas da Azure AD. Também pode configurar comentários de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais avaliações de acesso para funções privilegiadas de Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador privilegiado](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Comentários de acesso aberto

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que é membro da função de administrador de função Privileged.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **funções AD Azure**.

1. Em Gestão, selecione **avaliações de Acesso**e, em seguida, selecione **New**.

    ![Funções AZure AD - Lista de avaliações de acesso mostrando o estado de todas as avaliações](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de ter especificado as definições para uma revisão de acesso, selecione **Start**. A revisão de acesso aparecerá na sua lista com um indicador do seu estado.

![Lista de comentários de acesso mostrando o estado das avaliações iniciadas](./media/pim-how-to-start-security-review/access-reviews-list.png)

Por padrão, a Azure AD envia um e-mail aos revisores logo após o início da revisão. Se optar por não ter a Azure AD a enviar o e-mail, certifique-se de informar os revisores de que uma revisão de acesso está à espera que eles estejam concluídos. Pode mostrar-lhes as instruções de como rever o [acesso às funções AD do Azure](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerir a revisão de acesso

Pode acompanhar o progresso à medida que os revisores completam as suas avaliações na página **geral** da revisão de acesso. Não são alterados os direitos de acesso no diretório até que a [revisão esteja concluída.](pim-how-to-complete-review.md)

![Aceder à página geral das avaliações mostrando os detalhes da análise](./media/pim-how-to-start-security-review/access-review-overview.png)

Se esta for uma revisão única, então após o fim do período de revisão de acesso ou o administrador parar a revisão de acesso, siga os passos em [Concluir uma revisão de acesso das funções AD do Azure](pim-how-to-complete-review.md) para ver e aplicar os resultados.  

Para gerir uma série de avaliações de acesso, navegue para a revisão de acesso e encontrará as próximas ocorrências em avaliações agendadas e editará a data final ou adicione/remova os revisores em conformidade.

Com base nas suas seleções nas **definições de conclusão,** a inscrição automática será executada após a data de fim da revisão ou quando parar manualmente a revisão. O estado da revisão passará de **Concluído** através de estados intermédios como **a Aplicação** e, finalmente, para o Estado **Aplicado.** Deverá esperar ver utilizadores negados, caso existam, a serem removidos das funções em poucos minutos.

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso aos papéis de Ad Azure](pim-how-to-perform-security-review.md)
- [Complete uma revisão de acesso das funções AD da Azure](pim-how-to-complete-review.md)
- [Criar uma revisão de acesso das funções de recursos do Azure](pim-resource-roles-start-access-review.md)
