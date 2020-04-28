---
title: Analise os registos de atividade utilizando registos do Monitor Azure [ Microsoft Docs
description: Saiba como analisar registos de atividades do Diretório Ativo do Azure utilizando registos do Monitor Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74008277"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analise registos de atividade da Azure AD com registos do Monitor Azure

Depois de integrar os registos de atividade da [Azure AD com registos do Monitor Azure,](howto-integrate-activity-logs-with-log-analytics.md)pode utilizar a potência dos registos do Monitor Azure para obter informações sobre o seu ambiente. Também pode instalar as vistas de análise de Log para registos de atividade de [AD Azure](howto-install-use-log-analytics-views.md) para ter acesso a relatórios pré-construídos em torno de eventos de auditoria e login no seu ambiente.

Neste artigo, aprende a analisar os registos de atividade do Azure AD no seu espaço de trabalho Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para acompanhar, precisa de:

* Um espaço de trabalho log Analytics na sua subscrição Azure. Aprenda a criar um espaço de [trabalho log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Primeiro, complete os passos para encaminhar os registos de atividade da [AD Azure para o seu espaço](howto-integrate-activity-logs-with-log-analytics.md)de trabalho Log Analytics .
*  [Acesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) ao espaço de trabalho de análise de registo
* As seguintes funções no Diretório Ativo azure (se estiver a aceder ao Log Analytics através do portal Azure Ative Directory)
    - Administrador de Segurança
    - Leitor de Segurança
    - Leitor de Relatórios
    - Admin Global
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navegue para o espaço de trabalho log Analytics

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Selecione **Diretório Ativo Azure**e, em seguida, selecione **Registos** da secção **de Monitorização** para abrir o seu espaço de trabalho Log Analytics. O espaço de trabalho abrirá com uma consulta padrão.

    ![Consulta padrão](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Veja o esquema para registos de atividade da Azure AD

Os registos são empurrados para as **tabelas De Registos e** **SigninLogs** no espaço de trabalho. Para ver o esquema para estas tabelas:

1. A partir da vista de consulta padrão na secção anterior, selecione **Schema** e expanda o espaço de trabalho. 

2. Expanda a secção de Gestão de **Registos** e, em seguida, expanda os **Registos de Auditoria** ou os **SignInLogs** para visualizar o esquema de registo.
    ![Registos](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![de auditoria Registos Signin](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Consulta dos registos de atividade da AD Azure

Agora que tem os registos no seu espaço de trabalho, agora pode fazer perguntas contra eles. Por exemplo, para obter as aplicações de topo usadas na semana passada, substitua a consulta predefinida pelo seguinte e selecione **Executar**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Para obter os principais eventos de auditoria durante a última semana, use a seguinte consulta:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alerta sobre dados de registo de atividade da AD Azure

Também pode configurar alertas sobre a sua consulta. Por exemplo, configurar um alerta quando mais de 10 aplicações foram utilizadas na última semana:

1. A partir do espaço de trabalho, selecione **'Definir alerta'** para abrir a página **de regra Criar.**

    ![Definir alerta](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selecione os critérios de **alerta predefinidos criados** no alerta e atualize o **Limiar** na métrica padrão para 10.

    ![Critérios de alerta](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Introduza um nome e descrição para o alerta e escolha o nível de gravidade. Por exemplo, poderíamos defini-lo para **Informacional.**

4. Selecione o Grupo de **Ação** que será alertado quando o sinal ocorrer. Pode optar por notificar a sua equipa por e-mail ou mensagem de texto, ou pode automatizar a ação utilizando webhooks, funções Azure ou aplicações lógicas. Saiba mais sobre a criação e gestão de grupos de [alerta no portal Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)

5. Depois de configurar o alerta, selecione **Criar alerta** para o ativar. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instale e utilize vistas pré-construídas para registos de atividade da Azure AD

Também pode descarregar as vistas de análise de log pré-construídas para registos de atividade saqueada pelo Azure AD. Os pontos de vista fornecem vários relatórios relacionados com cenários comuns que envolvem auditoria e eventos de inscrição. Também pode alertar sobre qualquer um dos dados fornecidos nos relatórios, utilizando os passos descritos na secção anterior.

* Eventos de Provisionamento de **Conta AD Azure**: Esta visão mostra relatórios relacionados com a atividade de fornecimento de auditoria, tais como o número de novos utilizadores aprovisionados e falhas de provisionamento, número de utilizadores atualizados e falhas de atualização e o número de utilizadores desprovisionados e correspondentes falhas.    
* **Eventos de inscrição**: Esta visão mostra os relatórios mais relevantes relacionados com a monitorização da atividade de inscrição, tais como inscrições por aplicação, utilizador, dispositivo, bem como uma visão sumária que rastreia o número de inscrições ao longo do tempo.
* **Utilizadores Executando o Consentimento**: Esta visualização mostra relatórios relacionados com o consentimento do utilizador, tais como as subvenções de consentimento por utilizador, inscrições por utilizadores que concederam o consentimento, bem como inscrições por aplicação para todas as aplicações baseadas em consentimento. 

Aprenda a instalar e utilizar vistas de análise de [registos para registos de atividade da Azure AD](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Passos seguintes

* [Começar com consultas em registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Criar e gerir grupos de alerta no portal Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instale e utilize as vistas de análise de registo para o Diretório Ativo Azure](howto-install-use-log-analytics-views.md)
