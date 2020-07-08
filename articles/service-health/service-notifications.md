---
title: Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure
description: As notificações de saúde do serviço permitem-lhe visualizar mensagens de saúde de serviço publicadas pela Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75748648"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Ver notificações do estado de funcionamento do serviço ao utilizar o portal do Azure

As notificações de saúde do serviço são publicadas pela infraestrutura Azure no registo de atividades do [Azure](../azure-monitor/platform/platform-logs-overview.md).  As notificações contêm informações sobre os recursos sob a sua subscrição. Dado o eventual grande volume de informação armazenada no registo de atividades, existe uma interface de utilizador separada para facilitar a visualização e a configuração de alertas nas notificações de saúde do serviço. 

As notificações de saúde do serviço podem ser informativas ou accuais, dependendo da classe.

Para obter mais informações sobre as várias classes de notificações de saúde do serviço, consulte [as propriedades das notificações de saúde do Serviço.](service-health-notifications-properties.md)

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Veja as notificações de saúde do seu serviço no portal Azure

1. No [portal Azure,](https://portal.azure.com)selecione **Monitor**.

    ![Screenshot do menu do portal Azure, com Monitor selecionado](./media/service-notifications/home-monitor.png)

    O Azure Monitor reúne todas as suas definições de monitorização e dados numa única visão consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

1. Selecione **Alertas**.

    ![Screenshot do registo de atividade do monitor, com alertas selecionados](./media/service-notifications/service-health-summary.png)

1. Selecione **+Adicionar o alerta de registo de atividades**e crie um alerta para garantir que é notificado para futuras notificações de serviço. Para obter mais informações, consulte [Criar alertas de registo de atividades nas notificações de serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [os alertas de registo de atividades.](../azure-monitor/platform/activity-log-alerts.md)
