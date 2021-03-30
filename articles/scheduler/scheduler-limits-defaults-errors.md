---
title: Limites, quotas e limiares no Azure Scheduler
description: Saiba mais sobre limites, quotas, valores predefinidos e limiares de aceleração para Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898533"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limites, quotas e limiares de aceleração no Azure Scheduler

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Azure Scheduler, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que estabeleceu no Scheduler, [por favor, migra para a Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rápido possível. 
>
> O programador já não está disponível no portal Azure, mas os cmdlets [REST API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e coleções de emprego.

## <a name="limits-quotas-and-thresholds"></a>Limites, quotas e limiares

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>cabeçalho x-ms-request-id

Cada pedido feito contra o serviço Scheduler devolve um cabeçalho de resposta chamado **x-ms-request-id**. Este cabeçalho contém um valor opaco que identifica exclusivamente o pedido. Assim, se um pedido falhar consistentemente, e confirmar que o pedido está devidamente formatado, pode reportar o erro à Microsoft fornecendo o valor do cabeçalho de resposta **x-ms-pedido-id** e incluindo estes detalhes: 

* O valor **x-ms-request-id**
* O momento aproximado quando o pedido foi feito 
* Os identificadores para a subscrição do Azure, recolha de emprego e emprego 
* O tipo de operação que o pedido tentou

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Planos e faturação para Azure Scheduler](scheduler-plans-billing.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)