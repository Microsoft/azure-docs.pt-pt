---
title: Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure
description: Notificações de estado de funcionamento do serviço permitem-lhe ver mensagens de estado de funcionamento de serviço publicadas pelo Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069343"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

Notificações de estado de funcionamento do serviço são publicadas pela infraestrutura do Azure. Eles contêm informações sobre os recursos na sua subscrição. Estas notificações são uma classe secundárias da atividade de registo de eventos e, por isso, também podem ser encontradas no registo de atividades. Notificações de estado de funcionamento do serviço podem ser informativa ou passíveis de ação, dependendo da classe.

Para obter mais informações sobre as diversas classes de notificações de estado de funcionamento do serviço, consulte [propriedades de notificações de estado de funcionamento do serviço](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Ver as notificações de estado de funcionamento do serviço no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Menu do portal de captura de ecrã do Azure, com o Monitor selecionado](./media/service-notifications/home-monitor.png)

    O Azure Monitor reúne todas as suas monitorização definições e dados numa vista consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

1. Selecione **alertas**.

    ![Registo de atividades de captura de ecrã do Monitor, com os alertas selecionados](./media/service-notifications/service-health-summary.png)

1. Selecione **+ Adicionar alerta de registo de atividade**e configurar um alerta para garantir que é notificado para notificações de serviço futuras. Para obter mais informações, consulte [criar alertas do registo de atividade nas notificações do serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Passos Seguintes

* Receber [sempre que uma notificação de estado de funcionamento do serviço de notificações de alertas](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) é publicado.  
* Saiba mais sobre [alertas de registo de atividade](../../azure-monitor/platform/activity-log-alerts.md).
