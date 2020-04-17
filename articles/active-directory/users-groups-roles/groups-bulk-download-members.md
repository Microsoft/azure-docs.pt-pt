---
title: Lista de membros do grupo de descarregamento a granel - Portal de Diretório Ativo Azure [ Microsoft Docs
description: Adicione utilizadores a granel no centro de administração Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533695"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>A granel, os membros de um grupo no Azure Ative Directory

Utilizando o portal Azure Ative Directory (Azure AD), pode transferir em massa os membros de um grupo da sua organização para um ficheiro de valores separados (CSV) separados pela vírda.

## <a name="to-bulk-download-group-membership"></a>Para descarregar a granel membro do grupo

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador de utilizador na organização. Os proprietários de grupos também podem descarregar em massa membros de grupos que possuem.
1. Em Azure AD, selecione **Grupos** > **Todos os grupos**.
1. Abra o grupo cuja subscrição pretende descarregar e, em seguida, selecione **Membros**.
1. Na página **dos Membros,** selecione **Baixar os membros** para descarregar um ficheiro CSV com os membros do grupo.

   ![O comando dos Membros de Descarregamento está na página de perfil do grupo](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Verifique o estado do download

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para descarregar uma lista de membros do grupo pode funcionar até uma hora. Isto permite-lhe descarregar uma lista de pelo menos 500.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [Remoção a granel membros do grupo](groups-bulk-download-members.md)
