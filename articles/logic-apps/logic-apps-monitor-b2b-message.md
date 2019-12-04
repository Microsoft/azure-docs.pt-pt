---
title: Monitorar mensagens B2B com Azure Monitor
description: Configurar o log de diagnóstico para mensagens AS2, X12 e EDIFACT nos aplicativos lógicos do Azure usando Azure Monitor
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: cd96376f764ec3075b916bf2207ec6ee3dd3fcbd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791934"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Configurar o log de diagnóstico para mensagens B2B em aplicativos lógicos do Azure usando Azure Monitor

Depois de configurar a comunicação B2B entre parceiros comerciais em sua conta de integração, esses parceiros podem trocar mensagens entre si. Para verificar se essa comunicação funciona da maneira esperada, você pode monitorar as mensagens AS2, X12 e EDIFACT e configurar o log de diagnóstico para sua conta de integração com [logs de Azure monitor](../log-analytics/log-analytics-overview.md). Esse serviço monitora seus ambientes locais e de nuvem, ajuda você a manter a disponibilidade e o desempenho, além de coletar detalhes e eventos de tempo de execução para uma depuração mais avançada. Você também pode usar esses dados com outros serviços, como o armazenamento do Azure e os hubs de eventos do Azure.

> [!NOTE]
> Esta página ainda pode ter referências a Microsoft Operations Management Suite (OMS), que está sendo [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), mas substitui essas etapas com o Azure log Analytics sempre que possível. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo lógico que é configurado com o log de diagnóstico. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Depois de atender aos requisitos anteriores, você também precisa de um espaço de trabalho Log Analytics, que você usa para monitorar e controlar a comunicação B2B por meio de logs de Azure Monitor. Se você não tiver um espaço de trabalho Log Analytics, saiba [como criar um espaço de trabalho do log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Uma conta de integração que está vinculada ao seu aplicativo lógico. Saiba [como criar uma conta de integração com um link para seu aplicativo lógico](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Ativar o log de diagnósticos

Você pode ativar o registro em log diretamente de sua conta de integração ou [por meio do serviço de Azure monitor](#azure-monitor-service). Azure Monitor fornece monitoramento básico com dados de nível de infraestrutura. Saiba mais sobre [Azure monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Ativar o registro em log da conta de integração

1. Na [portal do Azure](https://portal.azure.com), localize e selecione sua conta de integração. Em **monitoramento**, selecione **configurações de diagnóstico**.

   ![Localize e selecione sua conta de integração, selecione "configurações de diagnóstico"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Agora, localize e selecione sua conta de integração. Em listas de filtros, selecione os valores que se aplicam à sua conta de integração.
Quando terminar, escolha **Adicionar configuração de diagnóstico**.

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Subscrição** | <*Azure-subscription-name*> | A assinatura do Azure associada à sua conta de integração | 
   | **Grupo de recursos** | <*Azure-Resource-Group-name*> | O grupo de recursos do Azure para sua conta de integração | 
   | **Tipo de recurso** | **Contas de integração** | O tipo para o recurso do Azure no qual você deseja ativar o registro em log | 
   | **Recurso** | <*Integration-nome da conta*> | O nome do recurso do Azure no qual você deseja ativar o registro em log | 
   ||||  

   Por exemplo:

   ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Forneça um nome para a nova configuração de diagnóstico e selecione seu Log Analytics espaço de trabalho e os dados que você deseja registrar.

   1. Selecione **Enviar para log Analytics**. 

   1. Em **log Analytics**, selecione **Configurar**. 

   1. Em **espaços de trabalho do OMS**, selecione o log Analytics espaço de trabalho que você deseja usar para o registro em log. 

      > [!NOTE]
      > Os espaços de trabalho do OMS estão sendo substituídos por espaços de trabalho do Log Analytics. 

   1. Em **log**, selecione a categoria **IntegrationAccountTrackingEvents** e escolha **salvar**.

   Por exemplo: 

   ![Configurar logs de Azure Monitor para que você possa enviar dados de diagnóstico para um log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Agora, [Configure o acompanhamento para suas mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Ativar o registro em log por meio de Azure Monitor

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **Monitor**. Em **configurações**, selecione **configurações de diagnóstico**. 

   ![Selecione "monitorar" > "configurações de diagnóstico" > sua conta de integração](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Agora, localize e selecione sua conta de integração. Em listas de filtros, selecione os valores que se aplicam à sua conta de integração.
Quando terminar, escolha **Adicionar configuração de diagnóstico**.

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Subscrição** | <*Azure-subscription-name*> | A assinatura do Azure associada à sua conta de integração | 
   | **Grupo de recursos** | <*Azure-Resource-Group-name*> | O grupo de recursos do Azure para sua conta de integração | 
   | **Tipo de recurso** | **Contas de integração** | O tipo para o recurso do Azure no qual você deseja ativar o registro em log | 
   | **Recurso** | <*Integration-nome da conta*> | O nome do recurso do Azure no qual você deseja ativar o registro em log | 
   ||||  

   Por exemplo:

   ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Forneça um nome para a nova configuração de diagnóstico e selecione seu Log Analytics espaço de trabalho e os dados que você deseja registrar.

   1. Selecione **Enviar para log Analytics**. 

   1. Em **log Analytics**, selecione **Configurar**. 

   1. Em **espaços de trabalho do OMS**, selecione o log Analytics espaço de trabalho que você deseja usar para o registro em log. 

      > [!NOTE]
      > Os espaços de trabalho do OMS estão sendo substituídos por espaços de trabalho do Log Analytics. 

   1. Em **log**, selecione a categoria **IntegrationAccountTrackingEvents** e escolha **salvar**.

   Por exemplo: 

   ![Configurar logs de Azure Monitor para que você possa enviar dados de diagnóstico para um log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Agora, [Configure o acompanhamento para suas mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Usar dados de diagnóstico com outros serviços

Juntamente com os logs de Azure Monitor, você pode estender como usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo: 

* [Arquivar logs de Diagnóstico do Azure no armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Transmitir logs de Diagnóstico do Azure para os hubs de eventos do Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Em seguida, você pode obter o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power bi](../azure-monitor/platform/powerbi.md). Por exemplo:

* [Transmitir dados de hubs de eventos para Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analise dados de streaming com Stream Analytics e crie um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nas opções que você deseja configurar, certifique-se de primeiro [criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, você pode selecionar os destinos para os quais deseja enviar dados de diagnóstico.
Os períodos de retenção se aplicam somente quando você opta por usar uma conta de armazenamento.

![Enviar dados para a conta de armazenamento do Azure ou Hub de eventos](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Esquemas de acompanhamento com suporte

O Azure dá suporte a esses tipos de esquema de controle, que todos têm esquemas fixos, exceto o tipo personalizado.

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Personalizar esquema de controlo](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Rastrear mensagens B2B em logs de Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Rastrear mensagens B2B em logs de Azure Monitor")
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

