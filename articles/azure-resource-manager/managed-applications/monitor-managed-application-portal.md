---
title: Utilize o portal Azure para monitorizar uma aplicação gerida
description: Mostra como usar o portal Azure para monitorizar a disponibilidade e alertas para uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651751"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorizar uma instância implementada de uma aplicação gerida

Depois de ter implementado uma aplicação gerida para a sua subscrição Azure, poderá querer verificar o estado da aplicação. Este artigo mostra opções no portal Azure para verificar o estado. Pode monitorizar a disponibilidade dos recursos na sua aplicação gerida. Também pode configurar e ver alertas.

## <a name="view-resource-health"></a>Ver o estados de funcionamento dos recursos

1. Selecione a sua instância de candidatura gerida.

   ![Selecione aplicação gerida](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecione **Saúde de Recursos**.

   ![Selecione a saúde dos recursos](./media/monitor-managed-application-portal/select-resource-health.png)

1. Veja a disponibilidade dos recursos na sua aplicação gerida.

   ![Ver o estados de funcionamento dos recursos](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Ver alertas

1. Selecione **Alertas**.

   ![Selecione alertas](./media/monitor-managed-application-portal/select-alerts.png)

1. Se tiver regras de alerta configuradas, consulte informações sobre alertas que foram levantados.

   ![Ver alertas](./media/monitor-managed-application-portal/view-alerts.png)

1. Para adicionar regras de alerta, **selecione + Nova regra de alerta**.

   ![Criar alerta](./media/monitor-managed-application-portal/create-new-alert.png)

Pode criar alertas para a sua instância de candidatura gerida ou para os recursos na aplicação gerida. Para obter informações sobre a criação de alertas, consulte a [visão geral dos alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Passos seguintes

* Para exemplos de aplicações geridas, consulte [projetos de amostra para aplicações geridas pelo Azure.](sample-projects.md)
* Para implementar uma aplicação gerida, consulte a aplicação de catálogo de [serviços Deploy através do portal Azure](deploy-service-catalog-quickstart.md).