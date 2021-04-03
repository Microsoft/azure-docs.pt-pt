---
title: Faça o streaming de registos para a SumoLogic utilizando o monitor Azure | Microsoft Docs
description: Saiba como integrar os registos do Azure Ative Directory com a SumoLogic utilizando o Azure Monitor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e1f45c787c319c32358e7f310108131647d60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335838"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integre os registos do Azure Ative Directory com a SumoLogic utilizando o Azure Monitor

Neste artigo, aprende-se a integrar os registos do Azure Ative Directory (Azure AD) com a SumoLogic utilizando o Azure Monitor. Primeiro encaminha os registos para um centro de eventos Azure e depois integra o centro de eventos com a SumoLogic.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Um centro de eventos Azure que contém registos de atividade azure AD. Saiba como [transmitir os seus registos de atividade para um centro de eventos.](./tutorial-azure-monitor-stream-logs-to-event-hub.md) 
* Uma assinatura única de inscrição na SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Passos para integrar os registos AD do Azure com a SumoLogic 

1. Em primeiro lugar, [transmita os registos AD do Azure para um centro de eventos Azure](./tutorial-azure-monitor-stream-logs-to-event-hub.md).
2. Configure a sua instância SumoLogic para [recolher registos para o Azure Ative Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Instale a aplicação Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) para utilizar os dashboards pré-configurados que fornecem uma análise em tempo real do seu ambiente.

   ![Dashboard](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

* [Interpretar esquema de registos de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)