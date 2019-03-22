---
title: APIs para a automatização de reserva do Azure | Documentos da Microsoft
description: Saiba mais sobre as APIs do Azure que pode utilizar para obter informações sobre a reserva programaticamente.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: banders
ms.openlocfilehash: 7e5697073b9406d915eda99a5e71e3123c48073a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880236"
---
# <a name="apis-for-azure-reservation-automation"></a>APIs para a automatização de reserva do Azure

Utilize as APIs do Azure para obter programaticamente informações para a sua organização sobre reservas de software ou serviço do Azure.

## <a name="find-reservation-plans-to-buy"></a>Encontre planos de reserva para comprar

Utilize a reserva recomendação API para obter recomendações no qual pretende comprar reservas com base na utilização da sua organização. Para obter mais informações, consulte [obter recomendações de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Também pode analisar o uso de recursos, utilizando os detalhes de utilização da API de consumo. Para obter mais informações, consulte [detalhes de utilização - lista para faturação período por conta de cobrança](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). Recursos do Azure que usar consistentemente geralmente são o melhor candidato para uma reserva.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Atualmente não é possível comprar uma reserva através de programação. Para comprar uma reserva, consulte os artigos seguintes:

Planos de serviço:
- [Máquina virtual](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [BD do Cosmos](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [Base de Dados SQL](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Planos de software:
- [Software SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Obter reservas

Se for um cliente do Azure com um Enterprise Agreement (cliente EA), pode obter as reservas sua organização tiver comprada através de [API os custos de transação de obter a instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Para outras subscrições, obter a lista de reservas comprou e têm permissões para ver com a API [encomenda de reserva - lista](/rest/api/reserved-vm-instances/reservationorder/list). Por predefinição, o proprietário da conta ou pessoa que comprasse a reserva tem permissões para ver a reserva.

## <a name="see-reservation-usage"></a>Ver utilização de reserva

Se for um cliente EA, pode ver através de programação como as reservas na sua organização estão sendo usadas. Para obter mais informações, consulte [utilização de obter a instância reservada para os clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Para outras subscrições, utilize a API [resumos de reservas - lista por reserva ordem e reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Se achar que as reservas da sua organização estão a ser utilizada:

- Certifique-se as máquinas virtuais que sua organização cria correspondem o tamanho da VM que está a reserva.
- Certifique-se de flexibilidade de tamanho de instância está ativada. Para obter mais informações, consulte [Gerir reservas - alteração otimizar a definição para instâncias de VM reservadas](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Altere o âmbito da reserva compartilhado, para que ele aplica-se mais amplamente. Para obter mais informações, consulte [Gerir reservas - alterar o âmbito de uma reserva](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).
- A quantidade não utilizada do Exchange. Para obter mais informações, consulte [Gerir reservas - cancelamentos e trocas](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Conceder acesso ao reservas

Obter a lista de todas as reservas que um utilizador tem acesso ao utilizar o [reserva - operação - API de lista](/rest/api/reserved-vm-instances/reservationorder/list). Para dar acesso uma reserva através de programação, consulte um dos seguintes artigos:

- [Gerir o acesso com RBAC e a API REST](../role-based-access-control/role-assignments-rest.md)
- [Gerir o acesso com RBAC e o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Gerir o acesso com RBAC e a CLI do Azure](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Reserva de dividir ou unir

Depois de comprar mais do que uma instância de recurso dentro de uma reserva, pode querer atribuir instâncias dentro desse reserva a subscrições diferentes. Pode alterar o âmbito de reserva para que seja aplicado a todas as subscrições no mesmo contexto de faturação. Mas para fins de gestão ou o orçamento de custo, talvez queira manter o âmbito como "subscrição individual" e atribuir as instâncias de reserva de uma subscrição específica. 

Para dividir uma reserva, use a API [dividir reserva -](/rest/api/reserved-vm-instances/reservation/split). Também pode dividir uma reserva com o PowerShell. Para obter mais informações, consulte [Gerir reservas - reserva dividido em duas reservas](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Para intercalar duas reservas numa reserva, use a API [reserva - Merge](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Alterar o âmbito de uma reserva

O âmbito de uma reserva pode ser subscrição única ou todas as subscrições no seu contexto de faturação. Se definir o âmbito para cada subscrição individual, a reserva é correspondida à execução de recursos na subscrição selecionada. Se definir o escopo compartilhado, o Azure corresponde a reserva de recursos que são executados em todas as subscrições dentro do contexto de faturação. O contexto de faturação é dependente da subscrição que utilizou para comprar a reserva. Para obter mais informações, consulte [Gerir reservas - alterar o âmbito](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).

Para alterar o âmbito por meio de programação, use a API [reserva - atualização](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Saiba mais

- [Quais são as reservas para o Azure](billing-save-compute-costs-reservations.md)
- [Compreender a forma como o desconto de reserva de VM é aplicado](billing-understand-vm-reservation-charges.md)
- [Compreender a forma como o desconto de plano de software SUSE Linux Enterprise é aplicado](billing-understand-suse-reservation-charges.md)
- [Compreender como é aplicado a outro desconto de reserva](billing-understand-reservation-charges.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa de fornecedor de soluções (CSP) do parceiro Center na nuvem](https://docs.microsoft.com/partner-center/azure-reservations)