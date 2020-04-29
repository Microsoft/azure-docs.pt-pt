---
title: Disponibilidade e fiabilidade elevadas
description: Saiba mais sobre alta disponibilidade e fiabilidade no Programador Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 20c2054e168a9b17d9b4ab159cfefbf607ab6d11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898551"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Elevada disponibilidade e fiabilidade para o Programador Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

O Azure Scheduler proporciona [uma elevada disponibilidade](https://docs.microsoft.com/azure/architecture/framework/#resiliency) e fiabilidade para os seus trabalhos. Para mais informações, consulte [SLA para Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Elevada disponibilidade

O Azure Scheduler está [altamente disponível] e utiliza a implantação de serviços geo-redundantes e a replicação de emprego geo-regional.

### <a name="geo-redundant-service-deployment"></a>Implantação de serviços geo-redundantes

O Azure Scheduler está disponível em quase [todas as regiões geográficas apoiadas hoje pelo Azure.](https://azure.microsoft.com/global-infrastructure/regions/#services) Assim, se um datacenter Azure numa região hospedada ficar indisponível, ainda pode utilizar o Programador Azure porque as capacidades de failover do serviço disponibilizam o Scheduler a partir de outro datacenter.

### <a name="geo-regional-job-replication"></a>Replicação geo-regional de emprego

Os seus próprios empregos no Azure Scheduler são replicados em regiões de Azure. Assim, se uma região tiver uma paragem, o Azure Scheduler falha e garante que o seu trabalho é executado a partir de outro centro de dados na região geográfica emparelhada.

Por exemplo, se criar um emprego no Centro Sul dos EUA, o Azure Scheduler replica automaticamente esse trabalho no Centro-Norte dos EUA. Se um fracasso acontecer no Centro Sul dos EUA, o Azure Scheduler gere o trabalho no Centro-Norte dos EUA. 

![Replicação geo-regional de emprego](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

O Azure Scheduler também garante que os seus dados se mantenham na mesma região geográfica, mas mais ampla, caso aconteça uma falha em Azure. Então, não tens de duplicar os teus empregos quando só queres uma grande disponibilidade. O Azure Scheduler fornece automaticamente alta disponibilidade para os seus trabalhos.

## <a name="reliability"></a>Fiabilidade

O Azure Scheduler garante a sua própria elevada disponibilidade, mas tem uma abordagem diferente dos empregos criados pelo utilizador. Por exemplo, suponha que o seu trabalho invoque um ponto final http que não esteja disponível. O Azure Scheduler ainda tenta executar o seu trabalho com sucesso, dando-lhe formas alternativas de lidar com falhas: 

* Estabeleça políticas de retry.
* Configurar pontos finais alternativos.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Políticas de retry

O Azure Scheduler permite-lhe criar políticas de retry. Se um trabalho falhar, então por defeito, o Scheduler volta a tentar o trabalho mais quatro vezes em intervalos de 30 segundos. Pode tornar esta política de repetição mais agressiva, como 10 vezes em intervalos de 30 segundos, ou menos agressiva, como duas vezes em intervalos diários.

Por exemplo, suponha que crie um trabalho semanal que chame um ponto final http. Se o ponto final do HTTP ficar indisponível durante algumas horas quando o seu trabalho funciona, pode não querer esperar mais uma semana para que o trabalho volte a funcionar, o que acontece porque a política de retry padrão não funcionará neste caso. Por isso, é melhor mudar a política padrão de retry para que as tentativas aconteçam, por exemplo, a cada três horas, em vez de a cada 30 segundos. 

Para aprender a criar uma política de retry, consulte [a retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Pontos finais alternativos

Se o seu trabalho no Azure Scheduler chamar um ponto final inacessível, mesmo depois de seguir a política de retry, o Scheduler recua para um ponto final alternativo que pode lidar com tais erros. Por isso, se configurar este ponto final, o Scheduler chama esse ponto final, o que torna os seus próprios empregos altamente disponíveis quando acontecem falhas.

Por exemplo, este diagrama mostra como o Scheduler segue a política de retry ao chamar um serviço web em Nova Iorque. Se as tentativas falharem, o Scheduler verifica um ponto final alternativo. Se o ponto final existir, o Scheduler começa a enviar pedidos para o ponto final alternativo. A mesma política de repescasão aplica-se tanto à ação original como à ação alternativa.

![Comportamento do programador com política de retry e ponto final alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

O tipo de ação para a ação alternativa pode diferir da ação original. Por exemplo, embora a ação original chame um ponto final http, a ação alternativa pode registar erros utilizando uma fila de armazenamento, fila de ônibus de serviço ou ação de tópico de ônibus de serviço.

Para aprender a configurar um ponto final alternativo, consulte [erroAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
* [Limites, quotas, valores predefinidos e códigos de erro](scheduler-limits-defaults-errors.md)
