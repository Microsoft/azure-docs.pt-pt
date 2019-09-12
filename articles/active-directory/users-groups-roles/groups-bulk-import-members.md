---
title: Upload de importação em massa para adicionar membros a um grupo-Azure Active Directory | Microsoft Docs
description: Adicione membros do grupo em massa no centro de administração do Azure Active Directory.
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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911047"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Importar membros do grupo (visualização) em massa no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode adicionar um grande número de membros a um grupo usando um arquivo CSV (valores separados por vírgula) para importar membros do grupo em massa.

> [!NOTE]
> As operações em massa do Azure AD são um recurso de visualização pública do Azure AD e estão disponíveis com qualquer plano de licença pago do Azure AD. Para obter mais informações sobre os termos de uso de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa para importar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite a importação de uma lista de pelo menos 40.000 membros.

## <a name="to-bulk-import-group-members"></a>Para importar membros do grupo em massa

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários do grupo também podem importar em massa membros de grupos que possuem.
1. No Azure AD, selecione **grupos** > **todos os grupos**.
1. Abra o grupo ao qual você está adicionando Membros e, em seguida, selecione **Membros**.
1. Na página **Membros** , selecione **importar Membros** para baixar, atualizar e carregar um arquivo CSV listando os membros que você deseja importar para o grupo.

   ![O comando importar Membros está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Verificar status da importação

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![A página resultados de operações em massa mostra o status de solicitação em massa](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>Passos Seguintes

- [Remover membros do grupo em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
