---
title: 'Resolução de problemas: Dados em falta nos registos de atividades transferidos do Azure Active Directory | Documentos da Microsoft'
description: Fornece uma resolução para os dados em falta nos registos de atividades transferidos do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfb56ea81abeeba83bee73356c682b3e9fae866f
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58292922"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Não consigo encontrar todos os dados nos registos de atividades do Azure Active Directory que transferi

## <a name="symptoms"></a>Sintomas

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Quando transfere registos de atividades no portal do Azure, limitamos o dimensionamento para 250 000 registos, ordenados pelos mais recente primeiro. 

## <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

## <a name="next-steps"></a>Passos Seguintes

* [FAQ de relatórios do Azure Active Directory](reports-faq.md)

