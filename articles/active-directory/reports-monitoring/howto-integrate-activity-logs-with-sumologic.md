---
title: Registos de fluxo para SumoLogic utilizando o Monitor Azure [ Microsoft Docs
description: Saiba como integrar registos de Diretórios Ativos Azure com a SumoLogic utilizando o Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014390"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integre os registos de DirectórioActivo Azure com a SumoLogic utilizando o Monitor Azure

Neste artigo, aprende-se a integrar os registos de Diretórios Ativos azure (Azure AD) com a SumoLogic utilizando o Azure Monitor. Você primeiro encaminha os registos para um hub de eventos Azure, e depois integra o centro de eventos com a SumoLogic.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Um centro de eventos Azure que contém registos de atividade da Azure AD. Aprenda a transmitir os seus registos de [atividade para um centro de eventos.](quickstart-azure-monitor-stream-logs-to-event-hub.md) 
* Uma subscrição ativada por um único sinal SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Passos para integrar registos da AD Azure com a SumoLogic 

1. Primeiro, [transmita os registos da AD Azure para um centro de eventos Azure.](quickstart-azure-monitor-stream-logs-to-event-hub.md)
2. Configure a sua instância SumoLogic para [recolher registos para o Diretório Ativo Azure](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Instale a aplicação Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) para utilizar os dashboards pré-configurados que fornecem uma análise em tempo real do seu ambiente.

   ![Dashboard](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

* [Interpretar esquemas de registos de auditoria no Monitor Azure](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)