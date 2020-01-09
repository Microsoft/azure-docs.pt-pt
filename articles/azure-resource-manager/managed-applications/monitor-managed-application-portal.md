---
title: Usar portal do Azure para monitorar um aplicativo gerenciado
description: Mostra como usar o portal do Azure para monitorar a disponibilidade e os alertas de um aplicativo gerenciado.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651751"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorar uma instância implantada de um aplicativo gerenciado

Depois de implantar um aplicativo gerenciado em sua assinatura do Azure, talvez você queira verificar o status do aplicativo. Este artigo mostra opções na portal do Azure para verificar o status. Você pode monitorar a disponibilidade dos recursos em seu aplicativo gerenciado. Você também pode configurar e exibir alertas.

## <a name="view-resource-health"></a>Ver o estados de funcionamento dos recursos

1. Selecione a instância do aplicativo gerenciado.

   ![Selecionar aplicativo gerenciado](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecione **Resource Health**.

   ![Selecionar Resource Health](./media/monitor-managed-application-portal/select-resource-health.png)

1. Exiba a disponibilidade dos recursos em seu aplicativo gerenciado.

   ![Ver o estados de funcionamento dos recursos](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Ver alertas

1. Selecione **alertas**.

   ![Selecionar alertas](./media/monitor-managed-application-portal/select-alerts.png)

1. Se você tiver regras de alerta configuradas, verá informações sobre alertas que foram gerados.

   ![Ver alertas](./media/monitor-managed-application-portal/view-alerts.png)

1. Para adicionar regras de alerta, selecione **+ nova regra de alerta**.

   ![Criar alerta](./media/monitor-managed-application-portal/create-new-alert.png)

Você pode criar alertas para a instância do aplicativo gerenciado ou os recursos no aplicativo gerenciado. Para obter informações sobre como criar alertas, consulte [visão geral de alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter exemplos de aplicativos gerenciados, consulte [projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para implantar um aplicativo gerenciado, consulte [implantar o aplicativo de catálogo de serviços por meio de portal do Azure](deploy-service-catalog-quickstart.md).