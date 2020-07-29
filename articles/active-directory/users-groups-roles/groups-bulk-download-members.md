---
title: Lista de membros do grupo de descarregamento a granel - Portal Azure Ative Directory / Microsoft Docs
description: Adicione utilizadores a granel no centro de administração Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65a6a622a0d3fac7b28cc699d860068f12b780fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728610"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Membros de descarregamento em massa de um grupo em Azure Ative Directory

Utilizando o portal Azure Ative Directory (Azure AD), pode descarregar em massa os membros de um grupo na sua organização para um ficheiro de valores separados por vírgula (CSV).

## <a name="to-bulk-download-group-membership"></a>Para descarregar em massa a adesão ao grupo

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador do Utilizador na organização. Os proprietários do grupo também podem descarregar em massa membros de grupos que possuem.
1. Em Azure AD, selecione **Grupos**  >  **Todos os grupos**.
1. Abra o grupo cuja adesão pretende baixar e, em seguida, selecione **Membros**.
1. Na página dos **Membros,** selecione **Baixar os membros** para descarregar um ficheiro CSV listando os membros do grupo.

   ![O comando de Membros de Descarregamento está na página de perfil do grupo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Verifique o estado do descarregamento

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para descarregar uma lista de membros do grupo pode ser executada por até uma hora. Isto permite-lhe descarregar uma lista de pelo menos 500.000 membros.

## <a name="next-steps"></a>Próximos passos

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [A granel remove membros do grupo](groups-bulk-download-members.md)
