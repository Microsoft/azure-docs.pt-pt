---
title: Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure
description: As notificações de saúde do serviço permitem-lhe visualizar mensagens de saúde de serviço publicadas pelo Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75748648"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

As notificações de saúde de serviço são publicadas pela infraestrutura Azure no registo de [atividade sinuosa do Azure.](../azure-monitor/platform/platform-logs-overview.md)  As notificações contêm informações sobre os recursos sob a sua subscrição. Dado o possível grande volume de informação armazenada no registo de atividade, existe uma interface separada do utilizador para facilitar a visualização e configuração de alertas sobre notificações de saúde de serviço. 

As notificações de saúde do serviço podem ser informativas ou exequíveis, dependendo da classe.

Para obter mais informações sobre as várias classes de notificações de saúde de serviço, consulte as propriedades das notificações de saúde do [Serviço.](service-health-notifications-properties.md)

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Consulte as notificações de saúde do seu serviço no portal Azure

1. No [portal Azure,](https://portal.azure.com)selecione **Monitor**.

    ![Screenshot do menu do portal Azure, com Monitor selecionado](./media/service-notifications/home-monitor.png)

    O Monitor Azure reúne todas as suas definições de monitorização e dados numa única vista consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

1. Selecione **Alertas**.

    ![Screenshot do registo de atividade do monitor, com alertas selecionados](./media/service-notifications/service-health-summary.png)

1. Selecione **+Adicionar alerta**de registo de atividade , e configurar um alerta para garantir que é notificado para futuras notificações de serviço. Para mais informações, consulte Criar alertas de registo de [atividade sonantes em notificações](../azure-monitor/platform/alerts-activity-log-service-notifications.md)de serviço .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [alertas](../azure-monitor/platform/activity-log-alerts.md)de registo de atividade .
