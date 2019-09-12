---
title: Remover em massa os membros do grupo carregando um arquivo CSV-Azure Active Directory | Microsoft Docs
description: Adicione usuários em massa no centro de administração do Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911049"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Remover membros do grupo em massa (visualização) no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode remover um grande número de membros de um grupo usando um arquivo CSV (valores separados por vírgula) para remover em massa membros do grupo.

> [!NOTE]
> As operações em massa do Azure AD são um recurso de visualização pública do Azure AD e estão disponíveis com qualquer plano de licença pago do Azure AD. Para obter mais informações sobre os termos de uso de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-removal-service-limits"></a>Limites de serviço de remoção em massa

Cada atividade em massa para remover uma lista de membros do grupo de pode ser executada por até uma hora. Isso permite a remoção de uma lista de pelo menos 40.000 membros.

## <a name="to-bulk-remove-group-members"></a>Para remover em massa membros do grupo

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários do grupo também podem remover em massa os membros dos grupos que eles possuem.
1. No Azure AD, selecione **grupos** > **todos os grupos**.
1. Abra o grupo do qual você está removendo membros e, em seguida, selecione **Membros**.
1. Na página **Membros** , selecione **remover membros** para baixar, atualizar e carregar um arquivo CSV listando os membros que você deseja remover do grupo.

   ![O comando remover membros está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Verificar status da remoção

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![A página resultados de operações em massa mostra o status de solicitação em massa](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Passos Seguintes

- [Membros do grupo de importação em massa](groups-bulk-import-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
