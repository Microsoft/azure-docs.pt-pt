---
title: O que é o Agendador do Azure?
description: Crie horários e execute trabalhos automatizados que liguem para serviços dentro ou fora do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898543"
---
# <a name="what-is-azure-scheduler"></a>O que é o Agendador do Azure?

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

O [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) ajuda-o a criar [tarefas](../scheduler/scheduler-concepts-terms.md) que são executadas na cloud ao descrever ações de forma declarativa. O serviço, em seguida, agenda automaticamente e executa essas ações. Por exemplo, pode chamar serviços dentro e fora do Azure, como chamar pontos finais HTTP ou HTTPS, e também publicar mensagens nas filas do Armazenamento do Microsoft Azure e nas filas ou nos tópicos do Azure Service Bus. Pode executar tarefas imediatamente ou num momento posterior. O Scheduler suporta [agendas complexas e periodicidade avançada](../scheduler/scheduler-advanced-complexity.md). O Scheduler especifica quando executar tarefas, mantém um histórico dos resultados das tarefas que pode rever e, em seguida, agenda de modo previsível e fiável as cargas de trabalho a executar.

Outras funcionalidades de agendamento do Azure também utilizam o Scheduler em segundo plano, por exemplo, o [WebJobs do Azure](../app-service/webjobs-create.md), que é uma das [Aplicações Web](https://azure.microsoft.com/services/app-service/web/) do Serviço de Aplicações do Azure. Pode gerir a comunicação para estas ações utilizando a API do [Scheduler REST,](https://docs.microsoft.com/rest/api/scheduler/)que o ajuda a gerir a comunicação para estas ações.

O Scheduler poderá ser útil nos seguintes cenários:

* Executar ações de aplicação recorrentes: por exemplo, recolher periodicamente dados do Twitter num feed.

* Efetuar a manutenção diária: tais como, eliminação diária de registos, realizar cópias de segurança e outras tarefas de manutenção.

  Por exemplo, como administrador, pode querer fazer cópias de segurança da base de dados à 1:00 todos os dias durante os próximos nove meses.

Apesar de poder utilizar o Scheduler para criar, manter e executar cargas de trabalho agendadas, o Scheduler não aloja as cargas de trabalho nem executa código. O serviço apenas *invoca* os serviços ou o código alojados noutro local, por exemplo, no Azure, no local ou noutro fornecedor. O Scheduler pode invocar através de HTTP, HTTPS, uma fila do Armazenamento, uma fila do Service Bus ou um tópico do Service Bus.

Para criar, agendar, gerir, atualizar ou eliminar empregos e recolhas de [emprego,](../scheduler/scheduler-concepts-terms.md)pode utilizar código, a API DO API do [Scheduler REST,](https://docs.microsoft.com/rest/api/scheduler/)ou os [cmdlets Do Calendário Azure PowerShell](scheduler-powershell-reference.md).

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Planos e faturação para O Scheduler Azure](scheduler-plans-billing.md)
* [Construir horários complexos e recorrência avançada com o Programador Azure](scheduler-advanced-complexity.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
