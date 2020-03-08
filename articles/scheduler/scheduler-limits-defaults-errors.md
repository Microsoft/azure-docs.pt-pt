---
title: Limites, quotas e limiares no Programador Azure
description: Conheça limites, quotas, valores predefinidos e limites de aceleração para o Programador Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898533"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limites, quotas e limites de aceleração no Programador Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

## <a name="limits-quotas-and-thresholds"></a>Limites, quotas e limiares

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>cabeçalho x-ms-request-id

Todos os pedidos feitos contra o serviço Scheduler devolvem um cabeçalho de resposta chamado **x-ms-request-id**. Este cabeçalho contém um valor opaco que identifica exclusivamente o pedido. Assim, se um pedido falhar de forma consistente, e confirmar que o pedido está devidamente formatado, pode reportar o erro à Microsoft fornecendo o valor do cabeçalho de resposta **x-ms-request-id** e incluindo estes detalhes: 

* O valor **x-ms-request-id**
* O tempo aproximado quando o pedido foi feito 
* Os identificadores para a subscrição, recolha de emprego e emprego do Azure 
* O tipo de operação que o pedido tentou

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
* [Planos e faturação para O Scheduler Azure](scheduler-plans-billing.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)