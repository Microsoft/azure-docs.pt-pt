---
title: Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure
description: As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748648"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

As notificações de integridade do serviço são publicadas pela infraestrutura do Azure no [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).  As notificações contêm informações sobre os recursos em sua assinatura. Considerando o volume possivelmente grande de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas sobre notificações de integridade do serviço. 

As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Para obter mais informações sobre as várias classes de notificações de integridade do serviço, consulte [Propriedades de notificações de integridade do serviço](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Exibir suas notificações de integridade do serviço no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Captura de tela do menu portal do Azure, com o monitor selecionado](./media/service-notifications/home-monitor.png)

    Azure Monitor reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

1. Selecione **alertas**.

    ![Captura de tela de monitorar log de atividades, com alertas selecionados](./media/service-notifications/service-health-summary.png)

1. Selecione **+ adicionar alerta do log de atividades**e configure um alerta para garantir que você seja notificado para futuras notificações de serviço. Para obter mais informações, consulte [criar alertas do log de atividades em notificações de serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md).
