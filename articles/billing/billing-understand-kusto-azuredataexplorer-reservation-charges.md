---
title: Compreender como o desconto de reserva é aplicado ao Azure Data Explorer
description: Saiba como o desconto de reserva é aplicado ao medidor de margem de lucro do Azure Data Explorer.
services: data-explorer
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 658ef17632a30877867c887360d127792370bde2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770021"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Compreender como o desconto de reserva é aplicado ao Azure Data Explorer

Depois de comprar uma capacidade reservada do Azure Data Explorer, o desconto de reserva é automaticamente aplicado aos recursos do Azure Data Explorer que correspondam aos atributos e à quantidade da reserva. A reserva inclui os custos de margem de lucro do Data Explorer do Azure. Não inclui a computação, a rede, o armazenamento nem qualquer outro recurso do Azure utilizado para operar o cluster do Azure Data Explorer. As reservas para estes recursos devem ser compradas separadamente.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva funciona numa base de “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Desconto de reserva aplicado aos clusters do Azure Data Explorer

O desconto de reserva é aplicado ao consumo de margem de lucro do Azure Data Explorer por hora. Para os recursos do Azure Data Explorer que não são executados durante uma hora completa, o desconto de reserva é automaticamente aplicado aos outros recursos do Azure Data Explorer que correspondam aos atributos da reserva. O desconto pode ser aplicado a recursos do Azure Data Explorer que estejam a ser executados em simultâneo. Se não tiver recursos do Azure Data Explorer que sejam executados durante um hora completa que correspondam aos atributos da reserva, não tirará partido completo do desconto de reserva para essa hora.

> [!NOTE]
> * É **vivamente recomendado** comprar [capacidade reservada](../virtual-machines/windows/prepay-reserved-vm-instances.md) para as máquinas virtuais utilizadas para o cluster do Azure Data Explorer para maximizar a poupança de capacidade reservada.
> * A compra de reserva aplicará descontos a todas as regiões.

## <a name="examples"></a>Exemplos

Os exemplos seguintes mostram como o desconto da capacidade reservada do Azure Data Explorer é aplicado em função do número de unidades de margem de lucro que comprou e quando estas estão em execução.
Por exemplo, para o tamanho do cluster de motor: **2 VMs D11_v2**, o total de encargos a pedido é para quatro unidades do medidor de margem de lucro do Azure Data Explorer por hora. 

**Cenário 1** 

Compra uma capacidade reservada do Azure Data Explorer para 8 unidades de marcação do Azure Data Explorer. Executa um cluster de motor de duas VMs D13_v2 com um total de 16 núcleos que cobra por 16 unidades de margem de lucro do Azure Data Explorer por hora e corresponde ao resto dos atributos da reserva. É-lhe cobrado o preço pay as you go por oito núcleos de utilização de computação do Azure Data Explorer e obtém o desconto de reserva para uma hora de utilização da unidade de margem de lucro do Azure Data Explorer de oito núcleos.

Para o resto destes exemplos, assuma que a capacidade reservada do Azure Data Explorer que comprou é para um cluster do Azure Data Explorer de 16 núcleos e que o resto dos atributos da reserva correspondem ao cluster do Azure Data Explorer em execução.

**Cenário 2** 

Executa dois clusters de motor do Azure Data Explorer com oito núcleos cada durante uma hora em duas regiões diferentes. O desconto de reserva de 16 núcleos é aplicado ao cluster e às 16 unidades de margem de lucro do Azure Data Explorer que consomem.

**Cenário 3** 

Executa um cluster de motor do Azure Data Explorer com 16 núcleos das 13:00 às 13:30. Executa outro cluster de motor do Azure Data Explorer com 16 núcleos das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.

**Cenário 4** 

Executa um cluster de motor do Azure Data Explorer com 16 núcleos das 13:00 às 13:45. Executa outro cluster de motor do Azure Data Explorer com 16 núcleos das 13:30 às 14:00. É-lhe cobrado o preço pay as you go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de margem de lucro do Azure Data Explorer durante o resto do tempo.

Para compreender e ver a aplicação das Reservas do Azure nos relatórios de utilização da faturação, veja [compreender a utilização das reservas do Azure](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas do Azure, veja os seguintes artigos:

* [Efetuar pré-pagamento pelos recursos de computação do Azure Data Explorer com a capacidade reservada do Data Explorer do Azure](../data-explorer/pricing-reserved-capacity.md)  
* [O que são as reservas do Azure](billing-save-compute-costs-reservations.md)  
* [Gerir reservas do Azure](billing-manage-reserved-vm-instance.md)  
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](billing-understand-reserved-instance-usage.md)
* [Compreender a utilização de reservas na inscrição Enterprise](billing-understand-reserved-instance-usage-ea.md)
* [Compreender a utilização de reservas nas subscrições do CSP](https://docs.microsoft.com/partner-center/azure-reservations)
