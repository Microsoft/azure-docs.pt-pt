---
title: Economize custos com a Azure VMware Solution reservado instância
description: Saiba como comprar uma instância reservada para a Azure VMware Solution.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: fa354d4fbfef868ea1e6783656be7871669f200d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951422"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Economize custos com a Azure VMware Solution

Quando se compromete com uma instância reservada da Azure VMware Solution, pode economizar dinheiro. O desconto de reserva é aplicado automaticamente ao número de anfitriões da Azure VMware Solution que correspondem ao âmbito e atributos da reserva. Você não precisa atribuir uma reserva a um anfitrião dedicado para obter os descontos. Uma compra de instância reservada cobre apenas a parte computacional do seu uso e inclui custos de licenciamento de software. Consulte a [visão geral da Solução VMware Azure](introduction.md).

## <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Casos reservados estão disponíveis com algumas exceções.

-   **Nuvens**   - As reservas só estão disponíveis nas regiões listadas nos [Produtos disponíveis por página da região.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)

-   **Quota**   insuficiente - Uma reserva que seja atribuída a uma subscrição única/partilhada deve ter a quota de anfitriões disponível na subscrição para o novo caso reservado. Pode [criar um pedido de aumento de quota](enable-azure-vmware-solution.md) para resolver esta questão.

-   **Oferta Elegibilidade**- Você precisará de um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md)   com a Microsoft.

-   Restrições de **capacidade**   - Em circunstâncias raras, o Azure limita a compra de novas reservas para a Azure VMware Solution host SKUs, devido à baixa capacidade numa região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Pode comprar uma instância reservada de um anfitrião Azure VMware Solution no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pague a reserva [adiantadamente ou com pagamentos mensais.](../cost-management-billing/reservations/prepare-buy-reservation.md)

Estes requisitos aplicam-se à compra de um caso de anfitrião dedicado reservado:

-   Você deve estar numa função proprietário para pelo menos uma subscrição EA ou uma subscrição com uma taxa de pagamento como você-go.

-   Para as assinaturas EA, a opção **Add Reserved Instances**   deve ser ativada no portal [EA](https://ea.azure.com/). Em alternativa, se essa definição estiver desativada, terá de ser o Administrador EA da subscrição.

Para comprar uma instância:

1. Inicie sessão no  [portal do Azure](https://portal.azure.com/).

2. Selecione **Todas as**Reservas de   >  **Serviços.**

3.  **Selecione Adicionar**   para comprar uma nova reserva e, em seguida, selecione **Azure VMware Solution**.

4. Introduza os campos necessários. A Correr Azure VMware Solution anfitriões que correspondem aos atributos que seleciona qualificam para obter o desconto de reserva. O número real dos anfitriões da Solução Azure VMware que obtêm o desconto dependem do âmbito e quantidade selecionados.

   Se tiver um acordo EA, pode utilizar a **opção Adicionar mais**   rapidamente para adicionar instâncias adicionais. A opção não está disponível para outros tipos de subscrição.

   | Campo        |  Descrição |
   | ------------ | ------------ |
   | Subscrição | A assinatura usada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Microsoft Customer Agreement ou uma subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os custos são deduzidos do saldo de fidelização monetária, se disponível, ou cobrados como utilização excedida. Para uma subscrição com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição. |
   | Âmbito        | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar:<br><ul><li><b>Âmbito de grupo de recursos único — Aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li><b>Âmbito de subscrição único — Aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li><b>Âmbito compartilhado — Aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estão no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>       |
   | Região       | A região de Azure que está coberta pela reserva.   |
   | Tamanho do anfitrião    | AV36    |
   | Termo         | Um ano ou três anos.  |
   | Quantidade     | O número de casos a serem comprados dentro da reserva. A quantidade é o número de anfitriões da Azure VMware Solution que podem obter o desconto de faturação.    |

## <a name="usage-data-and-reservation-utilization"></a>Dados de utilização e utilização de reservas

Os dados de utilização têm um preço efetivo de zero pela utilização que obtém um desconto de reserva. Pode ver qual a instância Azure VMware Solution recebeu o desconto de reserva para cada reserva.

Para obter mais informações sobre como os descontos de reserva aparecem nos dados de utilização, e você é um cliente da EA, consulte [o uso da reserva Understand Azure para a sua inscrição na Enterprise.](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) Se tiver uma subscrição individual, consulte [o uso da reserva Understand Azure para a sua subscrição Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode fazer os seguintes tipos de alterações numa reserva após a compra:

-   Atualizar âmbito da reserva

-   Flexibilidade do tamanho do exemplo (se aplicável)

-   Propriedade

Você também pode dividir uma reserva em pedaços menores ou fundir reservas. Nenhuma das alterações causa uma nova transação comercial ou altera a data de fim da reserva.

>[!NOTE]
>Uma vez adquirida a sua reserva, não poderá efetuar os seguintes tipos de alterações, diretamente:
>
> - Uma região de reserva existente
> - SKU
> - Quantidade
> - Duração
>
>No entanto, pode *trocar*   uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para mais informações, consulte [trocas de self-service e reembolsos para Reservas Azure.](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)