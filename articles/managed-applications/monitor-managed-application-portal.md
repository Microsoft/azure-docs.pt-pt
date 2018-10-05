---
title: Utilizar o portal do Azure para monitorizar uma aplicação gerida | Documentos da Microsoft
description: Mostra como utilizar o portal do Azure para monitorizar a disponibilidade e alertas de um aplicativo gerenciado.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ea9f55d7c6002aaba2fd4fdc2a76b93f98e1d6b0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810504"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorizar uma instância implantada de um aplicativo gerenciado

Depois de implementar uma aplicação gerida à sua subscrição do Azure, poderá ser útil verificar o estado do aplicativo. Este artigo mostra as opções no portal do Azure para verificar o estado. Pode monitorizar a disponibilidade dos recursos em seu aplicativo gerenciado. Também pode configurar e ver os alertas.

## <a name="view-resource-health"></a>Ver o estados de funcionamento dos recursos

1. Selecione a sua instância de aplicação gerida.

   ![Selecione a aplicação gerida](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecione **estado de funcionamento do recurso**.

   ![Selecione o estado de funcionamento do recurso](./media/monitor-managed-application-portal/select-resource-health.png)

1. Ver a disponibilidade dos recursos no seu aplicativo gerenciado.

   ![Ver o estados de funcionamento dos recursos](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Ver alertas

1. Selecione **alertas**.

   ![Selecione alertas](./media/monitor-managed-application-portal/select-alerts.png)

1. Se tiver regras de alerta configuradas, pode ver informações sobre alertas que tenham sido gerada.

   ![Ver alertas](./media/monitor-managed-application-portal/view-alerts.png)

1. Para adicionar regras de alerta, selecione **+ nova regra de alerta**.

   ![Criar alerta](./media/monitor-managed-application-portal/create-new-alert.png)

Pode criar alertas para a instância da aplicação gerida ou os recursos na aplicação gerida. Para obter informações sobre a criação de alertas, consulte [descrição geral dos alertas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="next-steps"></a>Passos Seguintes

* Para obter exemplos de aplicação gerida, veja [aplicativos gerenciados de projetos de exemplo para o Azure](sample-projects.md).
* Para implementar uma aplicação gerida, veja [implementar serviço catálogo de aplicações através do portal do Azure](deploy-service-catalog-quickstart.md).