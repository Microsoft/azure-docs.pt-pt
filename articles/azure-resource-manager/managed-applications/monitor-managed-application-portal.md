---
title: Use o portal Azure para monitorizar uma aplicação gerida
description: Mostra como usar o portal Azure para monitorizar a disponibilidade e alertas para uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578267"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorize uma instância implantada de uma aplicação gerida

Depois de ter implementado uma aplicação gerida para a sua subscrição Azure, poderá querer verificar o estado da aplicação. Este artigo mostra opções no portal Azure para verificar o estado. Pode monitorizar a disponibilidade dos recursos na sua aplicação gerida. Também pode configurar e visualizar alertas.

## <a name="view-resource-health"></a>Ver o estados de funcionamento dos recursos

1. Selecione a sua instância de aplicação gerida.

   ![Selecione a aplicação gerida](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecione **Estado de funcionamento do recurso**.

   ![Selecione a saúde dos recursos](./media/monitor-managed-application-portal/select-resource-health.png)

1. Veja a disponibilidade dos recursos na sua aplicação gerida.

   ![Ver o estados de funcionamento dos recursos](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Ver alertas

1. Selecione **Alertas**.

   ![Selecione alertas](./media/monitor-managed-application-portal/select-alerts.png)

1. Se tiver regras de alerta configuradas, consulte informações sobre alertas que foram levantados.

   ![Ver alertas](./media/monitor-managed-application-portal/view-alerts.png)

1. Para adicionar regras de alerta, selecione **+ Nova regra de alerta**.

   ![Criar alerta](./media/monitor-managed-application-portal/create-new-alert.png)

Pode criar alertas para a sua aplicação gerida ou para os recursos na aplicação gerida. Para obter informações sobre a criação de alertas, consulte [a visão geral dos alertas no Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Passos seguintes

* Para exemplos de aplicações geridos, consulte [projetos de amostra para aplicações geridas pela Azure](sample-projects.md).
* Para implementar uma aplicação gerida, consulte [a aplicação do catálogo de serviços Implementar através do portal Azure.](deploy-service-catalog-quickstart.md)