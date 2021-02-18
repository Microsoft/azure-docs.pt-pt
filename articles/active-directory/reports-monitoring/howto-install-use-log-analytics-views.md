---
title: Como instalar e utilizar as vistas de análise de registo | Microsoft Docs
description: Saiba como instalar e utilizar as vistas de análise de registo para o Azure Ative Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: 86ad698793d562f93f9972903ca21e50c209c79c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580008"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instale e utilize as vistas de análise de registo para o Azure Ative Directory

As vistas de análise de registo de registo do Azure Ative Informação ajudam-no a analisar e a pesquisar os registos de atividades da AD Azure no seu inquilino Azure AD. Os registos de atividades da AD Azure incluem:

* Registos de auditoria: O relatório de [atividade dos registos](concept-audit-logs.md) de auditoria dá-lhe acesso ao histórico de todas as tarefas realizadas no seu inquilino.
* Registos de início de sessão: Com o [relatório de atividade de inscrição,](concept-sign-ins.md)pode determinar quem executou as tarefas que são reportadas nos registos de auditoria.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as vistas de análise de registo, precisa de:

* Um espaço de trabalho Log Analytics na sua subscrição Azure. Saiba como [criar um espaço de trabalho Log Analytics.](../../azure-monitor/logs/quick-create-workspace.md)
* Em primeiro lugar, complete os passos para [encaminhar os registos de atividade azure AD para o seu espaço de trabalho Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Faça o download das vistas do [repositório GitHub](https://aka.ms/AADLogAnalyticsviews) para o seu computador local.

## <a name="install-the-log-analytics-views"></a>Instale as vistas de análise de registo

1. Navegue para o seu espaço de trabalho Log Analytics. Para isso, navegue primeiro para o [portal Azure](https://portal.azure.com) e selecione **Todos os serviços**. Digite **'Registar Analytics'** na caixa de texto e selecione **log analytics espaços de trabalho**. Selecione o espaço de trabalho para onde encaminhou os registos de atividade, como parte dos pré-requisitos.
2. Selecione **Ver Designer,** selecione **Importar** e, em seguida, **selecione Choose File** para importar as vistas do seu computador local.
3. Selecione as vistas que descarregou a partir dos pré-requisitos e **selecione Guardar** para salvar a importação. Faça-o para a vista **Azure AD Account Provisioning Events** e a vista **Eventos de Inscrição.**

## <a name="use-the-views"></a>Use as vistas

1. Navegue para o seu espaço de trabalho Log Analytics. Para isso, navegue primeiro para o [portal Azure](https://portal.azure.com) e selecione **Todos os serviços**. Digite **'Registar Analytics'** na caixa de texto e selecione **log analytics espaços de trabalho**. Selecione o espaço de trabalho para onde encaminhou os registos de atividade, como parte dos pré-requisitos.

2. Uma vez no espaço de trabalho, selecione **Workspace Summary**. Deve ver as seguintes três vistas:

    * **Eventos de Provisionamento de Conta AZure AD**: Esta visão mostra relatórios relacionados com a atividade de provisionamento de auditoria, tais como o número de novos utilizadores a provisionados e falhas de provisionamento, o número de utilizadores atualizados e as falhas de atualização e o número de utilizadores desavisionados e falhas correspondentes.    
    * **Eventos de início de** sposição : Esta vista mostra os relatórios mais relevantes relacionados com a monitorização da atividade de início de sposição, tais como inserções por aplicação, utilizador, dispositivo, bem como uma visão sumária que rastreia o número de ins-ins ao longo do tempo.

3. Selecione qualquer uma destas vistas para saltar para os relatórios individuais. Também pode definir alertas em qualquer um dos parâmetros do relatório. Por exemplo, vamos alertar para cada vez que há um erro de inscrição. Para isso, selecione primeiro a visualização **de Eventos de Início,** selecione **erros de início de sômbro** ao longo do tempo e, em seguida, selecione **Analytics** para abrir a página de detalhes, com a consulta real por trás do relatório. 

    ![A screenshot mostra a página de detalhes do Analytics que tem a consulta para o relatório.](./media/howto-install-use-log-analytics-views/details.png)


4. Selecione **'Alerta'** e, em seguida, selecione **Sempre que a pesquisa de registo personalizado estiver &lt; indefinida &gt;** na secção de **critérios de alerta.** Uma vez que queremos alertar sempre que houver um erro de entrada, descreva o **Limiar** da lógica de alerta predefinido para **1** e, em seguida, selecione **'Fazer' ( 'Fazer').** 

    ![Configurar lógica de sinal](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Introduza um nome e descrição para o alerta e desema esta medida de **gravidade** para Aviso .

    ![Criar regra](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecione o grupo de ação para alertar. Em geral, esta pode ser uma equipa que pretende notificar via e-mail ou mensagem de texto, ou pode ser uma tarefa automatizada usando webhooks, runbooks, funções, aplicações lógicas ou soluções ITSM externas. Saiba como [criar e gerir grupos de ação no portal Azure.](../../azure-monitor/alerts/action-groups.md)

7. Selecione **Criar a regra de alerta** para criar o alerta. Agora será alertado sempre que houver um erro de inscrição.

## <a name="next-steps"></a>Passos seguintes

* [Como analisar registos de atividade com registos do Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Começa com registos do Azure Monitor no portal Azure](../../azure-monitor/logs/log-analytics-tutorial.md)