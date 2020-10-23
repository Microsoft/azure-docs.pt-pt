---
title: Compreender o desconto de Instâncias Reservadas do Azure Dedicated Host
description: Saiba como o desconto do Azure Reserved VM Instance é aplicado ao Azure Dedicated Host.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 8d273aae3588a006f7b0a55d2798b7a43c040c9b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148372"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Como o desconto de reserva do Azure é aplicado ao Azure Dedicated Host

Depois de comprar uma Instância Reservada do Azure Dedicated Host, o desconto de reserva é automaticamente aplicado aos anfitriões dedicados que correspondam aos atributos e à quantidade da reserva. As reservas abrangem os custos de computação dos seus anfitriões dedicados.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando elimina um anfitrião dedicado, o desconto de reserva é aplicado automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Desconto de reserva para anfitriões dedicados

A Instância Reservada do Azure Dedicated Host oferece um desconto para o preço da infraestrutura de computação utilizada nos anfitriões dedicados. O desconto aplica-se aos anfitriões dedicados, independentemente de estarem ou não a ser utilizados por máquinas virtuais. A reserva não abrange custos adicionais, como licenciamento, utilização de rede ou consumo de armazenamento por parte das máquinas virtuais implementadas nos anfitriões dedicados.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda,  [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](./save-compute-costs-reservations.md)

- [Utilizar o Azure Dedicated Host](../../virtual-machines/dedicated-hosts.md)

- [Preços do Dedicated Hosts](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerir reservas do Azure](./manage-reserved-vm-instance.md)

- [Compreender a utilização de reservas na sua subscrição Pay As You Go](./understand-reserved-instance-usage.md)

- [Compreender a utilização de reservas na inscrição Enterprise](./understand-reserved-instance-usage-ea.md)

- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations)