---
title: Pré-pagamento para Azure Anfitriões Dedicados para economizar dinheiro
description: Saiba como comprar Azure Dedicados Anfitriões Instâncias Reservadas para economizar nos seus custos de cálculo.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 1450f03ae72e79daca5a5ae2c04506e6bee5180b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672271"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Poupe custos com reservas de anfitriões dedicados Azure

Quando se comprometer com uma instância reservada de Anfitriões Dedicados Azure, pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de anfitriões dedicados em execução que correspondem ao âmbito e atributos da reserva. Você não precisa atribuir uma reserva a um anfitrião dedicado para obter os descontos. Uma compra de instância reservada cobre apenas a parte computacional do seu uso e inclui custos de licenciamento de software. Consulte a [visão geral dos anfitriões dedicados a Azure para máquinas virtuais.](./dedicated-hosts.md)

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Determine o anfitrião dedicado direito SKU antes de comprar


Antes de comprar uma reserva, deverá determinar qual o anfitrião dedicado que precisa. Um SKU é definido para um anfitrião dedicado que representa a série e tipo VM. 

Comece por ver os tamanhos suportados para [a máquina virtual do Windows](./sizes.md) ou [linux](./sizes.md) para identificar a série VM.

Em seguida, verifique se é suportado em Azure Dedicados Anfitriões. [A página de preços dos anfitriões dedicados Azure](https://aka.ms/ADHPricing) tem a lista completa de anfitriões dedicados SKUs, suas informações de CPU, e várias opções de preços (incluindo instâncias reservadas).

Pode encontrar vários SKUs a apoiar-lhe séries VM (com tipos diferentes). Identifique o melhor SKU comparando a capacidade do hospedeiro (número de vCPUs). Note que poderá aplicar a sua reserva a vários anfitriões dedicados SKUs suportando a mesma série VM (por exemplo, DSv3_Type1 e DSv3_Type2), mas não em diferentes séries VM (como DSv3 e ESv3).



## <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Casos reservados estão disponíveis para a maioria dos tamanhos de hospedeiro dedicados, com algumas exceções.

Os descontos de reserva não se aplicam ao seguinte:

- **Nuvens** - As reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Contingente insuficiente** - Uma reserva que seja atribuída a uma única subscrição deve ter a quota vCPU disponível na subscrição para o novo caso reservado. Por exemplo, se a subscrição-alvo tiver um limite de quota de 10 vCPUs para dSv3-Series, então não pode comprar uma reserva dedicada a anfitriões que apoiem esta série. O controlo de quotas para reservas inclui os VMs e os anfitriões dedicados já implantados na subscrição. Pode [criar um pedido de aumento de quota](../azure-portal/supportability/resource-manager-core-quotas-request.md) para resolver esta questão.

- **Restrições de capacidade** - Em circunstâncias raras, o Azure limita a compra de novas reservas para o subconjunto de SKUs de hospedeiro dedicado, devido à baixa capacidade numa região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar um exemplo reservado de um Azure Dedicado Ao Anfitrião no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md). Estes requisitos aplicam-se à compra de um anfitrião dedicado reservado:

- Você deve estar numa função proprietário para pelo menos uma subscrição EA ou uma subscrição com uma taxa de pagamento como você-go.

- Para as assinaturas EA, a opção **Add Reserved Instances** deve ser ativada no [portal EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.

- Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** \> **Reservas**.

3. **Selecione Adicionar** para comprar uma nova reserva e, em seguida, clique **em Anfitriões Dedicados**.

4. Introduza os campos necessários. Executar Eventos de Anfitriões Dedicados que correspondam aos atributos que seleciona qualificam para obter o desconto de reserva. O número real das suas instâncias dedicadas ao anfitrião que obtêm o desconto dependem do âmbito e quantidade selecionados.

Se tiver um acordo EA, pode utilizar a **opção Adicionar mais** rapidamente para adicionar instâncias adicionais. A opção não está disponível para outros tipos de subscrição.

| **Campo**           | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscrição        | A assinatura usada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Microsoft Customer Agreement ou uma subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária), se disponível, ou cobrados como utilização excedida. Para uma subscrição com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição. |
| Âmbito               | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | A região de Azure que está coberta pela reserva.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Tamanho do hospedeiro dedicado | O tamanho das instâncias dedicadas do hospedeiro.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Termo                | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantidade            | O número de casos a serem comprados dentro da reserva. A quantidade é o número de casos de anfitriões dedicados que podem obter o desconto de faturação.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.

- **Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.

- **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.

## <a name="usage-data-and-reservation-utilization"></a>Dados de utilização e utilização de reservas

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Pode ver qual a instância VM que recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de utilização, consulte [o uso da reserva Understand Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) se for cliente da EA. Se tiver uma subscrição individual, consulte [o uso da reserva Understand Azure para a sua subscrição Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva

- Flexibilidade do tamanho do exemplo (se aplicável)

- Propriedade

Você também pode dividir uma reserva em pedaços menores e fundir reservas já divididas. Nenhuma das alterações causa uma nova transação comercial ou altera a data de fim da reserva.

Não é possível efetuar diretamente os seguintes tipos de alterações após a compra:

- Uma região de reserva existente

- SKU

- Quantidade

- Duração

No entanto, pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Utilizar o Azure Dedicated Host](./dedicated-hosts.md)

- [Preços do Dedicated Hosts](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerir Reservas no Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Compreender como o desconto das reservas é aplicado](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Custos de software Windows não incluídos nas reservas](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)