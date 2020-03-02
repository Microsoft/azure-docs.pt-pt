---
title: Pré-pagamento para anfitriões dedicados azure para economizar dinheiro
description: Aprenda a comprar Anfitriões Dedicados Azure Casos Reservados para economizar nos custos da sua computação.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207748"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>Poupe custos com uma instância reservada de anfitriões dedicados azure

Quando se compromete com uma instância reservada de Anfitriões Dedicados Azure, pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de anfitriões dedicados em execução que correspondem ao âmbito de reserva e atributos. Você não precisa atribuir uma reserva a um anfitrião dedicado para obter os descontos. Uma compra de instância reservada cobre apenas a parte computada do seu uso e inclui custos de licenciamento de software. Consulte a [visão geral dos anfitriões dedicados do Azure para máquinas virtuais.](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Determine o anfitrião dedicado certo SKU antes de comprar


Antes de comprar uma reserva, deve determinar qual o anfitrião dedicado que precisa. Um SKU é definido para um anfitrião dedicado que representa a série e tipo VM. 

Comece por ver os tamanhos suportados para [a máquina virtual do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) ou [linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para identificar a série VM.

Em seguida, verifique se é apoiado em Anfitriões Dedicados Azure. A página de preços dos [Anfitriões Dedicados Azure](https://aka.ms/ADHPricing) tem a lista completa de anfitriões dedicados SKUs, as suas informações de CPU e várias opções de preços (incluindo instâncias reservadas).

Pode encontrar vários SKUs que suportam a série VM (com tipos diferentes). Identifique o melhor SKU comparando a capacidade do hospedeiro (número de vCPUs). Note que poderá aplicar a sua reserva a vários anfitriões dedicados SKUs suportando a mesma série VM (por exemplo DSv3_Type1 e DSv3_Type2), mas não em diferentes séries vM (como DSv3 e ESv3).



## <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Os casos reservados estão disponíveis para a maioria dos tamanhos de hospedeiro dedicados, com algumas exceções.

Os descontos de reserva não se aplicam para o seguinte:

- **Clouds** - As reservas não estão disponíveis para compra nas regiões da Alemanha ou da China.

- **Quota insuficiente** - Uma reserva que seja orientada para uma única subscrição deve ter quota vCPU disponível na subscrição para a nova instância reservada. Por exemplo, se a subscrição-alvo tem um limite de quota de 10 vCPUs para dSv3-Series, então não pode comprar uma reserva dedicada a anfitriões que apoiem esta série. O controlo de quotas para reservas inclui os VMs e os anfitriões dedicados já implantados na subscrição. Pode [criar um pedido](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) de aumento de quota para resolver esta questão.

- **Restrições** de capacidade - Em circunstâncias raras, o Azure limita a compra de novas reservas para o subconjunto de SKUs dedicados, devido à baixa capacidade numa região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Você pode comprar uma instância reservada de um anfitrião dedicado Azure no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pague a reserva [antecipadamente ou com pagamentos mensais.](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations) Estes requisitos aplicam-se à compra de uma instância reservada de anfitrião dedicado:

- Você deve estar em uma função de Proprietário para pelo menos uma subscrição EA ou uma subscrição com uma taxa pay-as-you-go.

- Para as assinaturas EA, a opção **Adicionar Instâncias Reservadas** deve ser ativada no [portal EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.

- Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem comprar reservas.

Para comprar uma instância:

1. Inicie sessão no  [portal do Azure](https://portal.azure.com/).

2. Selecione **todos os serviços**  **reservas**\>.

3. Selecione **Adicionar** para comprar uma nova reserva e, em seguida, clique em **Anfitriões Dedicados**.

4. Introduza os campos necessários. Executar anfitriões dedicados casos que correspondam aos atributos que seleciona qualificam-se para obter o desconto de reserva. O número real de instâncias dedicadas do anfitrião que obtêm o desconto depende do âmbito e quantidade selecionados.

Se tiver um acordo eA, pode utilizar a **opção Adicionar mais** adicionar rapidamente instâncias adicionais. A opção não está disponível para outros tipos de subscrição.

| **Campo**           | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscrição        | A subscrição costumava pagar a reserva. O método de pagamento da subscrição é cobrado os custos da reserva. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Contrato de Cliente da Microsoft ou uma subscrição individual com taxas de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os encargos são deduzidos do saldo do compromisso monetário, se disponível, ou cobrados como excesso. Para uma subscrição com taxas de pagamento, os encargos são cobrados para o cartão de crédito ou método de pagamento de fatura na subscrição. |
| Âmbito               | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Região              | A região de Azure que está coberta pela reserva.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Tamanho dedicado do hospedeiro | O tamanho dos casos dedicados do Anfitrião.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Duração                | Um ano ou três anos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Quantidade            | O número de casos adquiridos dentro da reserva. A quantidade é o número de instâncias de anfitriões dedicados que podem obter o desconto de faturação.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Âmbito** de grupo de recursos únicos — Aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionados.

- **Âmbito único de subscrição** — Aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.

- **Âmbito partilhado** — Aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que se encontram no contexto da faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.

## <a name="usage-data-and-reservation-utilization"></a>Dados de utilização e utilização de reservas

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Pode ver qual a instância vm que recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de utilização, consulte o uso da reserva Do Minée do [Foco para a sua inscrição na Empresa](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) se for cliente DaE. Se tiver uma subscrição individual, consulte o uso da [reserva Do Understand Azure para a sua subscrição Pay-As-You-Go](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

- Atualizar âmbito da reserva

- Flexibilidade de tamanho de instância (se aplicável)

- Propriedade

Você também pode dividir uma reserva em pedaços menores e fundir reservas já divididas. Nenhuma das alterações provoca uma nova transação comercial ou altera a data final da reserva.

Não é possível efazer os seguintes tipos de alterações após a compra, diretamente:

- Uma região de reserva existente

- SKU

- Quantidade

- Duração

No entanto, pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para mais informações, consulte [trocas de self-service e reembolsos para Reservas Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio.

## <a name="next-steps"></a>Passos seguintes

Para aprender a gerir uma reserva, consulte [Gerir reservas azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance).

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as Reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Usando anfitriões dedicados azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Preços dedicados dos anfitriões](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gerir Reservas no Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Compreender como o desconto das reservas é aplicado](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Custos de software Windows não incluídos nas reservas](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)


