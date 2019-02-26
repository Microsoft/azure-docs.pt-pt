---
title: Analisar registos de atividades do Azure Active Directory através de registos do Azure Monitor (pré-visualização) | Documentos da Microsoft
description: Saiba como analisar registos de atividades do Azure Active Directory através de registos do Azure Monitor (pré-visualização)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: aca7f0dbe0906ea0e1572f4580d8937ebf3eee64
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823263"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs-preview"></a>Azure AD de analisar os registos de atividades com os registos do Azure Monitor (pré-visualização)

Depois de [integrar o Azure AD registos de atividades com os registos do Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md), pode utilizar o poder do Azure Monitor registos para obter informações sobre o seu ambiente. Também pode instalar o [vistas de análise para a atividade do Azure AD de registo registos](howto-install-use-log-analytics-views.md) para obter acesso aos relatórios criados previamente em torno de auditoria e eventos de início de sessão no seu ambiente.

Neste artigo, irá aprender a analisar o Azure registos de atividades do AD no seu espaço de trabalho do Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para acompanhar, terá de:

* Uma área de trabalho do Log Analytics na sua subscrição do Azure. Saiba como [criar uma área de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Em primeiro lugar, conclua os passos para [registos de atividade de rota do Azure AD para a área de trabalho do Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Navegue para a área de trabalho do Log Analytics

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione **do Azure Active Directory**e, em seguida, selecione **registos** partir o **monitorização** secção para abrir a área de trabalho do Log Analytics. A área de trabalho será aberto com uma consulta predefinida.

    ![Consulta predefinida](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Ver o esquema para a atividade do Azure AD registos

Os registos são enviados por push para o **AuditLogs** e **SigninLogs** tabelas na área de trabalho. Para ver o esquema para estas tabelas:

1. Na vista de consulta padrão, na secção anterior, selecione **esquema** e expanda a área de trabalho. 

2. Expanda a **gestão de registos** secção e, em seguida, expanda o **AuditLogs** ou **SignInLogs** para ver o esquema de registo.
    ![Registos de auditoria](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![registos de início de sessão](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Registos de atividade de consulta do Azure AD

Agora que tem os registos na sua área de trabalho, agora, pode executar consultas contra eles. Por exemplo, para obter as aplicações utilizadas na última semana, substitua a consulta predefinida com o seguinte procedimento e selecione **executar**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Para obter os eventos de auditoria principais durante a semana passada, utilize a seguinte consulta:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alerta sobre dados de registo de atividade do Azure AD

Também pode configurar alertas em sua consulta. Por exemplo, para configurar um alerta quando mais de 10 aplicações foram utilizadas na última semana:

1. Na área de trabalho, selecione **definir alerta** para abrir o **criar regra** página.

    ![Definir alerta](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selecione a predefinição **critérios de alerta** criado no alerta e na atualização a **limiar** na métrica predefinida para o 10.

    ![Critérios de alerta](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Introduza um nome e uma descrição do alerta e escolha o nível de gravidade. No nosso exemplo, podemos pode defini-lo **informativo**.

4. Selecione o **grupo de ação** que será alertado quando ocorre o sinal. Pode optar por notificar a sua equipa através de e-mail ou mensagem de texto, ou pode automatizar a ação através de webhooks, as funções do Azure ou aplicações lógicas. Saiba mais sobre [criar e gerir grupos no portal do Azure de alerta](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Assim que tiver configurado o alerta, selecione **criar alerta** para ativá-la. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalar e utilizar vistas previamente criadas para a atividade do Azure AD registos

As vistas de análise de registo criados previamente para a atividade do Azure AD também pode transferir registos. As vistas fornecem vários relatórios relacionados aos cenários comuns que envolvem a auditoria e eventos de início de sessão. Pode também alertar em qualquer um dos dados fornecidos nos relatórios, utilizando os passos descritos na secção anterior.

* **Conta do AD FS eventos de aprovisionamento**: Esta vista mostra relatórios relacionados com a auditoria de atividade de aprovisionamento, como o número de novos utilizadores aprovisionado e falhas de aprovisionamento, o número de utilizadores atualizados e atualizar falhas e o número de utilizadores desconfigurados e falhas correspondentes.    
* **Eventos de inícios de sessão**: Esta vista mostra os relatórios mais relevantes relacionada com a monitorização da atividade de entrada, tais como inícios de sessão por aplicação, utilizador, dispositivo, bem como uma vista de resumo de controlo o número de inícios de sessão ao longo do tempo.
* **Os utilizadores consentir**: Esta vista mostra os relatórios relacionados com o consentimento do utilizador, como o consentimento concede por utilizador, inícios de sessão por utilizadores que autoriza, bem como inícios de sessão por aplicação para todos os aplicativos baseados no consentimento. 

Saiba como [instalar e utilizar os modos de exibição do log analytics para a atividade do Azure AD registos](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Passos Seguintes

* [Introdução às consultas nos registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Criar e gerir grupos de alertas no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalar e utilizar os modos de exibição do log analytics do Azure Active Directory](howto-install-use-log-analytics-views.md)
