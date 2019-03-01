---
title: Monitorizar mensagens B2B com os registos do Azure Monitor - Azure Logic Apps | Documentos da Microsoft
description: Monitorizar AS2, X12 e EDIFACT as mensagens para contas de integração e Azure Logic Apps e configurar o registo de diagnóstico com os registos do Azure Monitor
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195171"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Monitorizar mensagens B2B com os registos do Azure Monitor no Azure Logic Apps

Depois de definir a comunicação de B2B entre parceiros comerciais na sua conta de integração, os parceiros podem trocar mensagens entre si. Para verificar se esta comunicação funciona como esperado, pode monitorizar AS2, X12, e EDIFACT mensagens e configurar para a sua conta de integração com o registo de diagnósticos [registos do Azure Monitor](../log-analytics/log-analytics-overview.md). Este serviço monitoriza os seus ambientes na cloud e no local, o ajudam a manter a disponibilidade e desempenho e recolhe detalhes de tempo de execução e os eventos de depuração mais avançada. Também pode utilizar estes dados com outros serviços como o armazenamento do Azure e os Hubs de eventos do Azure.

> [!NOTE]
> Esta página ainda pode ter referências para Microsoft Operations Management Suite (OMS), que é [extinguir em Janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), mas substitui esses passos com o Azure Log Analytics, sempre que possível. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação de lógica que está configurada com o registo de diagnósticos. Saiba mais [como criar uma aplicação lógica](quickstart-create-first-logic-app-workflow.md) e [como configurar o registo para essa aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Depois de cumprir os requisitos anteriores, também precisa de uma área de trabalho do Log Analytics, que são utilizados para monitorizar e a controlar B2B comunicação através de registos do Azure Monitor. Se não tiver uma área de trabalho do Log Analytics, saiba [como criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Uma conta de integração que está ligada à sua aplicação lógica. Saiba mais [como criar uma conta de integração com uma ligação à sua aplicação lógica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Ativar o registo de diagnósticos

Pode ativar o registo diretamente a partir de sua conta de integração ou [através do serviço do Azure Monitor](#azure-monitor-service). O Azure Monitor proporciona monitorização básica com dados de nível de infra-estrutura. Saiba mais sobre [do Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Ativar o registo da conta de integração

1. Na [portal do Azure](https://portal.azure.com), localize e selecione a sua conta de integração. Sob **monitorização**, selecione **das definições de diagnóstico**.

   ![Localize e selecione a sua conta de integração, selecione "Definições de diagnóstico"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Agora, localize e selecione a sua conta de integração. Nas listas de filtro, selecione os valores que se aplicam à sua conta de integração.
Quando tiver terminado, escolha **Adicionar definição de diagnóstico**.

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------|
   | **Subscrição** | <*Azure-subscription-name*> | A subscrição do Azure que está associada à sua conta de integração | 
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O grupo de recursos do Azure para a sua conta de integração | 
   | **Tipo de recurso** | **Contas de integração** | O tipo para o qual pretende ativar o registo de recurso do Azure | 
   | **Recurso** | <*integration-account-name*> | O nome do seu recurso do Azure onde pretende ativar o registo | 
   ||||  

   Por exemplo:

   ![Configurar diagnósticos para a sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Forneça um nome para a sua nova definição de diagnóstico e selecione a área de trabalho do Log Analytics e os dados para registrar em log.

   1. Selecione **enviar para o Log Analytics**. 

   1. Sob **do Log Analytics**, selecione **configurar**. 

   1. Sob **áreas de trabalho do OMS**, selecione a área de trabalho do Log Analytics que pretende utilizar para o registo. 

      > [!NOTE]
      > Áreas de trabalho do OMS estão a ser substituídas por áreas de trabalho do Log Analytics. 

   1. Sob **Log**, selecione a **IntegrationAccountTrackingEvents** categoria e escolha **guardar**.

   Por exemplo: 

   ![Configurar os registos do Azure Monitor para que possa enviar dados de diagnóstico para um registo](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Agora [configurar o controlo para as mensagens B2B nos registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Ativar o registo através do Azure Monitor

1. Na [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **Monitor**. Sob **configurações**, selecione **as definições de diagnóstico**. 

   ![Selecione "Monitor" > "As definições de diagnóstico" > sua conta de integração](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Agora, localize e selecione a sua conta de integração. Nas listas de filtro, selecione os valores que se aplicam à sua conta de integração.
Quando tiver terminado, escolha **Adicionar definição de diagnóstico**.

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------|
   | **Subscrição** | <*Azure-subscription-name*> | A subscrição do Azure que está associada à sua conta de integração | 
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O grupo de recursos do Azure para a sua conta de integração | 
   | **Tipo de recurso** | **Contas de integração** | O tipo para o qual pretende ativar o registo de recurso do Azure | 
   | **Recurso** | <*integration-account-name*> | O nome do seu recurso do Azure onde pretende ativar o registo | 
   ||||  

   Por exemplo:

   ![Configurar diagnósticos para a sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Forneça um nome para a sua nova definição de diagnóstico e selecione a área de trabalho do Log Analytics e os dados para registrar em log.

   1. Selecione **enviar para o Log Analytics**. 

   1. Sob **do Log Analytics**, selecione **configurar**. 

   1. Sob **áreas de trabalho do OMS**, selecione a área de trabalho do Log Analytics que pretende utilizar para o registo. 

      > [!NOTE]
      > Áreas de trabalho do OMS estão a ser substituídas por áreas de trabalho do Log Analytics. 

   1. Sob **Log**, selecione a **IntegrationAccountTrackingEvents** categoria e escolha **guardar**.

   Por exemplo: 

   ![Configurar os registos do Azure Monitor para que possa enviar dados de diagnóstico para um registo](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Agora [configurar o controlo para as mensagens B2B nos registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Utilizar dados de diagnóstico com outros serviços

Juntamente com os registos do Azure Monitor, pode expandir como utilizar dados de diagnóstico da sua aplicação lógica com outros serviços do Azure, por exemplo: 

* [Arquivar registos do diagnóstico do Azure no armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Registos de diagnóstico do Azure Stream para os Hubs de eventos do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Pode, em seguida, a monitorização utilizando a telemetria e de análise a partir de outros serviços, como get em tempo real [do Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../azure-monitor/platform/powerbi.md). Por exemplo:

* [Dados de Stream dos Hubs de eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de transmissão em fluxo com o Stream Analytics e criar um dashboard de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nas opções do mesmo conjunto de cópia de segurança, certifique-se de que primeiro [criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, pode selecionar os destinos em que pretende enviar dados de diagnóstico.
Os períodos de retenção aplicam-se apenas quando optar por utilizar uma conta de armazenamento.

![Enviar dados para o hub de evento ou a conta de armazenamento do Azure](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Suporte a esquemas de controlo

O Azure suporta estes tipos de esquema, os quais corrigiram esquemas, exceto o tipo personalizado de controlo.

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Personalizar esquema de controlo](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos Seguintes

* [Monitorizar mensagens B2B no registos do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "mensagens B2B de controle nos registos do Azure Monitor")
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

