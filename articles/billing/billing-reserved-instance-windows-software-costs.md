---
title: Reservas de custos de software para o Azure | Microsoft Docs
description: Saiba quais medidores de software não estão incluídos nos custos de instância de VM reservada do Azure.
services: billing
documentationcenter: ''
author: yashar
manager: yashar
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 52e2e2503303c2a7525a3a6c156f648c097b27dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478615"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Custos de software não incluídos com instâncias de VM reservadas do Azure

A instância reservada da máquina virtual e os descontos de capacidade reservada do SQL aplicam-se apenas aos custos de infraestrutura e não aos custos de software. Se você usar a VM do Windows e não tiver um Benefício Híbrido do Azure em suas instâncias de máquina virtual reservada, você será cobrado pelos medidores de software listados na seção a seguir. Para implantações de PaaS do SQL, o custo IP continuará a ser cobrado usando um medidor separado se Benefício Híbrido do Azure não estiver selecionado.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Medidores de software do Windows não incluídos no custo de reserva

| MeterId | Medidorname no arquivo de uso | Usado pela VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows SVR Burst (1 núcleo) | Série B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows SVR Burst (2 núcleos) | Série B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows SVR Burst (4 núcleos) | Série B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows SVR Burst (8 núcleos) | Série B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows SVR (1 núcleo) | Todos, exceto a série B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows SVR (2 núcleos) | Todos, exceto a série B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows SVR (4 núcleos) | Todos, exceto a série B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows SVR (6 núcleos) | Todos, exceto a série B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows SVR (8 núcleos) | Todos, exceto a série B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows SVR (12 núcleos) | Todos, exceto a série B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows SVR (16 núcleos) | Todos, exceto a série B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows SVR (20 núcleos) | Todos, exceto a série B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows SVR (24 núcleos) | Todos, exceto a série B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows SVR (32 núcleos) | Todos, exceto a série B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows SVR (40 núcleos) | Todos, exceto a série B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows SVR (64 núcleos) | Todos, exceto a série B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows SVR (72 núcleos) | Todos, exceto a série B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows SVR (128 núcleos) | Todos, exceto a série B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows SVR (256 núcleos) | Todos, exceto a série B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows SVR (96 núcleos) | Todos, exceto a série B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Medidores de software de serviços de nuvem não incluídos no custo de reserva

| MeterId | Medidorname no arquivo de uso |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Serviços de nuvem 1 licença vCPU|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Serviços de nuvem 2 licença vCPU|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Serviços de nuvem 4 licença vCPU|
|13103090-ca72-4825-ab12-7f16c4931d95|Serviços de nuvem 8 licença vCPU|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Serviços de nuvem 16 licença do vCPU|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Serviços de nuvem 20 licença do vCPU|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Serviços de nuvem 32 licença do vCPU|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Serviços de nuvem 64 licença do vCPU|
|7a803026-244c-4659-834c-11e6b2d6b76f|Serviços de nuvem 80 licença do vCPU|

## <a name="rates-for-azure-meters"></a>Taxas para medidores do Azure

Você pode obter o custo de cada um desses medidores por meio da API RateCard do Azure. Para obter informações sobre como obter as tarifas para um medidor do Azure, consulte [obter informações de preço e metadados para recursos usados em uma assinatura do Azure](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar reservas para o Azure](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de reserva para sua assinatura pré-paga](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
