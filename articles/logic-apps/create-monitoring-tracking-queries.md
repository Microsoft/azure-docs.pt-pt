---
title: Ver e criar consultas para aplicações lógicas em registos do Azure Monitor
description: Ver e criar consultas em registos do Azure Monitor para Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: d1f575d9d588ccd99523c6b6d5d2ceba6369dc3d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309920"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Ver e criar consultas para monitorização e rastreio em registos do Azure Monitor para Apps Azure Logic

Pode ver as consultas subjacentes que produzem os resultados dos registos do [Azure Monitor](../azure-monitor/log-query/log-query-overview.md) e criar consultas que filtram os resultados com base nos seus critérios específicos. Por exemplo, pode encontrar mensagens com base num número específico de controlo de intercâmbio. As consultas utilizam o [idioma de consulta Kusto,](/azure/data-explorer/kusto/query/)que pode editar se quiser ver diferentes resultados. Para obter mais informações, consulte [as consultas de registo do Azure Monitor](/azure/data-explorer/kusto/query/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Log Analytics. Se não tiver um espaço de trabalho log analytics, aprenda [a criar um espaço de trabalho Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Uma aplicação lógica que foi criada com o registo do Azure Monitor e envia essa informação para um espaço de trabalho do Log Analytics. Saiba [como configurar os registos do Azure Monitor para a sua aplicação lógica.](../logic-apps/monitor-logic-apps.md)

* Se estiver a utilizar uma conta de integração, certifique-se de que criou a conta com o Azure Monitor para enviar essa informação para um espaço de trabalho do Log Analytics. Saiba como configurar o [registo do Azure Monitor para a sua conta de integração.](../logic-apps/monitor-b2b-messages-log-analytics.md)

## <a name="view-queries-behind-results"></a>Ver consultas por trás dos resultados

Para ver ou editar a consulta que produz os resultados no resumo do seu espaço de trabalho, siga estes passos:

1. Em qualquer página de resultados, na parte inferior, selecione **Ver Tudo**.

   ![Ver todos os resultados](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   A página 'Logs' abre e mostra a consulta por trás da página de resultados anteriores.

   ![Página de registos - vista de consulta](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na página **'Registar'** pode selecionar estas opções:

   * Para ver os resultados da consulta como uma tabela, sob o editor de consulta, selecione **Tabela**.

   * Para alterar a consulta, atualize a cadeia de consulta e selecione **Executar** para ver os resultados na tabela.

## <a name="create-your-own-query"></a>Crie a sua própria consulta

Para encontrar ou filtrar resultados com base em propriedades ou valores específicos, pode criar a sua própria consulta a partir de uma consulta vazia ou utilizar uma consulta existente. Para obter mais informações, consulte [Começar com consultas de registo no Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. No [portal Azure,](https://portal.azure.com)encontre e selecione o seu espaço de trabalho Log Analytics.

1. No menu do seu espaço de trabalho, em **Geral,** selecione **Logs**.

1. Comece por uma consulta vazia ou qualquer consulta disponível.

   * Para verificar se existem consultas existentes, na barra de ferramentas de consulta, selecione ou **as consultas de**  >  **amostras Histórico**, que mostra consultas de consultas anteriores, ou selecione Explorador de **Consultas**, que mostra consultas pré-construídas.

     Por exemplo, a solução Logic Apps B2B fornece estas consultas pré-construídas:

     ![Comece com a solução "Logic Apps B2B" consultas pré-construídas](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Para começar de uma consulta vazia, no editor de consulta, comece a escrever a [linguagem de consulta Kusto](/azure/data-explorer/kusto/query/) para a sua consulta.

     ![Comece com consulta vazia](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Passos seguintes

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de rastreio personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)