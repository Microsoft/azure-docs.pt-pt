---
title: Planos e faturação - Azure Scheduler
description: Saiba mais sobre os planos e faturação do Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 3a8664497d3d082ec1c7f584188854991e872d50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720439"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planos e faturação do Azure Scheduler

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="job-collection-plans"></a>Planos de recolha de tarefa

No agendador do Azure, uma coleção de tarefas contém um número específico de tarefas. A coleção de tarefas é a entidade faturável e é fornecida nos planos Standard, P10 Premium e P20 Premium, que são descritos aqui: 

| Plano de recolha de tarefa | Máximo de tarefas por coleção | Periodicidade máxima | Coleções de tarefas máxima por subscrição | Limits | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 tarefas por coleção | Um minuto. Não é possível executar tarefas com mais frequência do que um minuto. | Cada subscrição do Azure pode ter até 100 coleções de tarefas padrão. | Acesso ao conjunto de todas as funcionalidades do Scheduler | 
| **P10 Premium** | 50 tarefas por coleção | Um minuto. Não é possível executar tarefas com mais frequência do que um minuto. | Cada subscrição do Azure pode ter até 10 000 coleções de tarefas P10 Premium. Para obter mais coleções, <a href="mailto:wapteams@microsoft.com">contacte-nos</a>. | Acesso ao conjunto de todas as funcionalidades do Scheduler |
| **P20 Premium** | 1000 tarefas por coleção | Um minuto. Não é possível executar tarefas com mais frequência do que um minuto. | Cada subscrição do Azure pode ter até 5 000 coleções de tarefas P20 Premium. Para obter mais coleções, <a href="mailto:wapteams@microsoft.com">contacte-nos</a>. | Acesso ao conjunto de todas as funcionalidades do Scheduler |
|||||| 

## <a name="pricing"></a>Preços

Para detalhes de preços, consulte [preços do Scheduler](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Aumentar ou diminuir planos

Em qualquer altura, pode atualizar ou mudar a versão de um plano de coleção de tarefas entre os planos Standard, P10 Premium e P20 Premium.

## <a name="active-status-and-billing"></a>Estado do Active Directory e faturação

Coleções de tarefas são sempre ativas, a menos que a sua subscrição do Azure inteira entra em estado temporário desativado devido a problemas de faturação. E embora pode desativar todas as tarefas numa coleção de tarefas por meio de uma única operação, esta ação não altera a Estado de faturação a coleção de tarefas, pelo que é a coleção de tarefas *ainda* faturado. Coleções de tarefas vazio são consideradas Active Directory e são faturadas.

Para certificar-se de que não é cobrada uma coleção de tarefas, tem de eliminar a coleção de tarefas.

## <a name="standard-billable-units"></a>Unidades faturáveis padrão

Uma unidade a cobrar standard pode ter até 10 coleções de tarefas padrão. Como uma coleção de tarefas padrão pode ter até 50 tarefas por coleção, uma unidade de faturação padrão permite que a sua subscrição do Azure ter até 500 tarefas ou até quase 22 *milhão* execuções por mês de tarefas. Esta lista explica como é cobrado com base em vários números de coleções de tarefas padrão:

* Se tiver entre 1 e coleções de tarefas padrão 10, é-lhe cobrada para uma unidade de faturação padrão. 

* Se tiver entre 11 e coleções de tarefas padrão 20, é cobrado para duas unidades de faturas padrão. 

* Se tiver entre 21 e coleções de tarefas padrão 30, a faturação para três unidades de faturas padrão e assim por diante.

## <a name="p10-premium-billable-units"></a>Unidades faturáveis do P10 premium

Uma unidade a cobrar P10 premium pode ter até 10 000 coleções de tarefas P10 Premium. Como uma coleção de tarefas P10 Premium pode ter até 50 tarefas por coleção, uma unidade faturação para o P10 premium permite que a sua subscrição do Azure ter até 500.000 tarefas ou até quase 22 *mil milhões* execuções por mês de tarefas. 

Coleções de tarefas P10 Premium fornecem as mesmas capacidades do coleções de tarefas padrão, mas oferecem uma garantia de reparação de preço para as aplicações que requerem muitas coleções de tarefas e fornecer escalabilidade mais. Esta lista explica como é cobrado com base em vários números de coleções de tarefas P10 Premium:

* Se tiver entre 1 e 10 000 coleções de tarefas P10 Premium, é-lhe cobrada para a unidade de faturação um P10 premium. 

* Se tiver entre 10,001 e 20 000 coleções de tarefas P10 Premium, a faturação para unidades de faturas 2 P10 premium e assim por diante.

## <a name="p20-premium-billable-units"></a>Unidades faturáveis do P20 premium

Uma unidade a cobrar P20 premium pode ter até 5 000 coleções de tarefas P20 Premium. Como uma coleção de tarefas P20 Premium pode ter até 1.000 tarefas por coleção de tarefas, uma unidade faturação para o P20 premium permite que a sua subscrição do Azure tem para 5,000,000 tarefas ou até quase 220 *mil milhões* execuções por mês de tarefas.

Coleções de tarefas P20 Premium fornecem as mesmas capacidades do coleções de tarefas P10 Premium, mas também para suportar um maior número de tarefas por coleção e um maior número total de tarefas geral que Premium P10, fornecendo escalabilidade mais.

## <a name="plan-comparison"></a>Comparação de planos

* Se tiver mais de 100 coleções de tarefas padrão (10 unidades de faturas padrão), em seguida, pode obter um resultado melhor fazendo com que todas as coleções de tarefas num plano Premium.

* Se tiver uma coleção de tarefas padrão e uma coleção de tarefas Premium, em seguida, é-lhe cobrada para uma unidade de faturação standard *e* uma unidade de faturação premium.

  As contas de serviço de Scheduler com base no número de coleções de tarefas ativas que estão standard ou premium.

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)