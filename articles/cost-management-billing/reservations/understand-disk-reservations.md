---
title: Compreender o desconto de reservas para armazenamento de discos do Azure
description: Saiba como o desconto de discos reservados do Azure é aplicado aos seus discos geridos SSD premium do Azure.
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 3541e3f12a6d0b1beecdd67821d31f71b47f6c68
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509470"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Compreender como o desconto de reserva é aplicado ao armazenamento de discos do Azure

Depois de comprar a capacidade reservada de discos do Azure, é aplicado automaticamente um desconto de reserva aos recursos dos discos que correspondam aos termos da reserva. O desconto de reserva aplica-se apenas aos SKUs de discos. Os instantâneos dos discos são cobrados às taxas pay as you go.

Para obter mais informações sobre a reserva de discos do Azure, veja [Reduzir custos com a reserva de discos do Azure](../../virtual-machines/linux/disks-reserved-capacity.md). Para obter mais informações sobre os preços de reserva de Discos do Azure, veja [Preços do Azure Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

O desconto de reserva de discos do Azure é um desconto “use-it-or-lose-it” ("utilizar ou perder"). É aplicado aos recursos de discos geridos de hora em hora. Numa determinada hora, se não tiver recursos de disco gerido que cumpram os termos da reserva, perde uma quantidade de reserva para essa hora. As horas não utilizadas não transitam.

Quando elimina um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente, as horas reservadas perdem-se.

## <a name="discount-examples"></a>Exemplos de desconto

Os seguintes exemplos mostram como o desconto de reserva de discos do Azure se aplica em função da sua implementação.

Imagine que compra e reserva cem discos P30 na região E.U.A. Oeste 2 para um termo de um ano. Cada disco tem aproximadamente 1 TiB de armazenamento. Vamos presumir que o custo deste exemplo de reserva é 140,100 €. Pode optar por pagar o montante total à cabeça ou pagar prestações mensais fixas de 11 675 € durante os próximos 12 meses.

Os cenários seguintes descrevem o que acontece caso subaproveite a capacidade reservada, caso a utilize em excesso ou caso a coloque em escalões. Nestes exemplos, vamos partir do princípio de que se inscreveu num plano de pagamentos de reserva mensal.

### <a name="underusing-your-capacity"></a>Subaproveitamento da capacidade

Imagine que implementa apenas 99 dos cem discos P30 de unidade de estado sólido premium do Azure reservados durante uma hora dentro do período da reserva. O disco P30 que sobra não é aplicado no decorrer dessa hora. Também não transita.

### <a name="overusing-your-capacity"></a>Utilização em excesso da capacidade

Suponhamos que imagina 101 discos P30 SSD premium durante uma hora do período de reserva. O desconto de reserva aplica-se apenas a cem desses discos. O disco P30 restante é cobrado às taxas pay as you go nessa hora. Durante a próxima hora, se a utilização diminuir para cem discos P30, toda a utilização é abrangida pela reserva.

### <a name="tiering-your-capacity"></a>Colocar a capacidade em escalões

Vamos supor que, numa determinada hora dentro do período da reserva, pretende utilizar um total de 200 SSDs P30 premium. Suponha também que utiliza apenas cem discos durante os primeiros 30 minutos. Durante esse período, a utilização está totalmente coberta, uma vez que fez uma reserva de cem discos P30. Se, em seguida, descontinuar a utilização dos primeiros 100 (para que utilize zero) e, depois, começar a utilizar os outros 100 ao longo dos 30 minutos restantes, essa utilização também é coberta pela reserva.

![Exemplo de subaproveitamento, utilização em excesso e colação em escalões da capacidade](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Reduzir custos com a Reserva de Discos do Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Reduzir custos com a Reserva de Discos do Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
