---
title: Elevada disponibilidade e fiabilidade - Azure Scheduler
description: Saiba mais sobre a alta disponibilidade e confiabilidade no agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 50ab6cfefe4a7df9d671e7fd1287aa16b803f260
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702896"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Elevada disponibilidade e fiabilidade do Scheduler do Azure

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

O Azure Scheduler fornece [elevada disponibilidade](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) e fiabilidade para os trabalhos. Para obter mais informações, consulte [SLA do Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Elevada disponibilidade

O Azure Scheduler é [altamente disponível] e utiliza a implementação do serviço georredundante e replicação de tarefa, de região geo.

### <a name="geo-redundant-service-deployment"></a>Implementação de serviço com redundância geográfica

O Azure Scheduler está disponível no portal do Azure em quase [cada região geográfica atualmente suportada pelo Azure](https://azure.microsoft.com/global-infrastructure/regions/#services). Portanto, se um datacenter do Azure numa região alojada ficar indisponível, pode continuar a utilizar a Azure Scheduler porque capacidades de ativação pós-falha do serviço disponibilizar agendador de outro datacenter.

### <a name="geo-regional-job-replication"></a>Replicação de tarefa geo-regional

Suas próprias tarefas no agendador do Azure são replicadas entre regiões do Azure. Então, se uma região ficar indisponível, o agendador do Azure efetua a ativação pós-falha e certifica-se de que seu trabalho é executado a partir de outro datacenter na região geográfica associada.

Por exemplo, se criar uma tarefa no centro-Sul, o agendador do Azure replica automaticamente essa tarefa no e.u.a. Centro-Norte. Se ocorrer uma falha num centro-Sul, o Azure Scheduler é executada a tarefa no e.u.a. Centro-Norte. 

![Replicação de tarefa geo-regional](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

O Azure Scheduler também torna-se de que seus dados permanecem na região geográfica mais amplo mas mesmo, caso de ocorrer uma falha no Azure. Portanto, não tem de duplicar as tarefas quando quiser apenas de elevada disponibilidade. O Azure Scheduler automaticamente fornece elevada disponibilidade a seus trabalhos.

## <a name="reliability"></a>Fiabilidade

O Azure Scheduler garante sua própria elevada disponibilidade, mas utiliza uma abordagem diferente para trabalhos criados pelo utilizador. Por exemplo, suponha que sua tarefa invoca um ponto final HTTP que não está disponível. O Azure Scheduler ainda tenta executar o seu trabalho com êxito ao fornecer-lhe maneiras alternativas para processamento de falhas: 

* Configure as políticas de repetição.
* Configure pontos finais alternativos.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Repetir políticas

O Azure Scheduler permite-lhe configurar as políticas de repetição. Se uma tarefa falhar, em seguida, por predefinição, o Scheduler tentará novamente a tarefa de quatro vezes mais em intervalos de 30 segundos. Pode fazer esta política de repetição intervalos mais agressivos, por exemplo, 10 vezes em intervalos de 30 segundos ou menos agressivos, por exemplo, duas vezes no diário.

Por exemplo, suponha que criar uma tarefa semanal que chama um ponto final HTTP. Se o ponto de extremidade HTTP ficar indisponível por algumas horas, quando a tarefa é executada, pode não querer esperar outro semana para a tarefa executar novamente, o que acontece porque a política de repetição predefinida não funcionará neste caso. Portanto, convém alterar a política de repetição padrão para que as repetições ocorrem, por exemplo, a cada três horas, em vez a cada 30 segundos. 

Para saber como configurar uma política de repetição, veja [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Pontos finais alternativos

Se a tarefa do agendador do Azure chama um ponto final que está inacessível, mesmo depois de seguir a política de repetição, o agendador retrocede para um ponto de final alternativo que pode lidar com esses erros. Então, se configurou a este ponto final, o agendador chama esse ponto de extremidade, o que torna as suas próprias tarefas altamente disponíveis quando ocorrem falhas.

Por exemplo, este diagrama mostra como o agendador segue a política de repetição ao chamar um serviço da web em nova York. Se as repetições falharem, o agendador verifica a existência de um ponto final alternativo. Se o ponto final de existir, o Scheduler inicia a enviar pedidos para o ponto final alternativo. A mesma política de repetição aplica-se para a ação original e a ação alternativa.

![Comportamento de Scheduler com a política de repetição e o ponto final alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

O tipo de ação para a ação alternativo pode ser diferente da ação de original. Por exemplo, embora a ação original chama um ponto final HTTP, a ação alternativa poderá registar erros ao utilizar uma fila de armazenamento, a fila do Service Bus ou a ação de tópico de barramento de serviço.

Para saber como configurar um ponto final alternativo, consulte [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md)
* [Limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)
