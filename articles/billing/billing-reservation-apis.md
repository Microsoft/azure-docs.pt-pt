---
title: APIs para automação de reserva do Azure | Microsoft Docs
description: Saiba mais sobre as APIs do Azure que você pode usar para obter informações de reserva programaticamente.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: 36bc403c4000e58541f22c2cb44f77a28e81cb72
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779892"
---
# <a name="apis-for-azure-reservation-automation"></a>APIs para automação de reserva do Azure

Use as APIs do Azure para obter informações de sua organização de forma programática sobre as reservas de software ou serviço do Azure.

## <a name="find-reservation-plans-to-buy"></a>Localizar planos de reserva para comprar

Use a API de recomendação de reserva para obter recomendações sobre quais planos de reservas comprar com base no uso da sua organização. Para obter mais informações, consulte [obter recomendações de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Você também pode analisar o uso de recursos usando os detalhes de uso da API de consumo. Para obter mais informações, consulte [detalhes de uso-lista de período de cobrança por conta de cobrança](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). Os recursos do Azure que você usa consistentemente geralmente são o melhor candidato para uma reserva.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar reservas do Azure e planos de software programaticamente usando APIs REST. Para saber mais, consulte [ordem de reserva-API de compra](/rest/api/reserved-vm-instances/reservationorder/purchase).

Aqui está uma solicitação de exemplo para comprar usando a API REST:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Corpo do Pedido:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Você também pode comprar uma reserva no portal do Azure. Para obter mais informações, veja os artigos seguintes:

Planos de serviço:
- [Máquina virtual](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [BD do Cosmos](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [Base de Dados SQL](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Planos de software:
- [Software SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Obter reservas

Se você for um cliente do Azure com um Enterprise Agreement (cliente EA), poderá obter as reservas que sua organização comprou usando a [API obter](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)encargos de transação de instância reservada. Para outras assinaturas, obtenha a lista de reservas compradas e tenha permissões para exibir usando a [lista ordem de reserva](/rest/api/reserved-vm-instances/reservationorder/list)da API. Por padrão, o proprietário da conta ou a pessoa que comprou a reserva tem permissões para exibir a reserva.

## <a name="see-reservation-usage"></a>Consulte uso de reserva

Se você for um cliente do EA, poderá exibir programaticamente como as reservas em sua organização estão sendo usadas. Para obter mais informações, consulte [obter o uso da instância reservada para clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Para outras assinaturas, use os resumos de [reservas de API-List por ordem de reserva e reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Se você descobrir que as reservas da sua organização estão sendo usadas:

- Verifique se as máquinas virtuais que sua organização cria correspondem ao tamanho da VM que está na reserva.
- Verifique se a flexibilidade do tamanho da instância está ativada. Para obter mais informações, consulte [gerenciar reservas – alterar a configuração otimizar para instâncias de VM reservadas](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Altere o escopo de reserva para compartilhado para que ele se aplique mais amplamente. Para obter mais informações, consulte [gerenciar reservas – alterar o escopo de uma reserva](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Troque a quantidade não utilizada. Para obter mais informações, consulte [gerenciar reservas](billing-manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Conceder acesso a reservas

Obtenha a lista de todas as reservas às quais um usuário tem acesso usando a [API reserva-Operation-List](/rest/api/reserved-vm-instances/reservationorder/list). Para conceder acesso a uma reserva programaticamente, consulte um dos seguintes artigos:

- [Gerenciar o acesso usando o RBAC e a API REST](../role-based-access-control/role-assignments-rest.md)
- [Gerenciar o acesso usando RBAC e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Gerenciar o acesso usando RBAC e CLI do Azure](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Reserva de divisão ou mesclagem

Depois de comprar mais de uma instância de recurso em uma reserva, talvez você queira atribuir instâncias dentro dessa reserva a assinaturas diferentes. Você pode alterar o escopo de reserva para que ele se aplique a todas as assinaturas no mesmo contexto de cobrança. Mas para fins de gerenciamento de custos ou orçamento, convém manter o escopo como "assinatura única" e atribuir instâncias de reserva a uma assinatura específica.

Para dividir uma reserva, use a API [Reservation-Split](/rest/api/reserved-vm-instances/reservation/split). Você também pode dividir uma reserva usando o PowerShell. Para obter mais informações, consulte [gerenciar reservas – dividir a reserva em duas reservas](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Para mesclar duas reservas em uma reserva, use a [mesclagem de reserva](/rest/api/reserved-vm-instances/reservation/merge)de API.

## <a name="change-scope-for-a-reservation"></a>Alterar o escopo de uma reserva

O escopo de uma reserva pode ser assinatura única, grupo de recursos único ou todas as assinaturas em seu contexto de cobrança. Se você definir o escopo como assinatura única ou grupo de recursos único, a reserva será correspondida à execução de recursos na assinatura selecionada. Se você excluir ou mover a assinatura ou o grupo de recursos, a reserva não será utilizada.  Se você definir o escopo como compartilhado, o Azure corresponderá à reserva para recursos que são executados em todas as assinaturas no contexto de cobrança. O contexto de cobrança depende da assinatura que você usou para comprar a reserva. Você pode selecionar o escopo na compra ou alterá-lo a qualquer momento após a compra. Para obter mais informações, consulte [gerenciar reservas-alterar o escopo](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Para alterar o escopo programaticamente, use a API [Reservation-Update](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Saber mais

- [O que são reservas para o Azure](billing-save-compute-costs-reservations.md)
- [Entenda como o desconto de reserva da VM é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda como o desconto do plano de software SUSE Linux Enterprise é aplicado](billing-understand-suse-reservation-charges.md)
- [Entender como outros descontos de reserva são aplicados](billing-understand-reservation-charges.md)
- [Entenda o uso de reserva para sua assinatura pré-paga](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa CSP (provedor de soluções na nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
