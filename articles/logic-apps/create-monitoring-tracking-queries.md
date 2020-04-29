---
title: Ver e criar consultas para aplicações lógicas em registos do Monitor Do Azure
description: Ver e criar consultas em registos do Monitor Azure para aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76908075"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Ver e criar consultas para monitorização e rastreio em registos do Monitor Azure para aplicações lógicas azure

Pode ver as consultas subjacentes que produzem os resultados dos registos do [Monitor Azure](../log-analytics/log-analytics-overview.md) e criar consultas que filtram os resultados baseados nos seus critérios específicos. Por exemplo, pode encontrar mensagens com base num número específico de controlo de intercâmbio. As consultas usam a linguagem de [consulta Kusto](https://aka.ms/LogAnalyticsLanguageReference), que pode editar se quiser ver resultados diferentes. Para mais informações, consulte consultas de [registo do Monitor Azure](../azure-monitor/log-query/query-language.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Log Analytics. Se não tiver um espaço de trabalho de Log Analytics, aprenda [a criar um espaço](../azure-monitor/learn/quick-create-workspace.md)de trabalho log Analytics .

* Uma aplicação lógica que é configurada com o registo do Monitor Azure e envia essa informação para um espaço de trabalho do Log Analytics. Aprenda [a configurar registos do Monitor Azure para a sua aplicação lógica](../logic-apps/monitor-logic-apps.md).

* Se estiver a utilizar uma conta de integração, certifique-se de que criou a conta com o Registo De Acesso ao Azure Monitor para enviar essa informação para um espaço de trabalho do Log Analytics. Aprenda a configurar o registo do Monitor Azure para a sua conta de [integração.](../logic-apps/monitor-b2b-messages-log-analytics.md)

## <a name="view-queries-behind-results"></a>Ver consultas por trás dos resultados

Para visualizar ou editar a consulta que produz os resultados no resumo do seu espaço de trabalho, siga estes passos:

1. Em qualquer página de resultados, na parte inferior, selecione **Ver Tudo**.

   ![Ver todos os resultados](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   A página Logs abre e mostra a consulta por trás da página de resultados anteriores.

   ![Página de logs - vista de consulta](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na página **De Registos,** pode selecionar estas opções:

   * Para ver os resultados da consulta como uma tabela, sob o editor de consulta, selecione **Tabela**.

   * Para alterar a consulta, atualize a cadeia de consulta e selecione **Executar** para visualizar os resultados na tabela.

## <a name="create-your-own-query"></a>Crie a sua própria consulta

Para encontrar ou filtrar resultados com base em propriedades ou valores específicos, pode criar a sua própria consulta partindo de uma consulta vazia ou utilizar uma consulta existente. Para mais informações, consulte [Começar com consultas de log no Monitor Azure](../azure-monitor/log-query/get-started-queries.md).

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o seu espaço de trabalho Log Analytics.

1. No menu do espaço de trabalho, em **Geral,** selecione **Registos**.

1. Comece por uma consulta vazia ou quaisquer consultas disponíveis.

   * Para verificar se existem consultas existentes, na barra de ferramentas de consulta, selecione ou **Amostras consultas** > **História**, que mostra consultas de consultas anteriores, ou selecione **Query Explorer**, que mostra consultas pré-construídas.

     Por exemplo, a solução Logic Apps B2B fornece estas consultas pré-construídas:

     ![Comece com a solução "Logic Apps B2B" pré-construída consultas](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Para começar de uma consulta vazia, no editor de consulta, comece a digitar a linguagem de [consulta Kusto](../azure-monitor/log-query/query-language.md) para a sua consulta.

     ![Comece com consulta vazia](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Passos seguintes

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de rastreio personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)