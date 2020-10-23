---
title: APIs para automatização de reservas do Azure | Microsoft Docs
description: Saiba mais sobre as APIs do Azure que pode utilizar para obter informações de reserva de forma programática.
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f12c1f20e01cb67adff2c83c7262f92daf8da969
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151490"
---
# <a name="apis-for-azure-reservation-automation"></a>APIs para automatização de reservas do Azure

Utilize as APIs do Azure para obter informações para a sua organização sobre as reservas de software ou serviço do Azure de forma programática.

## <a name="find-reservation-plans-to-buy"></a>Localizar planos de reservas para comprar

Utilize a API de Recomendação de reservas para obter recomendações sobre quais os planos de reservas a comprar com base na utilização da sua organização. Para obter mais informações, veja [Get reservation recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) (Obter recomendações de reservas).

Também pode analisar a utilização de recursos com os Detalhes de Utilização da API de Consumo. Para obter mais informações, veja [Usage Details - List For Billing Period By Billing Account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy) (Detalhes de Utilização – Lista do Período de Faturação por Conta de Faturação). Os recursos do Azure que utiliza consistentemente são geralmente o melhor candidato para uma reserva.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Pode utilizar APIs REST para comprar reservas do Azure e planos de software de forma programática. Para saber mais, veja [Reservation Order - Purchase API](/rest/api/reserved-vm-instances/reservationorder/purchase) (Encomenda de Reservas – API de Compras).

Segue-se um exemplo de pedido para comprar com a API REST:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Corpo do pedido:

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

Também pode comprar uma reserva no portal do Azure. Para obter mais informações, veja os artigos seguintes:

Planos do serviço:
- [Máquina virtual](../../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [BD do Cosmos](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [Base de Dados SQL](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

Planos de software:
- [Software SUSE Linux](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Obter reservas

Se for um cliente do Azure com um Contrato Enterprise (cliente EA), poderá obter as reservas que a sua organização comprou com a [API Obter os custos de transação das Instâncias Reservadas](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Para outras subscrições, obtenha a lista de reservas que comprou e para as quais tem permissões de visualização com a API [Encomenda de Reserva – Lista](/rest/api/reserved-vm-instances/reservationorder/list). Por predefinição, o proprietário da conta ou a pessoa que comprou a reserva tem permissões para visualizar a reserva.

## <a name="see-reservation-usage"></a>Ver utilização da reserva

Se for um cliente EA, poderá visualizar de forma programática a forma como as reservas na sua organização estão a ser utilizadas. Para obter mais informações, veja [Get Reserved Instance usage for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) (Obter utilização das Instâncias Reservadas dos clientes empresariais). Para outras subscrições, utilize a API [Resumos de Reservas – Lista por Encomenda de Reserva e Reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Se observar que as reservas da sua organização estão a ser pouco utilizadas:

- Confirme que as máquinas virtuais que a sua organização cria correspondem ao tamanho da VM que está na reserva.
- Confirme que a flexibilidade de tamanho da instância está ativada. Para obter mais informações, veja [Gerir reservas – Alterar a definição de otimização para Instâncias de VMs Reservadas](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Altere o âmbito de reserva para partilhada para que seja aplicado mais amplamente. Para obter mais informações, veja [Gerir reservas – Alterar o âmbito de uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).
- Troque a quantidade não utilizada. Para obter mais informações, veja [Gerir Reservas](manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Conceder acesso às reservas

Obtenha a lista de todas as reservas às quais um utilizador tem acesso com a [API Reserva – Operação – Lista](/rest/api/reserved-vm-instances/reservationorder/list). Para conceder acesso a uma reserva de forma programática, veja um dos seguintes artigos:

- [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-rest.md)
- [Utilizar o Azure PowerShell para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-powershell.md)
- [Utilizar a CLI do Azure para adicionar ou remover atribuições de funções do Azure](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dividir ou unir a reserva

Depois de comprar mais do que uma instância de recursos numa reserva, talvez queira atribuir instâncias nessa reserva a subscrições diferentes. Pode alterar o âmbito da reserva para que se aplique a todas as subscrições no mesmo contexto de faturação. Contudo, para fins de gestão de custo ou orçamento, talvez queira manter o âmbito como “subscrição individual” e atribuir instâncias de reserva a uma subscrição específica.

Para dividir uma reserva, utilize a API [Reserva – Dividir](/rest/api/reserved-vm-instances/reservation/split). Também pode dividir uma reserva com o PowerShell. Para obter mais informações, veja [Gerir reservas – Dividir a reserva em duas reservas](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Para unir duas reservas numa reserva, utilize a API [Reserva – Unir](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Alterar o âmbito de uma reserva

O âmbito de uma reserva pode ser uma subscrição individual, um grupo de recursos individual ou todas as subscrições no seu contexto de faturação. Se definir o âmbito como uma subscrição individual ou grupo de recursos individual, será feita a correspondência da reserva para executar recursos na subscrição selecionada. Se eliminar ou mover a subscrição ou o grupo de recursos, a reserva não vai ser utilizada.  Se definir o âmbito como partilhado, o Azure fará a correspondência da reserva com os recursos que são executados em todas as subscrições no contexto de faturação. O contexto de faturação depende da subscrição que utilizou para comprar a reserva. Pode selecionar o âmbito na compra ou alterá-lo em qualquer momento após a compra. Para obter mais informações, veja [Gerir reservas – Alterar o âmbito](manage-reserved-vm-instance.md#change-the-reservation-scope).

Para alterar o âmbito de forma programática, utilize a API [Reserva – Atualização](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Saiba mais

- [O que são as reservas do Azure](save-compute-costs-reservations.md)
- [Compreender como o desconto das reservas das VMs é aplicado](../manage/understand-vm-reservation-charges.md)
- [Compreender como o desconto do plano de software SUSE Linux Enterprise é aplicado](understand-suse-reservation-charges.md)
- [Compreender como outros descontos das reservas são aplicados](understand-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Custos de software Windows não incluídos nas reservas](reserved-instance-windows-software-costs.md)
- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)