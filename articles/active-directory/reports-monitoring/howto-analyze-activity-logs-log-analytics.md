---
title: Analisar logs de atividade usando logs de Azure Monitor | Microsoft Docs
description: Saiba como analisar Azure Active Directory logs de atividades usando logs de Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d9bb8bca64292b73bc14f02d126d384da33780
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885269"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analisar logs de atividades do Azure AD com logs de Azure Monitor

Depois de [integrar os logs de atividades do Azure ad aos logs de Azure monitor](howto-integrate-activity-logs-with-log-analytics.md), você pode usar o poder dos logs de Azure monitor para obter informações sobre o seu ambiente. Você também pode instalar os [modos de exibição do log Analytics para logs de atividades do Azure ad](howto-install-use-log-analytics-views.md) para obter acesso a relatórios pré-criados sobre eventos de auditoria e de entrada em seu ambiente.

Neste artigo, você aprenderá a analisar os logs de atividades do Azure AD em seu espaço de trabalho do Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para acompanhar, você precisa de:

* Um espaço de trabalho Log Analytics em sua assinatura do Azure. Saiba como [criar um espaço de trabalho log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Primeiro, conclua as etapas para [rotear os logs de atividade do Azure ad para seu espaço de trabalho log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
*  [Acesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) ao espaço de trabalho do log Analytics
* As funções a seguir no Azure Active Directory (se você estiver acessando Log Analytics por meio do portal do Azure Active Directory)
    - Administrador de Segurança
    - Leitor de segurança
    - Leitor de relatórios
    - Administrador global
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navegue até o espaço de trabalho Log Analytics

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione **Azure Active Directory**e, em seguida, selecione **logs** na seção **monitoramento** para abrir o espaço de trabalho log Analytics. O espaço de trabalho será aberto com uma consulta padrão.

    ![Consulta padrão](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Exibir o esquema para logs de atividade do Azure AD

Os logs são enviados por push para as tabelas **AuditLogs** e **SigninLogs** no espaço de trabalho. Para exibir o esquema para essas tabelas:

1. Na exibição de consulta padrão na seção anterior, selecione **esquema** e expanda o espaço de trabalho. 

2. Expanda a seção **Gerenciamento de log** e expanda **AuditLogs** ou **SignInLogs** para exibir o esquema de log.
    ![logs de auditoria](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![logs de entrada](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Consultar os logs de atividades do Azure AD

Agora que você tem os logs em seu espaço de trabalho, agora você pode executar consultas em relação a eles. Por exemplo, para obter os principais aplicativos usados na última semana, substitua a consulta padrão pelo seguinte e selecione **executar**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Para obter os principais eventos de auditoria na última semana, use a seguinte consulta:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alerta sobre os dados do log de atividades do Azure AD

Você também pode configurar alertas em sua consulta. Por exemplo, para configurar um alerta quando mais de 10 aplicativos tiverem sido usados na última semana:

1. No espaço de trabalho, selecione **definir alerta** para abrir a página **criar regra** .

    ![Definir alerta](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selecione os **critérios de alerta** padrão criados no alerta e atualize o **limite** na métrica padrão para 10.

    ![Critérios de alerta](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Insira um nome e uma descrição para o alerta e escolha o nível de severidade. Para nosso exemplo, poderíamos defini-lo como **informativo**.

4. Selecione o **grupo de ações** que será alertado quando ocorrer o sinal. Você pode optar por notificar sua equipe por email ou mensagem de texto, ou pode automatizar a ação usando WebHooks, Azure Functions ou aplicativos lógicos. Saiba mais sobre como [criar e gerenciar grupos de alertas no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Depois de configurar o alerta, selecione **criar alerta** para habilitá-lo. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalar e usar exibições predefinidas para logs de atividades do Azure AD

Você também pode baixar os modos de exibição pré-criados do log Analytics para logs de atividades do Azure AD. As exibições fornecem vários relatórios relacionados a cenários comuns envolvendo eventos de auditoria e de entrada. Você também pode alertar sobre qualquer um dos dados fornecidos nos relatórios, usando as etapas descritas na seção anterior.

* **Eventos de provisionamento de conta do Azure ad**: essa exibição mostra relatórios relacionados à atividade de provisionamento de auditoria, como o número de novos usuários com falhas de provisionamento e provisionamento, o número de usuários atualizados e falhas de atualização e o número de usuários desprovisionados e falhas correspondentes.    
* **Eventos de entrada**: essa exibição mostra os relatórios mais relevantes relacionados à atividade de entrada de monitoramento, como entradas por aplicativo, usuário, dispositivo, bem como uma exibição de resumo que controla o número de entradas ao longo do tempo.
* **Usuários**que estão executando o consentimento: essa exibição mostra relatórios relacionados ao consentimento do usuário, como as concessões concedidas por usuário, entradas por usuários que receberam consentimento, bem como entradas por aplicativo para todos os aplicativos baseados em consentimento. 

Saiba como [instalar e usar exibições do log Analytics para logs de atividades do Azure ad](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Passos seguintes

* [Introdução às consultas em logs de Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Criar e gerenciar grupos de alertas no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalar e usar as exibições do log Analytics para Azure Active Directory](howto-install-use-log-analytics-views.md)
