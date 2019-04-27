---
title: Efetue o pré-pagamento do planos de software - as reservas do Azure | Documentos da Microsoft
description: Saiba como pode efetuar o pré-pagamento do planos de software poupar dinheiro sobre os custos de pay as you go.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771248"
---
# <a name="prepay-for-azure-software-plans"></a>Pré-pagamento dos planos de software do Azure

Quando efetuar o pré-pagamento do seu uso de software SUSE e Red Hat no Azure, pode poupar dinheiro sobre os custos de pay as you go. Os descontos só se aplicam a medidores de SUSE e Red Hat e não na utilização da máquina virtual. Pode comprar reservas para máquinas virtuais em separado para poupanças adicionais.

Pode comprar planos de software SUSE e Red Hat no portal do Azure. Comprar um plano:

- Tem de ter a função de proprietário para, pelo menos, uma empresa ou uma subscrição pay as you go.
- Para subscrições Enterprise, o **adicionar as instâncias reservadas** opção tem de estar ativada no [portal EA](https://ea.azure.com/). Se a definição estiver desativada, tem de ser um administrador de EA para a subscrição.
- Para o programa de fornecedor de soluções Cloud (CSP), os agentes de administrador ou agentes de vendas podem comprar planos de software.

## <a name="buy-a-software-plan"></a>Comprar um plano de software

1. Início de sessão no portal do Azure e aceda a [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Clique em **adicionar** e, em seguida, selecione o plano de software que pretende comprar.
Preencha os campos obrigatórios. Qualquer VM do Linux SUSE ou a VM de RedHat que corresponda os atributos de compra obtém o desconto. O número real de implementações que obter o desconto depende do escopo e quantidade selecionado.
3. Selecione uma subscrição. É utilizado para pagar o plano.
O método de pagamento da subscrição é cobrado os custos iniciais para a reserva. O tipo de subscrição tem de ser um contrato Enterprise (números da oferta: MS-AZR-0017P ou MS-AZR - 0148 P) ou pay as you go (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
    - Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida.
    - Para obter uma subscrição pay as you go, os custos são faturados ao método de pagamento do cartão de crédito ou por fatura da subscrição.
4. Selecione um âmbito. O âmbito pode abranger uma subscrição ou várias subscrições (âmbito partilhado).
    - Subscrição individual - o desconto de plano é aplicada a correspondência de utilização na subscrição.
    - O desconto de plano partilhado - é aplicado a correspondência de instâncias em qualquer subscrição no seu contexto de faturação. Para os clientes empresariais, o contexto de faturação é a inscrição e inclui todas as subscrições na inscrição. Para clientes pay as you go, o contexto de faturação é todas as subscrições pay as you go a criada pelo administrador de conta.
5. Selecione um produto para escolher o tamanho da VM e o tipo de imagem. O desconto aplica-se apenas ao tamanho de VM selecionada.
6. Selecione um termo de um ano ou três anos.
7. Escolha uma quantidade, o que é o número de instâncias VM pré-paga que pode obter o desconto de faturação.
8. Adicione o produto ao carrinho, revisão e a compra.

O desconto de reserva é aplicado automaticamente para o medidor de software que paga previamente. Custos de computação VM não são abrangidos pelo plano. Pode comprar as reservas de VM em separado.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Desconto aplica-se aos diferentes tamanhos de VM de SUSE

Como instâncias de VM reservadas, planos SUSE Linux oferecem a flexibilidade de tamanho de instância. Obter seu desconto aplica-se mesmo quando implementar uma VM que é um tamanho diferente do esquema de SUSE comprou. Para obter mais informações, consulte [compreender a forma como o desconto de plano de software é aplicado](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Desconto de plano de Red Hat

Os planos estão disponíveis apenas para máquinas de virtuais de Red Hat Enterprise Linux. O desconto não se aplica a VMs do Red Hat Enterprise Linux SAP HANA ou VMs de aplicações do Red Hat Enterprise Linux SAP Business.

Os descontos de plano de Red Hat aplicam-se apenas para o tamanho da VM que selecionou no momento da compra. Planos do RHEL não podem ser reembolsados ou trocados após a compra.


## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Não é possível cancelar ou um plano SUSE ou RedHat que comprou do exchange. Verifique a sua utilização para se certificar de que compra o plano adequado. Para obter ajuda identificar o que comprar, consulte [compreender a forma como o desconto de plano de software é aplicado](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

Para saber como gerir uma reserva, consulte [reservas de gerir o Azure](../../billing/billing-manage-reserved-vm-instance.md).

Para obter mais informações, consulte os artigos seguintes:

- [Quais são as reservas do Azure?](../../billing/billing-save-compute-costs-reservations.md)
- [Gerir reservas no Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Compreender a forma como o desconto de reserva de SUSE é aplicado](../../billing/billing-understand-suse-reservation-charges.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](../../billing/billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](../../billing/billing-understand-reserved-instance-usage-ea.md)
