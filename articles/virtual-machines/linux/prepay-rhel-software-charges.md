---
title: Comprar planos de Red Hat Enterprise Linux - reservas do Azure | Documentos da Microsoft
description: Saiba como pode efetue o pré-pagamento para a sua utilização do Red Hat e poupar dinheiro sobre os custos de pay as you go.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653144"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Efetue o pré-pagamento do Red Hat Enterprise Linux planos das reservas do Azure

Efetue o pré-pagamento para a sua utilização do Red Hat e poupar dinheiro sobre os custos de pay as you go. Os descontos só se aplicam a medidores de Red Hat e não na utilização da máquina virtual. Pode comprar reservas para máquinas virtuais em separado para poupar ainda mais.

Pode comprar planos de software do Red Hat no portal do Azure. Comprar um plano:

- Deve estar numa função de proprietário de, pelo menos, uma empresa ou uma subscrição pay as you go.
- Para subscrições Enterprise, **adicionar as instâncias reservadas** tem de estar ativada no [portal EA](https://ea.azure.com). Em alternativa, se essa definição estiver desativada, tem de ser um administrador de EA da subscrição.
- Para o programa de fornecedor de soluções Cloud (CSP), os agentes de administrador ou agentes de vendas podem comprar planos do Red Hat.

## <a name="buy-a-red-hat-software-plan"></a>Comprar um plano de software do Red Hat

1. Aceda a [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) no portal do Azure.
1. Selecione **adicionar** e selecione o Red Hat Linux.
1. Preencha os campos obrigatórios. Qualquer VM do Red Hat Linux que corresponda os atributos de compra obtém o desconto. O número real de implementações que obter o desconto depende do escopo e quantidade selecionado.

    | Campo      | Descrição|
    |:------------|:--------------|
    |Name        |O nome desta compra.|
    |Subscrição|A subscrição utilizada para pagar para este plano. O método de pagamento da subscrição é cobrado os custos iniciais para a reserva. O tipo de subscrição tem de ser um contrato enterprise (números da oferta: MS-AZR-0017P ou MS-AZR - 0148 P) ou pay as you go (números da oferta: MS-AZR-0003P ou MS-AZR - 0023 P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.|
    |Âmbito       |O âmbito pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <ul><li>Subscrição individual - o desconto de plano é aplicada a utilização do Red Hat Linux nesta subscrição. </li><li>O desconto de plano partilhado - é aplicado a utilização do Red Hat Linux em qualquer subscrição com o contexto de faturação. Para os clientes empresariais, o escopo compartilhado é a inscrição e inclui todas as subscrições na inscrição. Para clientes pay as you go, o âmbito partilhado é todas as subscrições pay as you go a criada pelo administrador de conta.</li></ul>|
    |Planear     |Selecione o plano de Red Hat Linux. Para obter ajuda a identificar o que comprar, consulte Compreender a forma como o desconto de reserva de software do Red Hat Linux Enterprise é aplicado.|
    |Tamanho da VM     |Preços do Red Hat Linux dependem do número de vCPUs na VM. Selecione a opção que representa o número de vCPUs nas suas VMs do Red Hat Linux.|
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de VMs que está comprando para este plano de Red Hat Linux. A quantidade é o número de instâncias de Red Hat Linux que podem obter o desconto de faturação em execução.|
1. Selecione **Comprar**.
1. Selecione **ver esta reserva** para ver o estado da sua compra.

O desconto de reserva é automaticamente aplicado às quaisquer máquinas de virtuais de Red Hat em execução que corresponda a reserva. O desconto aplica-se apenas para o medidor de Red Hat. Os custos de computação VM não são abrangidos por este plano.

## <a name="discount-applies-to-different-vm-sizes"></a>Desconto aplica-se aos diferentes tamanhos de VM

Como instâncias de VM reservadas, planos de Red Hat Linux oferecem a flexibilidade de tamanho de instância. Isso significa que se aplica o desconto até mesmo quando implementa uma VM que é um tamanho diferente do esquema de Red Hat comprou. Para obter mais informações, consulte Compreender a forma como o desconto de reserva de software do Red Hat Linux Enterprise é aplicado.

## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Não é possível cancelar ou um plano de Red Hat que comprou do exchange. Verifique a sua utilização para se certificar de que compra o plano adequado. Para obter ajuda a identificar o que comprar, consulte Compreender a forma como o desconto de reserva de software do Red Hat Linux Enterprise é aplicado.

## <a name="next-steps"></a>Passos Seguintes

Para saber como gerir uma reserva, consulte [reservas de gerir o Azure](../../billing/billing-manage-reserved-vm-instance.md).

Para obter mais informações, consulte os artigos seguintes:

- [Quais são as reservas do Azure?](../../billing/billing-save-compute-costs-reservations.md)
- [Gerir reservas no Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Compreender a forma como o desconto de reserva do Red Hat é aplicado
- [Compreender a utilização de reserva para a sua subscrição pay as you go](../../billing/billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda tiver mais perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.