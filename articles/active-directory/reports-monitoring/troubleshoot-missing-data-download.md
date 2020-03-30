---
title: 'Resolução de problemas: Dados em falta nos registos de atividades descarregados / Microsoft Docs'
description: Fornece uma resolução para os dados em falta nos registos de atividades transferidos do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee07fdb6f8a4e69600297bdb16b6ad74793f10c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007726"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Não encontro todos os dados nos registos de atividade do Diretório Ativo Azure que descarreguei

## <a name="symptoms"></a>Sintomas

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Ao descarregar registos de atividade sintetizados no portal Azure, limitamos a escala a 250.000 discos, classificados pela primeira vez. 

## <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

## <a name="next-steps"></a>Passos seguintes

* [Azure Ative Directory reporta FAQ](reports-faq.md)

