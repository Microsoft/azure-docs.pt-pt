---
title: Compreender como um desconto de reserva é aplicado ao Armazenamento de Discos do Azure
description: Saiba como o desconto de discos reservados do Azure é aplicado aos discos geridos SSD premium.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902139"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Compreender como o desconto de reserva é aplicado ao Armazenamento de Discos do Azure

Depois de comprar a capacidade reservada de discos do Azure, o desconto de reserva é automaticamente aplicado aos recursos dos discos que correspondam aos termos da reserva. O desconto de reserva aplica-se apenas à capacidade do disco. Os instantâneos de discos são cobrados com as tarifas pay as you go.

Para obter mais informações acerca da reserva de Discos do Azure, veja [Reduzir custos com a Reserva de Discos do Azure](../../virtual-machines/linux/disks-reserved-capacity.md).
Para obter mais informações acerca dos preços de reserva de Discos do Azure, veja os [Preços dos Discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

O desconto de reserva de discos do Azure é um desconto “use-it-or-lose-it” (utilizar ou perder) e aplica-se aos recursos de discos geridos por hora. Caso não tenha quaisquer recursos de discos geridos que cumpram os termos da reserva para uma determinada hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando elimina um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

## <a name="discount-examples"></a>Exemplos de desconto

Os seguintes exemplos mostram como o desconto de reserva de discos do Azure se aplica em função das implementações:

Vamos supor que comprou 100 discos P30 (~1 TiB por disco) de capacidade reservada na região EUA Oeste 2 para o prazo de 1 ano. Vamos assumir que o custo desta reserva de amostra é de 140 100 $. Pode optar por pagar o montante total adiantado ou pagar prestações mensais fixas de 11 675 $ por mês durante os próximos 12 meses.
Para estes exemplos, vamos assumir que se inscreveu num plano de pagamentos de reserva mensal. Os cenários seguintes descrevem o que acontece caso subaproveite a capacidade reservada, caso a utilize em excesso ou caso a coloque em escalões.

### <a name="underusing-your-capacity"></a>Subaproveitamento da capacidade

Vamos supor que, numa determinada hora dentro do período de reserva, implementou apenas 99 SSD premium P30 da sua reserva de 100 discos P30. O 1 P30 restante não é aplicado a essa hora e não é transferido.

### <a name="overusing-your-capacity"></a>Utilização em excesso da capacidade

Vamos supor que, numa determinada hora dentro do período de reserva, utilizou 101 discos premium P30. O desconto de reserva é aplicado a 100 discos P30 e o 1 P30 restante é cobrado de acordo com as tarifas pay as you go para aquela hora. Durante a próxima hora, se a utilização diminuir para 100 discos P30, toda a utilização será abrangida pela reserva.

### <a name="tiering-your-capacity"></a>Colocar a capacidade em escalões

Vamos supor que, numa determinada hora dentro do período da reserva, pretende utilizar um total de 200 SSDs premium P30. Durante os primeiros 30 minutos, utiliza apenas 100. Durante este período, a utilização está totalmente coberta, uma vez que fez uma reserva de 100 discos P30. Se, em seguida, descontinuar a utilização dos primeiros 100 (para que utilize zero) e, depois, começar a utilizar os outros 100 ao longo dos 30 minutos restantes, essa utilização também é coberta pela reserva.

![Representação de exemplos de subaproveitamento, utilização em excesso e colação em escalões da capacidade](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Reduzir custos com a Reserva de Discos do Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Reduzir custos com a Reserva de Discos do Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [O que são as reservas do Azure?](save-compute-costs-reservations.md)