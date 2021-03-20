---
title: Lista de membros do grupo de descarregamento a granel - Azure Ative Directory portal | Microsoft Docs
description: Adicione utilizadores a granel no centro de administração Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4055fb3bfdbcfd7e4a74d9290017a6193578146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547701"
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

[![Verifique o estado na página resultados das operações a granel.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para descarregar uma lista de membros do grupo pode ser executada por até uma hora. Isto permite-lhe descarregar uma lista de pelo menos 500.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [A granel remove membros do grupo](groups-bulk-download-members.md)
