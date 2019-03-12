---
title: O que é o Agendador do Azure? | Microsoft Docs
description: Saiba como criar, agendar e executar tarefas automatizadas que chamam serviços dentro ou fora do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 21204e85adf1c68264ea448360c9e1120567ef3f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764579"
---
# <a name="what-is-azure-scheduler"></a>O que é o Agendador do Azure?

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

O [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) ajuda-o a criar [tarefas](../scheduler/scheduler-concepts-terms.md) que são executadas na cloud ao descrever ações de forma declarativa. O serviço, em seguida, agenda automaticamente e executa essas ações. Por exemplo, pode chamar serviços dentro e fora do Azure, como chamar pontos finais HTTP ou HTTPS, e também publicar mensagens nas filas do Armazenamento do Microsoft Azure e nas filas ou nos tópicos do Azure Service Bus. Pode executar tarefas imediatamente ou num momento posterior. O Scheduler suporta [agendas complexas e periodicidade avançada](../scheduler/scheduler-advanced-complexity.md). O Scheduler especifica quando executar tarefas, mantém um histórico dos resultados das tarefas que pode rever e, em seguida, agenda de modo previsível e fiável as cargas de trabalho a executar.

Apesar de poder utilizar o Scheduler para criar, manter e executar cargas de trabalho agendadas, o Scheduler não aloja as cargas de trabalho nem executa código. O serviço apenas *invoca* os serviços ou o código alojados noutro local, por exemplo, no Azure, no local ou noutro fornecedor. O Scheduler pode invocar através de HTTP, HTTPS, uma fila do Armazenamento, uma fila do Service Bus ou um tópico do Service Bus. Para criar, gerir e agendar tarefas, pode utilizar o [portal do Azure](../scheduler/scheduler-get-started-portal.md), código, [API REST do Scheduler](https://docs.microsoft.com/rest/api/scheduler/) ou a [Referência de cmdlets do PowerShell do Microsoft Azure Scheduler](scheduler-powershell-reference.md). Por exemplo, pode criar, ver, atualizar, gerir ou eliminar tarefas e [coleções de tarefas](../scheduler/scheduler-concepts-terms.md) de forma programática através de scripts e no portal do Azure.

Outras funcionalidades de agendamento do Azure também utilizam o Scheduler em segundo plano, por exemplo, o [WebJobs do Azure](../app-service/webjobs-create.md), que é uma das [Aplicações Web](https://azure.microsoft.com/services/app-service/web/) do Serviço de Aplicações do Azure. Pode gerir a comunicação para estas ações com a [API REST do Scheduler](https://docs.microsoft.com/rest/api/scheduler/). Ajuda-o a gerir a comunicação destas ações.

O Scheduler poderá ser útil nos seguintes cenários:

* **Executar ações de aplicação recorrente**: Por exemplo, periodicamente recolha dados do Twitter num feed.

* **Efetuar a manutenção diária**: Por exemplo, a remoção de registos diariamente, efetuar cópias de segurança e outras tarefas de manutenção. 

  Por exemplo, como administrador, pode querer fazer cópias de segurança da base de dados à 1:00 todos os dias durante os próximos nove meses.

## <a name="next-steps"></a>Passos Seguintes

* [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)
* Saiba mais sobre [planos e faturação do Azure Scheduler](scheduler-plans-billing.md)
* Saiba [como criar agendas complexas e periodicidade avançada com o Azure Scheduler](scheduler-advanced-complexity.md)