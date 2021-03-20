---
title: Disponibilidade e fiabilidade elevadas
description: Saiba mais sobre alta disponibilidade e fiabilidade no Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 98a6672af7e74fdd0732f3ba03264d2f674eb44f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368150"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Alta disponibilidade e fiabilidade para a Azure Scheduler

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Azure Scheduler, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que estabeleceu no Scheduler, [por favor, migra para a Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rápido possível. 
>
> O programador já não está disponível no portal Azure, mas os cmdlets [REST API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e coleções de emprego.

O Azure Scheduler proporciona [elevada disponibilidade](/azure/architecture/framework/#resiliency) e fiabilidade para os seus trabalhos. Para mais informações, consulte [SLA para Agendar](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Elevada disponibilidade

O Azure Scheduler está [altamente disponível] e utiliza a implantação de serviços geo-redundantes e a replicação de empregos geo-regionais.

### <a name="geo-redundant-service-deployment"></a>Implantação de serviços desausaciação de energia

O Azure Scheduler está disponível em quase [todas as regiões geográficas apoiadas pelo Azure hoje.](https://azure.microsoft.com/global-infrastructure/regions/#services) Assim, se um datacenter Azure numa região hospedada ficar indisponível, ainda pode utilizar o Azure Scheduler porque as capacidades de failover do serviço disponibilizam o Scheduler a partir de outro datacenter.

### <a name="geo-regional-job-replication"></a>Replicação do trabalho geo-regional

Os seus próprios empregos em Azure Scheduler são replicados em regiões de Azure. Assim, se uma região tiver uma paragem, o Azure Scheduler falha e garante que o seu trabalho é executado a partir de outro datacenter na região geográfica emparelhada.

Por exemplo, se criar um emprego no Centro Sul dos EUA, o Azure Scheduler replica automaticamente esse trabalho nos EUA. Se um falhanço acontecer no Centro Sul dos EUA, a Azure Scheduler gere o trabalho no Centro Norte dos EUA. 

![Replicação do trabalho geo-regional](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

O Azure Scheduler também garante que os seus dados permanecem dentro da mesma região geográfica, mas mais ampla, caso ocorra uma falha no Azure. Por isso, não tens de duplicar os teus empregos quando só queres muita disponibilidade. O Azure Scheduler fornece automaticamente alta disponibilidade para os seus trabalhos.

## <a name="reliability"></a>Fiabilidade

O Azure Scheduler garante a sua própria elevada disponibilidade, mas tem uma abordagem diferente dos empregos criados pelo utilizador. Por exemplo, suponha que o seu trabalho invoca um ponto final HTTP que não está disponível. A Azure Scheduler ainda tenta executar o seu trabalho com sucesso, dando-lhe formas alternativas de lidar com falhas: 

* Estabeleça políticas de repetição.
* Configurar pontos finais alternativos.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Políticas de retíria

O Azure Scheduler permite-lhe definir políticas de repetição. Se um trabalho falhar, então por defeito, o Agendador retira o trabalho mais quatro vezes a intervalos de 30 segundos. Pode tornar esta política de repetição mais agressiva, como 10 vezes em intervalos de 30 segundos, ou menos agressiva, como duas vezes em intervalos diários.

Por exemplo, suponha que crie um trabalho semanal que chame um ponto final HTTP. Se o ponto final HTTP ficar indisponível por algumas horas quando o seu trabalho funcionar, pode não querer esperar mais uma semana para que o trabalho volte a funcionar, o que acontece porque a política de retenção por defeito não funcionará neste caso. Por isso, é melhor mudar a política padrão de repetição para que as retretes aconteçam, por exemplo, a cada três horas, em vez de a cada 30 segundos. 

Para aprender a criar uma política de relíndi, consulte [a reconstapolitidade.](scheduler-concepts-terms.md#retrypolicy)

### <a name="alternate-endpoints"></a>Pontos finais alternativos

Se o seu trabalho de Azure Scheduler chamar um ponto final inacessível, mesmo depois de seguir a política de re-tentação, o Scheduler volta a ser um ponto final alternativo que pode lidar com tais erros. Então, se configurar este ponto final, o Scheduler chama esse ponto final, o que torna os seus próprios empregos altamente disponíveis quando as falhas acontecem.

Por exemplo, este diagrama mostra como o Scheduler segue a política de relemissão quando liga para um serviço web em Nova Iorque. Se as retrígios falharem, o Programador verifica se há um ponto final alternativo. Se o ponto final existir, o Scheduler começa a enviar pedidos para o ponto final alternativo. A mesma política de recandidão aplica-se tanto à ação original como à ação alternativa.

![Comportamento do programador com política de relemis e ponto final alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

O tipo de ação para a ação alternativa pode diferir da ação original. Por exemplo, embora a ação original chame um ponto final HTTP, a ação alternativa pode registar erros usando uma fila de armazenamento, fila de ônibus de serviço ou ação de tópico service bus.

Para aprender a configurar um ponto final alternativo, consulte [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
* [Limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)