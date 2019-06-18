---
title: Integrar registos do Azure Active Directory com o Splunk através do Azure Monitor | Documentos da Microsoft
description: Saiba como integrar registos do Azure Active Directory com SumoLogic através do Azure Monitor
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
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f30e19a351f7b25f995a85cfd566bcba091ac27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597825"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Como: Integrar registos do Azure Active Directory com o Splunk através do Azure Monitor

Neste artigo, saiba como integrar registos do Azure Active Directory (Azure AD) com Splunk com o Azure Monitor. Primeiro encaminha os registos para um hub de eventos do Azure e, em seguida, integrar o hub de eventos com Splunk.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar esta funcionalidade, precisa de:
* Registos de um hub de eventos do Azure que contém a atividade do Azure AD. Saiba como [transmitir os registos de atividade para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* O suplemento de Monitor do Azure para o Splunk. [Transferir e configurar a sua instância do Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrar registos do Azure Active Directory 

1. Abra a sua instância do Splunk e selecione **resumo de dados**.

    ![O botão "Resumo de dados"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecione o **Sourcetypes** separador e, em seguida, selecione **amal: aadal:audit**

    ![A guia Sourcetypes de resumo de dados](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    A atividade do Azure AD, os registos são mostrados na figura a seguir:

    ![Registos de atividade](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se não pode instalar um suplemento na sua instância de Splunk (por exemplo, se estiver a utilizar um proxy ou em execução na Splunk Cloud), pode reencaminhar estes eventos para o Recoletor de eventos do Splunk HTTP. Para tal, utilize este [função do Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é acionado por mensagens novas no hub de eventos. 
>

## <a name="next-steps"></a>Passos Seguintes

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretar o esquema dos registos de auditoria no Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretar o esquema dos registos de início de sessão no Azure Monitor)
* [Perguntas mais frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)