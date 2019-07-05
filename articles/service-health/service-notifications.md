---
title: Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure
description: Notificações de estado de funcionamento do serviço permitem-lhe ver mensagens de estado de funcionamento de serviço publicadas pelo Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538357"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

Notificações de estado de funcionamento do serviço são publicadas pela infraestrutura do Azure para o [registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).  As notificações contêm informações sobre os recursos na sua subscrição. Tendo em conta, possivelmente, grande volume de informações armazenadas no registo de atividades, existe uma interface de utilizador em separado para que seja mais fácil ver e configurar alertas nas notificações de estado de funcionamento do serviço. 

Notificações de estado de funcionamento do serviço podem ser informativa ou passíveis de ação, dependendo da classe.

Para obter mais informações sobre as diversas classes de notificações de estado de funcionamento do serviço, consulte [propriedades de notificações de estado de funcionamento do serviço](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Ver as notificações de estado de funcionamento do serviço no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Menu do portal de captura de ecrã do Azure, com o Monitor selecionado](./media/service-notifications/home-monitor.png)

    O Azure Monitor reúne todas as suas monitorização definições e dados numa vista consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

1. Selecione **alertas**.

    ![Registo de atividades de captura de ecrã do Monitor, com os alertas selecionados](./media/service-notifications/service-health-summary.png)

1. Selecione **+ Adicionar alerta de registo de atividade**e configurar um alerta para garantir que é notificado para notificações de serviço futuras. Para obter mais informações, consulte [criar alertas do registo de atividade nas notificações do serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [alertas de registo de atividade](../azure-monitor/platform/activity-log-alerts.md).
