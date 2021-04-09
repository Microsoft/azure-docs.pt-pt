---
title: Analise os registos de atividade usando os registos do Monitor Azure | Microsoft Docs
description: Saiba como analisar registos de atividade do Azure Ative Directory utilizando registos do Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: 905261058c2de0afae18cbc5572c64962bef8834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580026"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analise os registos de atividades da AZure com registos do Monitor Azure

Depois de [integrar os registos de atividade do Azure AD com registos do Azure Monitor,](howto-integrate-activity-logs-with-log-analytics.md)pode utilizar a potência dos registos do Azure Monitor para obter informações sobre o seu ambiente. Também pode instalar as [vistas de analítica do Log para registos de atividades Azure AD](howto-install-use-log-analytics-views.md) para ter acesso a relatórios pré-construídos em torno de eventos de auditoria e login no seu ambiente.

Neste artigo, aprende-se a analisar os registos de atividades Azure AD no seu espaço de trabalho Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para acompanhar, precisa:

* Um espaço de trabalho Log Analytics na sua subscrição Azure. Saiba como [criar um espaço de trabalho Log Analytics.](../../azure-monitor/logs/quick-create-workspace.md)
* Em primeiro lugar, complete os passos para [encaminhar os registos de atividade azure AD para o seu espaço de trabalho Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
*  [Acesso](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) ao espaço de trabalho de análise de log analytics
* As seguintes funções no Azure Ative Directory (se estiver a aceder ao Log Analytics através do portal Azure Ative Directory)
    - Administrador de Segurança
    - Leitor de Segurança
    - Leitor de Relatórios
    - Admin Global
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navegue para o espaço de trabalho Log Analytics

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione **O Diretório Ativo Azure** e, em seguida, selecione **Logs** da secção **de Monitorização** para abrir o seu espaço de trabalho Log Analytics. O espaço de trabalho abrirá com uma consulta padrão.

    ![Consulta padrão](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Ver o esquema para registos de atividades Azure AD

Os registos são empurrados para as **tabelas AuditLogs** e **SigninLogs** no espaço de trabalho. Para ver o esquema destas tabelas:

1. A partir da vista de consulta padrão na secção anterior, selecione **Schema** e expanda o espaço de trabalho. 

2. Expanda a secção **de Gestão de Registos** e, em seguida, expanda os **AuditLogs** ou **SigninLogs** para visualizar o esquema de registo.
    ![Registos de auditoria ](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![ Registos de Sinais](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Consulta os registos de atividade azure AD

Agora que tem os registos no seu espaço de trabalho, agora pode fazer consultas contra eles. Por exemplo, para obter as principais aplicações usadas na última semana, substitua a consulta por defeito com a seguinte e selecione **Executar**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Para obter os melhores eventos de auditoria durante a semana passada, utilize a seguinte consulta:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alerta sobre os dados de registo de atividade da AZure AD

Também pode configurar alertas na sua consulta. Por exemplo, para configurar um alerta quando mais de 10 aplicações foram usadas na última semana:

1. A partir do espaço de trabalho, selecione **Definir alerta** para abrir a página **de regras Criar.**

    ![Definir alerta](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selecione os **critérios** de alerta predefinidos criados no alerta e atualize o **Limiar** na métrica predefinitiva para 10.

    ![Critérios de alerta](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Insira um nome e descrição para o alerta e escolha o nível de gravidade. Por exemplo, poderíamos defini-lo para **Informação.**

4. Selecione o **Grupo de Ação** que será alertado quando o sinal ocorrer. Pode optar por notificar a sua equipa por e-mail ou mensagem de texto, ou pode automatizar a ação utilizando webhooks, funções Azure ou aplicações lógicas. Saiba mais sobre [a criação e gestão de grupos de alerta no portal Azure.](../../azure-monitor/alerts/action-groups.md)

5. Uma vez configurado o alerta, selecione **Criar alerta** para o ativar. 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>Utilize livros pré-construídos para registos de atividades Azure AD

Os livros fornecem vários relatórios relacionados com cenários comuns envolvendo eventos de auditoria, inscrição e provisionamento. Também pode alertar qualquer um dos dados fornecidos nos relatórios, utilizando os passos descritos na secção anterior.

* **Análise do provisionamento**: Este [livro](../app-provisioning/application-provisioning-log-analytics.md) mostra relatórios relacionados com a atividade de fornecimento de auditoria, tais como o número de novos utilizadores a provisionados e falhas no provisionamento, o número de utilizadores atualizados e falhas de atualização e o número de utilizadores desavisionados e falhas correspondentes.    
* **Eventos de início de sposição**: Este livro mostra os relatórios mais relevantes relacionados com a monitorização da atividade de início de sposição, tais como inserções por aplicação, utilizador, dispositivo, bem como uma visão sumária que rastreia o número de ins- ins ao longo do tempo.
* **Insights de acesso condicional**: Os insights de acesso condicional e [o livro](../conditional-access/howto-conditional-access-insights-reporting.md) de relatórios permitem-lhe compreender o impacto das políticas de Acesso Condicional na sua organização ao longo do tempo. 

## <a name="next-steps"></a>Passos seguintes

* [Começar com consultas em registos do Azure Monitor](../../azure-monitor/logs/get-started-queries.md)
* [Criar e gerir grupos de alerta no portal Azure](../../azure-monitor/alerts/action-groups.md)
* [Instale e utilize as vistas de análise de registo para o Azure Ative Directory](howto-install-use-log-analytics-views.md)