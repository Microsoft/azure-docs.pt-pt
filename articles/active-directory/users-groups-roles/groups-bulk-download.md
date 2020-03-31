---
title: Faça o download de uma lista de grupos no portal azure Ative Diretório [ Microsoft Docs
description: Descarregue propriedades do grupo a granel no centro de administração Azure em Azure Ative Directory.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517147"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>A granel, descarregue uma lista de grupos (pré-visualização) no Diretório Ativo do Azure

Utilizando o portal Azure Ative Directory (Azure AD), pode transferir em massa a lista de todos os grupos da sua organização para um ficheiro de valores separados (CSV) separados pela vírda.

## <a name="to-download-a-list-of-groups"></a>Para descarregar uma lista de grupos

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador na organização.
1. Em Azure AD, selecione grupos de**descarregamento** **de grupos** > .
1. Na página de **download de Grupos,** selecione **Começar** a receber um ficheiro CSV com listas dos seus grupos.

   ![O comando dos grupos de descarregamento está na página de Todos os grupos](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verifique o estado do download

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk (pré-visualização).**

   ![A página de resultados das operações a granel mostra o seu estado de pedido em massa](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites de serviço de descarregamento a granel

Cada atividade a granel para descarregar uma lista de grupos pode funcionar até uma hora. Isto permite-lhe descarregar uma lista de pelo menos 300.000 grupos.

## <a name="next-steps"></a>Passos seguintes

- [Remoção a granel membros do grupo](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
