---
title: Pré-pagamento para planos de software - Reservas Azure
description: Saiba como pode pagar os planos de software para economizar dinheiro acima dos seus custos de pagamento.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973137"
---
# <a name="prepay-for-azure-software-plans"></a>Pré-pagamento dos planos de software do Azure

Quando paga o seu software SUSE e RedHat em Azure, pode economizar dinheiro acima dos seus custos de pagamento. Os descontos aplicam-se apenas aos contadores SUSE e RedHat e não ao uso da máquina virtual. Você pode comprar reservas para máquinas virtuais separadamente para poupanças adicionais.

Pode comprar planos de software SUSE e RedHat no portal Azure. Para comprar um plano:

- Você deve ter o papel de proprietário para pelo menos uma Empresa ou subscrição individual com preços pay-as-you-go.
- Para subscrições Enterprise, a opção **Adicionar Instâncias Reservadas** deve ser ativada no [EA Portal](https://ea.azure.com/). Se a definição estiver desativada, deve ser um Administrador EA para a subscrição.
- Para o programa Cloud Solution Provider (CSP), os agentes de administração ou agentes de vendas podem comprar os planos de software.

## <a name="buy-a-software-plan"></a>Comprar um plano de software

1. Inscreva-se no portal Azure e vá às [Reservas.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)
2. Clique em **Adicionar** e, em seguida, selecione o plano de software que pretende comprar.
Preencha os campos necessários. Qualquer VM SUSE Linux ou RedHat VM que corresponda aos atributos do que compra recebe o desconto. O número real de implementações que obtêm o desconto depende do âmbito e quantidade selecionados.
3. Selecione uma subscrição. É usado para pagar o plano.
O método de pagamento de subscrição é cobrado os custos iniciais da reserva. O tipo de subscrição deve ser um Acordo de Empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P).
    - Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida.
    - Para uma subscrição individual com preços de pagamento, os encargos são cobrados no cartão de crédito da subscrição ou no método de pagamento da fatura.
4. Selecione um âmbito. O âmbito pode abranger uma subscrição ou várias subscrições (âmbito partilhado).
    - Assinatura única - O desconto do plano é aplicado à utilização correspondente na subscrição.
    - Partilhado - O desconto do plano é aplicado a instâncias correspondentes em qualquer subscrição no seu contexto de faturação. Para os clientes empresariais, o contexto de faturação é a inscrição e inclui todas as subscrições na inscrição. Para um plano individual com clientes de preços pay-as-you-go, o contexto de faturação é todos os planos individuais com subscrições de preços pay-as-you-go criadas pelo administrador da conta.
5. Selecione um produto para escolher o tamanho VM e o tipo de imagem. O desconto aplica-se apenas ao tamanho de VM selecionado.
6. Selecione um mandato de um ano ou três anos.
7. Escolha uma quantidade, que é o número de casos de VM pré-pagos que podem obter o desconto de faturação.
8. Adicione o produto ao carrinho, reveja e compre.

O desconto de reserva é automaticamente aplicado ao medidor de software que pré-paga. As despesas computação VM não estão cobertas pelo plano. Pode comprar as reservas vm separadamente.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>O desconto aplica-se a diferentes tamanhos de VM SUSE

Tal como os casos de VM reservados, os planos SUSE Linux oferecem flexibilidade de tamanho de instância. O seu desconto aplica-se mesmo quando implementa um VM de tamanho diferente do plano SUSE que comprou. Para mais informações, consulte [entenda como é aplicado o desconto do plano de software.](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)

## <a name="redhat-plan-discount"></a>Desconto do plano RedHat

Os planos estão disponíveis apenas para máquinas virtuais Red Hat Enterprise Linux. O desconto não se aplica aos VMs RedHat Enterprise Linux SAP HANA VMs ou RedHat Enterprise Linux SAP Business Apps VMs.

Os descontos do plano RedHat aplicam-se apenas ao tamanho VM que seleciona no momento da compra. Os planos rHEL não podem ser reembolsados ou trocados após a compra.


## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Não pode cancelar ou trocar um plano SUSE ou RedHat que comprou. Verifique a sua utilização para se certificar de que compra o plano adequado. Para ajudar a identificar o que comprar, consulte [Entenda como é aplicado o desconto do plano de software.](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio.

## <a name="next-steps"></a>Passos seguintes

Para aprender a gerir uma reserva, consulte [as reservas do Manage Azure.](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)

Para saber mais, consulte os seguintes artigos:

- [O que são Reservas Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gerir Reservas no Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Entenda como é aplicado o desconto de reserva SUSE](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
