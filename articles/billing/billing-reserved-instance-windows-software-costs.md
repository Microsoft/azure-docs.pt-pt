---
title: Custos de reservas de software para o Azure | Documentos da Microsoft
description: Saiba quais medidores de software não estão incluídos nos custos de instância de VM reservada do Azure.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: ee7c471cdd76829abc03fa4578d09b8a7303cb38
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57891438"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Custos de software não incluídos com instâncias de VM reservadas do Azure

Se não tiver um benefício de utilização híbrida do Azure nas suas instâncias de máquina virtual reservada, em seguida, que lhe é cobrada com os medidores de software listados nas secções seguintes.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Medidores de software do Windows não incluídos no custo de reserva

| Id do Medidor | MeterName no ficheiro de utilização | Utilizado por VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Períodos de rajada de instâncias reservadas do Windows de reserva Server (1 núcleo) | Série B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Períodos de rajada de instâncias reservadas do Windows de reserva Server (2 núcleos) | Série B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Períodos de rajada de instâncias reservadas do Windows de reserva Server (4 núcleos) | Série B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Períodos de rajada de instâncias reservadas do Windows de reserva Server (8 núcleos) | Série B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Windows de instâncias reservadas do Windows Server (1 núcleo) | Tudo, exceto a série B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Windows de instâncias reservadas do Windows Server (2 núcleos) | Tudo, exceto a série B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Windows de instâncias reservadas do Windows Server (4 núcleos) | Tudo, exceto a série B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Windows de instâncias reservadas do Windows Server (6 núcleos) | Tudo, exceto a série B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Windows de instâncias reservadas do Windows Server (8 núcleos) | Tudo, exceto a série B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Windows de instâncias reservadas do Windows Server (12 núcleos) | Tudo, exceto a série B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Windows de instâncias reservadas do Windows Server (16 núcleos) | Tudo, exceto a série B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr (20 Core) | Tudo, exceto a série B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Windows de instâncias reservadas do Windows Server (24 núcleos) | Tudo, exceto a série B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Windows de instâncias reservadas do Windows Server (32 núcleos) | Tudo, exceto a série B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Windows de instâncias reservadas do Windows Server (40 núcleos) | Tudo, exceto a série B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Windows de instâncias reservadas do Windows Server (64 núcleos) | Tudo, exceto a série B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Windows de instâncias reservadas do Windows Server (72 núcleos) | Tudo, exceto a série B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Windows de instâncias reservadas do Windows Server (128 núcleos) | Tudo, exceto a série B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 Core) | Tudo, exceto a série B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Windows de instâncias reservadas do Windows Server (96 núcleos) | Tudo, exceto a série B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Os medidores de software não incluídos no custo de reserva serviços em nuvem

| Id do Medidor | MeterName no ficheiro de utilização |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU licença|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU licença|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU licença|
|13103090-ca72-4825-ab12-7f16c4931d95|Serviços de 8 vCPU licença da cloud|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU licença|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU licença|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 Vcpus licença|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 Vcpus licença|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU licença|

## <a name="rates-for-azure-meters"></a>Taxas de medidores do Azure

Pode obter o custo de cada um destes medidores pela API de RateCard do Azure. Para obter informações sobre como obter as taxas de para um medidor do azure, consulte [Obtenha informações sobre o preço e os metadados de recursos utilizados numa subscrição do Azure](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
