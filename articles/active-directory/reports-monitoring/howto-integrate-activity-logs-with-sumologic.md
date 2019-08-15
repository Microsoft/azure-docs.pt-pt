---
title: Transmitir logs de Azure Active Directory para SumoLogic usando Azure Monitor | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com o SumoLogic usando Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1645415a8cfd86b142c25dc145c60e8b986d1013
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989744"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrar logs de Azure Active Directory com o SumoLogic usando Azure Monitor

Neste artigo, você aprenderá a integrar os logs do Azure Active Directory (Azure AD) ao SumoLogic usando Azure Monitor. Primeiro, você roteia os logs para um hub de eventos do Azure e, em seguida, integra o Hub de eventos ao SumoLogic.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Um hub de eventos do Azure que contém logs de atividade do Azure AD. Saiba como [transmitir seus logs de atividade para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Uma assinatura habilitada para logon único do SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Etapas para integrar os logs do Azure AD ao SumoLogic 

1. Primeiro, [transmita os logs do Azure ad para um hub de eventos do Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Configure sua instância do SumoLogic para [coletar logs para Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Instale o aplicativo SumoLogic do Azure ad](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) para usar os painéis pré-configurados que fornecem análise em tempo real de seu ambiente.

   ![Dashboard](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Passos Seguintes

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretar o esquema dos registos de auditoria no Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)