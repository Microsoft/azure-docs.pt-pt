---
title: O que é a monitorização do Azure Active Directory? | Microsoft Docs
description: Mostra uma descrição geral da monitorização do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63f8440ca527a746f73574bc156037d85fc3599
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009047"
---
# <a name="what-is-azure-active-directory-monitoring"></a>O que é a monitorização do Azure Active Directory?

Com a monitorização do Azure Active Directory (Azure AD), pode agora encaminhar os registos de atividades do Azure AD para diferentes pontos finais. Em seguida, pode retê-los para utilização a longo prazo ou integrá-los com ferramentas de Gestão de Informações e Eventos de Segurança (SIEM) de terceiros para obter informações sobre o seu ambiente.

Atualmente, pode encaminhar os registos para:

- Uma conta de armazenamento do Azure.
- Um hub de eventos do Azure, para que possa integrar em instâncias do Splunk e do Sumologic.
- A área de trabalho do Azure Log Analytics, onde pode analisar os dados, criar o dashboard e alertar sobre eventos específicos

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="diagnostic-settings-configuration"></a>Configuração de definições de diagnóstico

Para configurar definições de monitorização para registos de atividades do Azure AD, primeiro inicie sessão no [portal do Azure](https://portal.azure.com) e, em seguida, selecione **Azure Active Directory**. A partir daqui, pode aceder à página de configuração de definições de diagnóstico de duas formas:

* Selecione **Definições de diagnóstico** na secção **Monitorização**.

    ![Definições de diagnósticos](./media/overview-monitoring/diagnostic-settings.png)
    
* Selecione **Registos de Auditoria** ou **Inícios de Sessão** e, em seguida, selecione **Exportar definições**. 

    ![Exportar definições](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Encaminhar registos para a conta de armazenamento

Ao encaminhar os registos para uma conta de armazenamento do Azure, pode mantê-los durante mais tempo do que o período de retenção predefinido descrito nas nossas [políticas de retenção](reference-reports-data-retention.md). Saiba como [encaminhar dados para a sua conta de armazenamento](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Transmitir registos no hub de eventos

O encaminhamento de registos para um hub de eventos do Azure permite-lhe integrar com ferramentas SIEM de terceiros, como o Sumologic e o Splunk. Esta integração permite-lhe combinar dados de registo de atividade do Azure AD com outros dados geridos pelo seu SIEM, para fornecer informações mais ricas sobre o seu ambiente. Saiba como [transmitir registos a um hub de eventos](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Enviar registos para os registos do Azure Monitor

[Registos de Monitor do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) é uma solução que consolida os dados de diferentes origens de monitorização e oferece um mecanismo de análise e linguagem de consulta que lhe dá informações sobre o funcionamento das suas aplicações e recursos. Através do envio do Azure AD registos de atividade para os registos do Azure Monitor, pode rapidamente obter, monitorizar e alertar relativamente dados recolhidos. Saiba como [enviar dados para os registos do Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md).

Também pode instalar as vistas pré-criadas para os registos de atividades do Azure AD para monitorizar cenários comuns que envolvem inícios de sessão e eventos de auditoria. Saiba como [instalar e utilizar os modos de exibição do log analytics para a atividade do Azure AD registos](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Passos Seguintes

* [Registos de atividades no Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Transmitir registos ao hub de eventos](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Enviar registos para os registos do Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)