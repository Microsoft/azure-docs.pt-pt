---
title: Planos e faturação
description: Saiba mais sobre planos e faturação do Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78898477"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planos e faturação para Azure Scheduler

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Azure Scheduler, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que estabeleceu no Scheduler, [por favor, migra para a Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rápido possível. 
>
> O programador já não está disponível no portal Azure, mas os cmdlets [REST API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e coleções de emprego.

## <a name="job-collection-plans"></a>Planos de recolha de emprego

Em Azure Scheduler, uma coleção de emprego contém um número específico de empregos. A recolha de emprego é a entidade faturada e vem nos planos Standard, P10 Premium e P20 Premium, que são descritos aqui: 

| Plano de recolha de emprego | Empregos máximos por coleção | Recorrência máxima | Cobranças de emprego max por subscrição | Limites | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 postos de trabalho por coleção | Um por minuto. Não se pode gerir trabalhos mais do que um por minuto. | Cada subscrição da Azure pode ter até 100 coleções de emprego standard. | Acesso ao conjunto de funcionalidades completas do Scheduler | 
| **Prémio P10** | 50 postos de trabalho por coleção | Um por minuto. Não se pode gerir trabalhos mais do que um por minuto. | Cada subscrição da Azure pode ter até 10.000 coleções de emprego P10 Premium. Para mais coleções, <a href="mailto:wapteams@microsoft.com">contacte-nos.</a> | Acesso ao conjunto de funcionalidades completas do Scheduler |
| **Prémio P20** | 1000 postos de trabalho por coleção | Um por minuto. Não se pode gerir trabalhos mais do que um por minuto. | Cada subscrição da Azure pode ter até 5.000 coleções de emprego P20 Premium. Para mais coleções, <a href="mailto:wapteams@microsoft.com">contacte-nos.</a> | Acesso ao conjunto de funcionalidades completas do Scheduler |
|||||| 

## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [o Preço do Programador](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Planos de atualização ou de downgrade

A qualquer momento, você pode atualizar ou desvalorizar um plano de recolha de emprego em todos os planos Standard, P10 Premium e P20 Premium.

## <a name="active-status-and-billing"></a>Estado ativo e faturação

As cobranças de emprego estão sempre ativas, a menos que toda a sua subscrição do Azure entre num estado temporário de incapacidade devido a problemas de faturação. E embora possa desativar todos os empregos numa coleção de empregos através de uma única operação, esta ação não altera o estatuto de faturação da coleção de empregos, por isso a cobrança de *empregos ainda* está cobrada. As coleções de empregos vazias são consideradas ativas e cobradas.

Para garantir que uma coleção de emprego não seja cobrada, tem de apagar a coleção de empregos.

## <a name="standard-billable-units"></a>Unidades faturadas padrão

Uma unidade faturada padrão pode ter até 10 coleções de emprego standard. Uma vez que uma coleção de empregos Standard pode ter até 50 postos de trabalho por cobrança, uma unidade de faturação padrão permite que a sua subscrição Azure tenha até 500 postos de trabalho, ou até quase 22 *milhões de* execuções de emprego por mês. Esta lista explica como é faturado com base em vários números de coleções de emprego standard:

* Se tiver entre 1 e 10 coleções de emprego standard, é cobrado por uma unidade de faturação padrão. 

* Se tiver entre 11 e 20 coleções de emprego standard, está cobrado por duas unidades de faturação padrão. 

* Se tens entre 21 e 30 coleções de empregos standard, estás cobrado por três unidades de faturação padrão, e assim por diante.

## <a name="p10-premium-billable-units"></a>Unidades faturadas premium P10

Uma unidade de faturação premium P10 pode ter até 10.000 coleções de emprego P10 Premium. Uma vez que uma coleção de empregos P10 Premium pode ter até 50 postos de trabalho por coleção, uma unidade de faturação premium P10 permite que a sua assinatura Azure tenha até 500.000 empregos, ou até quase 22 *mil milhões* de execuções de emprego por mês. 

As coleções de emprego P10 Premium fornecem as mesmas capacidades que as coleções de emprego Standard, mas oferecem uma pausa de preço para apps que requerem muitas coleções de emprego e proporcionam mais escalabilidade. Esta lista explica como é faturado com base em vários números de coleções de emprego P10 Premium:

* Se tiver entre 1 e 10.000 coleções de empregoS10 Premium, está faturado por uma unidade de faturação premium P10. 

* Se tiver entre 10.001 e 20.000 coleções de empregos P10 Premium, está cobrado por 2 unidades de faturação premium P10, e assim por diante.

## <a name="p20-premium-billable-units"></a>Unidades faturadas premium P20

Uma unidade de faturação premium P20 pode ter até 5.000 coleções de emprego P20 Premium. Uma vez que uma coleção de empregos P20 Premium pode ter até 1.000 empregos por recolha de emprego, uma unidade de faturação premium P20 permite que a sua assinatura Azure tenha até 5.000.000 empregos, ou até quase 220 *mil milhões* de execuções de emprego por mês.

As coleções de empregos P20 Premium oferecem as mesmas capacidades que as coleções de empregoS10 Premium, mas também apoiam um maior número de postos de trabalho por coleção e um maior número total de postos de trabalho em geral do que o P10 Premium, proporcionando uma maior escalabilidade.

## <a name="plan-comparison"></a>Comparação do plano

* Se tiver mais de 100 coleções de emprego standard (10 unidades de faturação padrão), então pode obter um melhor negócio tendo todas as coleções de emprego num plano Premium.

* Se você tem uma coleção de emprego Standard e uma coleção de emprego Premium, então você está cobrado para uma unidade de faturação padrão *e* uma unidade de faturação premium.

  As contas de serviço do Scheduler com base no número de coleções de emprego ativas que são standard ou premium.

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)