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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898477"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planos e faturação para O Scheduler Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

## <a name="job-collection-plans"></a>Planos de recolha de emprego

No Azure Scheduler, uma coleção de empregocontém um número específico de empregos. A coleção de emprego é a entidade faturada e vem nos planos Standard, P10 Premium e P20 Premium, que são descritos aqui: 

| Plano de recolha de emprego | Trabalhos max por coleção | Recorrência máxima | Cobranças de emprego max por subscrição | Limites | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 postos de trabalho por coleção | Um por minuto. Não se pode gerir empregos mais frequentemente do que um por minuto. | Cada subscrição azure pode ter até 100 coleções de emprego Standard. | Acesso ao conjunto completo de funcionalidades do Scheduler | 
| **Prémio P10** | 50 postos de trabalho por coleção | Um por minuto. Não se pode gerir empregos mais frequentemente do que um por minuto. | Cada subscrição azure pode ter até 10.000 coleções de emprego P10 Premium. Para mais coleções, <a href="mailto:wapteams@microsoft.com">contacte-nos.</a> | Acesso ao conjunto completo de funcionalidades do Scheduler |
| **Prémio P20** | 1000 postos de trabalho por coleção | Um por minuto. Não se pode gerir empregos mais frequentemente do que um por minuto. | Cada subscrição azure pode ter até 5.000 coleções de emprego P20 Premium. Para mais coleções, <a href="mailto:wapteams@microsoft.com">contacte-nos.</a> | Acesso ao conjunto completo de funcionalidades do Scheduler |
|||||| 

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, consulte [o Preço do Programador](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Planos de upgrade ou de saque

A qualquer momento, pode atualizar ou desvalorizar um plano de recolha de emprego sem planos Standard, P10 Premium e P20 Premium.

## <a name="active-status-and-billing"></a>Estado ativo e faturação

As coleções de emprego estão sempre ativas, a menos que toda a subscrição do Azure vá para um estado com deficiência temporária devido a problemas de faturação. E embora possa desativar todos os empregos numa coleção de empregos através de uma única operação, esta ação não altera o estatuto de cobrança de emprego, por isso a cobrança de emprego *ainda* é cobrada. As coleções de emprego vazias são consideradas ativas e são cobradas.

Para garantir que uma coleção de emprego não é cobrada, deve apagar a coleção de emprego.

## <a name="standard-billable-units"></a>Unidades de faturação padrão

Uma unidade normal de faturação pode ter até 10 coleções de emprego standard. Uma vez que uma coleção de emprego Standard pode ter até 50 postos de trabalho por coleção, uma unidade de faturação padrão permite que a sua subscrição Azure tenha até 500 postos de trabalho, ou até quase 22 *milhões de* execuções de emprego por mês. Esta lista explica como você é cobrado com base em vários números de coleções de emprego Standard:

* Se tiver entre 1 e 10 coleções de emprego padrão, é cobrado por uma unidade de faturação padrão. 

* Se tiver entre 11 e 20 coleções de emprego padrão, é cobrado por duas unidades de faturação padrão. 

* Se tiver entre 21 e 30 coleções de emprego padrão, é cobrado por três unidades de faturação padrão, e assim por diante.

## <a name="p10-premium-billable-units"></a>Unidades de faturação premium P10

Uma unidade de faturação premium P10 pode ter até 10.000 coleções de emprego P10 Premium. Uma vez que uma coleção de empregoS P10 Premium pode ter até 50 postos de trabalho por coleção, uma unidade de faturação premium P10 permite que a sua subscrição Azure tenha até 500.000 postos de trabalho, ou seja, quase 22 *mil milhões* de execuções de emprego por mês. 

As coleções de emprego P10 Premium fornecem as mesmas capacidades que as coleções de emprego Standard, mas oferecem uma quebra de preço para apps que requerem muitas coleções de emprego e proporcionam mais escalabilidade. Esta lista explica como é cobrado com base em vários números de coleções de emprego P10 Premium:

* Se tiver entre 1 e 10.000 coleções de emprego P10 Premium, é cobrado por uma unidade de faturação premium P10. 

* Se tiver entre 10.001 e 20.000 coleções de emprego P10 Premium, é cobrado por 2 unidades de faturação premium P10, e assim por diante.

## <a name="p20-premium-billable-units"></a>Unidades de faturação premium P20

Uma unidade de faturação premium P20 pode ter até 5.000 coleções de emprego P20 Premium. Uma vez que uma coleção de empregos P20 Premium pode ter até 1.000 postos de trabalho por coleção de emprego, uma unidade de faturação premium P20 permite que a sua subscrição Azure tenha até 5.000.000 postos de trabalho, ou seja, quase 220 *mil milhões* de execuções de emprego por mês.

As coleções de emprego Premium P20 fornecem as mesmas capacidades que as coleções de emprego P10 Premium, mas também apoiam um maior número de postos de trabalho por coleção e um maior número total de postos de trabalho em geral do que o P10 Premium, proporcionando mais escalabilidade.

## <a name="plan-comparison"></a>Comparação de planos

* Se tiver mais de 100 coleções de emprego Standard (10 unidades de faturação padrão), então pode obter um melhor negócio tendo todas as coleções de emprego num plano Premium.

* Se você tem uma coleção de emprego Standard e uma coleção de emprego Premium, então você é cobrado para uma unidade de faturação padrão *e* uma unidade de faturação premium.

  As contas de serviço do Scheduler com base no número de coleções de emprego ativas que são standard ou premium.

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)