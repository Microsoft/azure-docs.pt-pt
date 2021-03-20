---
title: 'Resolução de problemas: Dados em falta nos registos de atividade descarregados | Microsoft Docs'
description: Fornece uma resolução para os dados em falta nos registos de atividades transferidos do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5003d7b644a0c75401a17ed6a37f31acd8180aa9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85608080"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Não consigo encontrar todos os dados nos registos de atividade do Azure Ative Directory que descarreguei.

## <a name="symptoms"></a>Sintomas

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Causa

Quando descarrega registos de atividades no portal Azure, limitamos a escala a 250.000 registos, classificados pela primeira vez mais recente. 

## <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

## <a name="next-steps"></a>Passos seguintes

* [Azure Ative Directory reporta FAQ](reports-faq.md)

