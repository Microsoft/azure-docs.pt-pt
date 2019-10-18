---
title: Baixar uma lista de grupos no portal de Azure Active Directory | Microsoft Docs
description: Baixe as propriedades do grupo em massa no centro de administração do Azure em Azure Active Directory.
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
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517147"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Baixar em massa uma lista de grupos (versão prévia) no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode fazer o download em massa da lista de todos os grupos em sua organização para um arquivo CSV (valores separados por vírgula).

## <a name="to-download-a-list-of-groups"></a>Para baixar uma lista de grupos

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador na organização.
1. No Azure AD, selecione **grupos**  > **baixar grupos**.
1. Na página de **Download grupos** , selecione **Iniciar** para receber um arquivo CSV que lista seus grupos.

   ![O comando baixar grupos está na página todos os grupos](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Verificar status de download

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![A página resultados de operações em massa mostra o status de solicitação em massa](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites do serviço de download em massa

Cada atividade em massa para baixar uma lista de grupos pode ser executada por até uma hora. Isso permite que você baixe uma lista de pelo menos 300.000 grupos.

## <a name="next-steps"></a>Passos seguintes

- [Remover membros do grupo em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
