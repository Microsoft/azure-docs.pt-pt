---
title: Custos do software de reservas do Azure
description: Saiba quais medidores de software não estão incluídos nos custos do Azure Reserved VM Instances.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: banders
ms.openlocfilehash: 464e129314a369c14dcc6bc6c16eadf5f8bcf01f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052994"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Custos do software não incluídos no Azure Reserved VM Instances

A instância de máquina virtual reservada e os descontos de capacidade reservada do SQL aplicam-se apenas aos custos da infraestrutura e não aos custos do software. Se utilizar a VM do Windows e não tiver um Benefício Híbrido do Azure nas instâncias de máquina virtual reservada, ser-lhe-ão cobrados os medidores de software listados na secção a seguir. Para implementações PaaS do SQL, o custo dos IPs continuará a ser cobrado com um medidor distinto, caso o Benefício Híbrido do Azure não tenha sido selecionado.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Medidores de software do Windows não incluídos no custo de reserva

| MeterId | MeterName no ficheiro de utilização | Utilizado pela VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Burst (1 Core) | Série B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Burst (2 Core) | Série B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Burst (4 Core) | Série B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Burst (8 Core) | Série B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr (1 Core) | Todos, exceto a Série B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr (2 Core) | Todos, exceto a Série B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr (4 Core) | Todos, exceto a Série B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr (6 Core) | Todos, exceto a Série B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr (8 Core) | Todos, exceto a Série B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr (12 Core) | Todos, exceto a Série B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr (16 Core) | Todos, exceto a Série B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr (20 Core) | Todos, exceto a Série B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr (24 Core) | Todos, exceto a Série B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr (32 Core) | Todos, exceto a Série B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr (40 Core) | Todos, exceto a Série B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr (64 Core) | Todos, exceto a Série B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr (72 Core) | Todos, exceto a Série B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr (128 Core) | Todos, exceto a Série B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 Core) | Todos, exceto a Série B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr (96 Core) | Todos, exceto a Série B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Medidores de software dos serviços cloud não incluídos no custo de reserva

| MeterId | MeterName no ficheiro de utilização |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Licença de Serviços Cloud 1 vCPU|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Licença de Serviços Cloud 2 vCPU|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Licença de Serviços Cloud 4 vCPU|
|13103090-ca72-4825-ab12-7f16c4931d95|Licença de Serviços Cloud 8 vCPU|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Licença de Serviços Cloud 16 vCPU|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Licença de Serviços Cloud 20 vCPU|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Licença de Serviços Cloud 32 vCPU|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Licença de Serviços Cloud 64 vCPU|
|7a803026-244c-4659-834c-11e6b2d6b76f|Licença de Serviços Cloud 80 vCPU|

## <a name="get-rates-for-azure-meters"></a>Obter as tarifas dos medidores do Azure

Você pode obter o custo de cada um dos contadores com a AZure Retail Prices API. Para obter informações sobre como obter as tarifas de um medidor Azure, consulte [a visão geral dos preços de venda a retalho do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Gerir reservas do Azure](manage-reserved-vm-instance.md)
- [Compreender como o desconto das reservas é aplicado](../manage/understand-vm-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).