---
title: Como instalar e usar as exibições do log Analytics | Microsoft Docs
description: Saiba como instalar e usar as exibições do log Analytics para Azure Active Directory
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: 00bca8bca19f9a71c4eeb639017845987682661f
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820905"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalar e usar as exibições do log Analytics para Azure Active Directory

As exibições Azure Active Directory log Analytics ajudam a analisar e Pesquisar os logs de atividades do Azure AD em seu locatário do Azure AD. Os logs de atividades do Azure AD incluem:

* Logs de auditoria: o [relatório de atividade de logs de auditoria](concept-audit-logs.md) fornece acesso ao histórico de todas as tarefas que são executadas em seu locatário.
* Logs de entrada: com o [relatório de atividade de entrada](concept-sign-ins.md), você pode determinar quem executou as tarefas que são relatadas nos logs de auditoria.

## <a name="prerequisites"></a>Pré-requisitos

Para usar as exibições do log Analytics, você precisa de:

* Um espaço de trabalho Log Analytics em sua assinatura do Azure. Saiba como [criar um espaço de trabalho log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Primeiro, conclua as etapas para [rotear os logs de atividade do Azure ad para seu espaço de trabalho log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Baixe os modos de exibição do [repositório GitHub](https://aka.ms/AADLogAnalyticsviews) para o computador local.

## <a name="install-the-log-analytics-views"></a>Instalar as exibições do log Analytics

1. Navegue até seu espaço de trabalho do Log Analytics. Para fazer isso, primeiro navegue até a [portal do Azure](https://portal.azure.com) e selecione **todos os serviços**. Digite **log Analytics** na caixa de texto e selecione **log Analytics espaços de trabalho**. Selecione o espaço de trabalho para o qual você direcionou os logs de atividade, como parte dos pré-requisitos.
2. Selecione **Designer de exibição**, selecione **importar** e, em seguida, selecione **escolher arquivo** para importar as exibições do computador local.
3. Selecione as exibições que você baixou dos pré-requisitos e selecione **salvar** para salvar a importação. Faça isso para a exibição de **eventos de provisionamento de conta do Azure ad** e a exibição de **eventos** de entradas.

## <a name="use-the-views"></a>Usar as exibições

1. Navegue até seu espaço de trabalho do Log Analytics. Para fazer isso, primeiro navegue até a [portal do Azure](https://portal.azure.com) e selecione **todos os serviços**. Digite **log Analytics** na caixa de texto e selecione **log Analytics espaços de trabalho**. Selecione o espaço de trabalho para o qual você direcionou os logs de atividade, como parte dos pré-requisitos.

2. Quando estiver no espaço de trabalho, selecione **Resumo do espaço de trabalho**. Você deve ver os três seguintes modos de exibição:

    * **Eventos de provisionamento de conta do Azure ad**: esta exibição mostra relatórios relacionados à atividade de provisionamento de auditoria, como o número de novos usuários com falhas de provisionamento e provisionamento, o número de usuários atualizados e falhas de atualização e o número de usuários desprovisionadas e falhas correspondentes.    
    * **Eventos de entrada**: essa exibição mostra os relatórios mais relevantes relacionados à atividade de entrada de monitoramento, como entradas por aplicativo, usuário, dispositivo, bem como uma exibição de resumo que controla o número de entradas ao longo do tempo.

3. Selecione uma dessas exibições para ir para os relatórios individuais. Você também pode definir alertas em qualquer um dos parâmetros de relatório. Por exemplo, vamos definir um alerta para cada vez que houver um erro de entrada. Para fazer isso, primeiro selecione o modo de exibição de **eventos** de entradas, selecione o relatório **erros de entrada ao longo do tempo** e, em seguida, selecione **análise** para abrir a página de detalhes, com a consulta real por trás do relatório. 

    ![Detalhes](./media/howto-install-use-log-analytics-views/details.png)


4. Selecione **definir alerta**e, em seguida, selecione **sempre que a pesquisa de logs personalizada for &lt;lógica&gt;da** na seção **critérios de alerta** . Como queremos alertar sempre que houver um erro de entrada, defina o **limite** da lógica de alerta padrão como **1** e, em seguida, selecione **concluído**. 

    ![Configurar lógica de sinal](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Insira um nome e uma descrição para o alerta e defina a severidade como **aviso**.

    ![Criar regra](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecione o grupo de ações para alertar. Em geral, isso pode ser uma equipe que você deseja notificar por email ou mensagem de texto, ou pode ser uma tarefa automatizada usando WebHooks, runbooks, funções, aplicativos lógicos ou soluções de ITSM externas. Saiba como [criar e gerenciar grupos de ações no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Selecione **criar regra de alerta** para criar o alerta. Agora você será alertado toda vez que houver um erro de entrada.

## <a name="next-steps"></a>Passos seguintes

* [Como analisar logs de atividade com logs de Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Introdução aos logs de Azure Monitor no portal do Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)