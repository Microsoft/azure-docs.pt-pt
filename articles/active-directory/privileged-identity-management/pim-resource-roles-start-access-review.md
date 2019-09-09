---
title: Criar uma revisão de acesso das funções de recurso do Azure no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba674c143cd8d85b9764c8ade776b52dd1d3598d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804152"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Criar uma revisão de acesso das funções de recurso do Azure no PIM

O acesso a funções privilegiadas de recursos do Azure para funcionários muda ao longo do tempo. Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar o acesso regularmente. Você pode usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para criar revisões de acesso para funções de recursos do Azure com privilégios. Você também pode configurar revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais revisões de acesso para funções privilegiadas de recursos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Abrir revisões de acesso

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de administrador de função com privilégios.

1. Abra **Azure ad Privileged Identity Management**.

1. No menu à esquerda, clique em **recursos do Azure**.

1. Clique no recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em gerenciar, clique em **revisões de acesso**.

    ![Recursos do Azure-lista de revisões de acesso mostrando o status de todas as revisões](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Iniciar a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso mostrando o status da revisão iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da revisão. Se você optar por não fazer o Azure AD enviar o email, certifique-se de informar aos revisores que uma revisão de acesso está aguardando para que eles sejam concluídos. Você pode mostrar as instruções sobre como [revisar o acesso às funções de recurso do Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a revisão de acesso

Você pode acompanhar o progresso à medida que os revisores concluírem suas revisões na página **visão geral** da revisão de acesso. Nenhum direito de acesso é alterado no diretório até que a [revisão seja concluída](pim-resource-roles-complete-access-review.md).

![Página Visão geral das revisões de acesso mostrando os detalhes da revisão](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se esta for uma revisão única, depois que o período de revisão de acesso for concluído ou o administrador parar a revisão de acesso, siga as etapas em [concluir uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de revisões de acesso, navegue até a revisão de acesso e você encontrará ocorrências futuras nas revisões agendadas e edite a data de término ou adicione/remova revisores adequadamente.

Com base em suas seleções nas **configurações de conclusão**, a aplicação automática será executada após a data de término da revisão ou quando você interromper manualmente a revisão. O status da revisão será alterado de **concluído** por meio de Estados intermediários, como **aplicar** e, por fim, o estado **aplicado**. Você deve esperar que os usuários negados, se houver, sejam removidos das funções em alguns minutos.

## <a name="next-steps"></a>Passos Seguintes

- [Examinar o acesso às funções de recurso do Azure](pim-resource-roles-perform-access-review.md)
- [Concluir uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md)
- [Criar uma revisão de acesso das funções do Azure AD](pim-how-to-start-security-review.md)
