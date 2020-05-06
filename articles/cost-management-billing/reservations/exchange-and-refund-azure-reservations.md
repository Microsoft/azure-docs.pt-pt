---
title: Self-service exchanges and refunds for Azure Reservations (Trocas e reembolsos self-service para Reservas do Azure)
description: Saiba como pode trocar ou reembolsar Reservas do Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 174ed17056bf49b541d55719f4058141e88e7ea5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192115"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Trocas e reembolsos personalizados das Reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a dar resposta às suas necessidades em evolução. Pode trocar uma reserva por outra reserva do mesmo tipo. Também pode reembolsar uma reserva, até 50 000 $ por ano, se já não precisar da mesma. O limite máximo do reembolso aplica-se a todas as reservas no âmbito do contrato com a Microsoft.

A capacidade de troca self-service e de cancelamento não está disponível para os clientes com o Contrato Enterprise US Government. Os outros tipos de subscrições US Government, incluindo Pay As You Go e CSP, são suportados.

Deve ter acesso de proprietário na Encomenda de Reserva para trocar ou reembolsar uma reserva existente.

## <a name="exchange-an-existing-reserved-instance"></a>Trocar uma instância reservada já existente

Pode trocar a sua reserva com três passos rápidos no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas que quer reembolsar e selecione **Trocar**.  
    ![Imagem de exemplo a mostrar reservas a serem devolvidas](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Selecione o produto de VM que pretende comprar e introduza uma quantidade Confirme que o novo total da compra é superior ao total da devolução. [Determine o tamanho certo antes da compra](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Imagem de exemplo a mostrar o produto da VM a comprar com uma troca](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Reveja e conclua a transação.  
    ![Imagem de exemplo a mostrar o produto da VM a comprar com uma troca, concluindo a devolução](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Para reembolsar uma reserva, aceda a **Detalhes da Reserva** e selecione **Reembolsar**.

## <a name="how-transactions-are-processed"></a>Como as transações são processadas

Primeiro, a Microsoft cancela a reserva existente e reembolsa o valor rateado dessa reserva. Se houver uma troca, a nova compra será processada. A Microsoft processa reembolsos através de um dos seguintes métodos, consoante o tipo de conta e o método de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do contrato Enterprise

O dinheiro é adicionado à alocação monetária para trocas e reembolsos se a compra original tiver sido feita com uma. Quaisquer faturas de utilização excedida desde as compras originais são reabertas e reclassificadas para garantir que a alocação monetária é utilizada. Se o termo de alocação monetária que usa a reserva comprado já não estiver ativo, o crédito será adicionado ao seu termo de alocação monetária do contrato Enterprise atual. O crédito é válido durante 90 dias a partir da data de reembolso. O crédito não utilizado expira após 90 dias.

Se a compra original foi feita como utilização excedida, a Microsoft emite uma nota de crédito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagamentos de faturas pay as you go e programa CSP

A fatura de compra da reserva original é cancelada e uma nova fatura é criada para o reembolso. Para trocas, a nova fatura mostra o reembolso e a nova compra. O valor do reembolso é ajustado em relação à compra. Se apenas reembolsou uma reserva, o valor rateado permanece com a Microsoft e é ajustado em relação a uma compra futura de uma reserva.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes de cartão de crédito pay as you go

A fatura original é cancelada e cria-se uma nova. O dinheiro é reembolsado para o cartão de crédito que serviu para a compra original. Se tiver alterado o cartão, [contacte o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Políticas de cancelamento, de troca e de reembolso

O Azure tem as seguintes políticas para cancelamentos, trocas e reembolsos.

**Políticas de troca**

- Pode devolver várias reservas existentes para comprar uma nova reserva do mesmo tipo. Não é possível trocar reservas de um tipo por outro. Por exemplo, não pode devolver uma reserva de VM para comprar uma reserva de SQL.
- Apenas os proprietários da reserva podem processar uma troca. [Saiba como Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Uma troca é processada como um reembolso e uma recompra, isto é, são criadas transações diferentes para o cancelamento e a nova compra. O valor de reserva rateado é reembolsado em relação às reservas que envolver na troca. A nova compra é-lhe cobrada na íntegra. O valor da reserva proporcional é o valor residual diário rateado da reserva a ser devolvida.
- Pode trocar ou reembolsar reservas mesmo que o contrato Enterprise que serviu para comprar a reserva tenha expirado e sido renovado como um novo contrato.
- Pode alterar uma propriedade da reserva, como a família, a série, a versão, o SKU, a região, a quantidade e o termo, com uma troca.
- O novo total da compra deve ser igual ou maior do que o valor devolvido.
- A nova reserva comprada como parte da troca tem um novo termo, com início desde o momento da troca.
- Não existe qualquer penalização nem limites anuais para trocas.

**Políticas de reembolso**
- Poderá ser aplicada uma taxa de 12% de rescisão antecipada para cancelamentos no futuro. Neste momento, não estamos a cobrar esta penalização.
- O valor total do reembolso não pode exceder os 50.000 USD num período sem interrupção de 12 meses.
- Os reembolsos são calculados com base no preço mais baixo do preço de compra ou do preço atual da reserva.
- Apenas os proprietários de encomendas de reservas podem processar reembolsos. [Saiba como Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Troca de armazenamento não premium por armazenamento premium

Pode trocar uma reserva comprada para um tamanho de VM que não suporta armazenamento premium por um tamanho de VM correspondente que o faça. Por exemplo, um _F1_ por um _F1s_. Para fazer a troca, aceda a Detalhes da Reserva e selecione **Trocar**. A troca não repõe o termo da instância reservada nem cria uma nova transação.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
    - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
    - [Gerir Reservas no Azure](manage-reserved-vm-instance.md)
    - [Compreender como o desconto das reservas é aplicado](../manage/understand-vm-reservation-charges.md)
    - [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)
