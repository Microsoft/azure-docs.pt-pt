---
title: Descontos do plano de reserva do Red Hat – Azure
description: Saiba como os descontos do plano do Red Hat são aplicados ao software Red Hat em máquinas virtuais.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 311ce7edc617b02e62f4e4fefc7393318f99a1a9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148278"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Compreender de que forma o desconto do plano de reserva do software Red Hat Linux Enterprise é aplicado ao Azure

Depois de comprar um plano Red Hat Linux, o desconto é automaticamente aplicado às máquinas virtuais (VMs) implementadas pelo Red Hat que correspondam à reserva. Um plano do Red Hat Linux abrange o custo da execução do software Red Hat numa VM do Azure.

Para comprar o plano certo do Red Hat Linux, tem de saber que VMs do Red Hat são executadas e o número de vCPUs nessas VMs. Utilize as seguintes secções para ajudar a identificar, a partir do ficheiro CSV de utilização, o plano a ser comprado.

## <a name="discount-applies-to-different-vm-sizes"></a>O desconto aplica-se a diferentes tamanhos de VM

Tal como as Instâncias de VM Reservada, as compras do plano do Red Hat oferecem flexibilidade de tamanho de instância. Isto significa que o seu desconto se aplica mesmo quando implementa uma VM com uma contagem de vCPU diferente. O desconto aplica-se a diferentes tamanhos de VM no plano de software.

O valor do desconto depende da proporção apresentada nas seguintes tabelas. A proporção compara a quantidade relativa para cada tamanho de VM nesse grupo. A proporção depende das vCPUs da VM. Utilize o valor da proporção para calcular quantas instâncias de VM obtêm o desconto do plano do Red Hat Linux.

Por exemplo, se comprar um plano do Red Hat Linux Enterprise Server para uma VM com 1 ou 4 vCPUs, a proporção dessa reserva é 1. O desconto abrange o custo do software Red Hat para:

- 1 VM implementada com 1 ou 4 vCPUs
- ou 0,46 ou cerca de 46% dos custos do Red Hat Enterprise Linux para uma VM com 5 ou mais vCPUs.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Nomes do marketplace do portal do Azure:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Ver os medidores do Red Hat Enterprise Linux aos quais o plano de aplica](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as reservas, veja os seguintes artigos:

- [O que são as reservas do Azure](save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento dos planos do software Red Hat com as reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerir reservas do Azure](manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).