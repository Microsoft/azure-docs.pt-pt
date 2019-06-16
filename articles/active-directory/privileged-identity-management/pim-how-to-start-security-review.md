---
title: Criar uma revisão de acesso de funções do Azure AD no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como criar uma revisão de acesso de funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0680ddf2c9e654455933bf09699ab81e8ab65d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141743"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Criar uma revisão de acesso de funções do Azure AD no PIM

Acesso privilegiado de funções do Azure AD para funcionários alterações ao longo do tempo. Para reduzir o risco associado atribuições de função obsoleta, deve consultar regularmente acesso. Pode utilizar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para criar as revisões de acesso para o privileged funções do Azure AD. Também pode configurar as revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar um ou mais as revisões de acesso para o privileged funções do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

- [Administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Revisões de acesso aberto

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com um utilizador que seja membro da função de administrador com função privilegiada.

1. Open **do Azure AD Privileged Identity Management**.

1. No menu à esquerda, clique em **funções do Azure AD** e, em seguida, clique em **as revisões de acesso**.

1. Em gerir, clique em **as revisões de acesso**.

    ![Revisões de acesso de funções do Azure do AD-](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Iniciar a revisão de acesso

Depois de especificar as definições para uma revisão de acesso, clique em **iniciar**. A revisão de acesso será apresentado na sua lista com um indicador do seu estado.

![Lista de revisões de acesso](./media/pim-how-to-start-security-review/access-reviews-list.png)

Por predefinição, o Azure AD envia um e-mail para os revisores logo após a revisão é iniciado. Se optar por não ter o Azure AD para enviar o e-mail, certifique-se de que informe os revisores de que uma revisão de acesso está a aguardar a sua conclusão. Pode mostrar-lhes as instruções sobre como a [rever acesso para funções do Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerir a revisão de acesso

Pode monitorizar o progresso conforme os revisores de concluir as revisões no **descrição geral** página de revisão de acesso. Nenhum direito de acesso forem alterado no diretório até que o [revisão é concluída](pim-how-to-complete-review.md).

![Progresso de revisões de acesso](./media/pim-how-to-start-security-review/access-review-overview.png)

Se se tratar de uma revisão de uso individual, em seguida, após o período de revisão de acesso ou o administrador para a revisão de acesso, siga os passos em [concluir uma revisão de acesso de funções do Azure AD](pim-how-to-complete-review.md) para ver e aplicar os resultados.  

Para gerir uma série de acesso a revisões, navegue para a revisão de acesso, e será encontrar ocorrências futuras nas revisões de agendada e edite a data de fim ou adicionar/remover os revisores da mesma forma.

Com base nas suas seleções **nas definições de conclusão**, aplicar automaticamente irá ser executado após a data de fim a revisão ou quando manualmente para a revisão. O estado da revisão será alterado de **concluído** através de Estados intermediários como **aplicar** e, finalmente, para o estado **aplicados**. Deve esperar para ver utilizadores sem permissão, caso haja algum, que está a ser removido da funções dentro de alguns minutos.

## <a name="next-steps"></a>Passos Seguintes

- [Rever acesso para funções do Azure AD](pim-how-to-perform-security-review.md)
- [Concluir uma revisão de acesso de funções do Azure AD](pim-how-to-complete-review.md)
- [Criar uma revisão de acesso de funções de recursos do Azure](pim-resource-roles-start-access-review.md)
