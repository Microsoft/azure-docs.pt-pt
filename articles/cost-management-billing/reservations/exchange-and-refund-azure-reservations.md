---
title: Self-service exchanges and refunds for Azure Reservations (Trocas e reembolsos self-service para Reservas do Azure)
description: Saiba como pode trocar ou reembolsar Reservas do Azure. Para trocar ou reembolsar reservas, tem de ter acesso de proprietário à Encomenda da Reserva.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: b0f17149eb646b6108dc6a81922e6e5b00f143d6
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560547"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Trocas e reembolsos personalizados das Reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a dar resposta às suas necessidades em evolução. Pode trocar uma reserva por outra reserva do mesmo tipo. Por exemplo, pode trocar uma reserva de máquina virtual para comprar outra reserva para qualquer outro tamanho de VM ou região. Do mesmo modo, uma reserva da Base Dados SQL PaaS pode ser trocada para comprar outra reserva de qualquer tipo ou região da Base de Dados SQL PaaS. Também pode reembolsar reservas, mas a soma total da alocação de reservas canceladas no seu âmbito de faturação (como EA, Contrato de Cliente Microsoft e Contrato de Parceiro da Microsoft) não pode exceder 50 000 USD num período de 12 meses. A capacidade reservada do Azure Databricks, a reserva da solução VMware no Azure da CloudSimple, a reserva do Azure Red Hat Open Shift, os planos Red Hat e os planos SUSE Linux não são elegíveis para reembolso.

A capacidade de troca self-service e de cancelamento não está disponível para os clientes com o Contrato Enterprise US Government. São suportados outros tipos de subscrições de administração pública dos EUA, incluindo Pay As You Go e Fornecedor de Soluções Cloud (CSP).

> [!NOTE]
> - **Tem de ter acesso de proprietário na Encomenda de Reserva para trocar ou reembolsar uma reserva existente**. Pode [Adicionar ou alterar os utilizadores que podem gerir reservas](./manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
> - Atualmente, a Microsoft não cobra taxas de rescisão antecipadas relativamente a reembolsos de reservas. Poderemos cobrar estas taxas em reembolsos feitos no futuro. Não temos data para a aplicação desta taxa neste momento.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Como trocar ou reembolsar reservas já existentes

Pode trocar a sua reserva no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas que quer reembolsar e selecione **Trocar**.  
    [![Imagem de exemplo a mostrar reservas a serem devolvidas](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Selecione o produto de VM que pretende comprar e introduza uma quantidade Confirme que o novo total da compra é superior ao total da devolução. [Determine o tamanho certo antes da compra](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Imagem de exemplo a mostrar o produto VM a comprar com uma troca](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Reveja e conclua a transação.  
    [![Imagem de exemplo a mostrar o produto VM a comprar com uma troca, concluindo a devolução](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Para reembolsar uma reserva, aceda a **Detalhes da Reserva** e selecione **Reembolsar**.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Troca de armazenamento não premium por armazenamento premium

Pode trocar uma reserva comprada para um tamanho de VM que não suporta armazenamento premium por um tamanho de VM correspondente que o faça. Por exemplo, um _F1_ por um _F1s_. Para fazer a troca, aceda a Detalhes da Reserva e selecione **Trocar**. A troca não repõe o termo da instância reservada nem cria uma nova transação. 

## <a name="how-transactions-are-processed"></a>Como as transações são processadas

Primeiro, a Microsoft cancela a reserva existente e reembolsa o valor rateado dessa reserva. Se houver uma troca, a nova compra será processada. A Microsoft processa reembolsos através de um dos seguintes métodos, consoante o tipo de conta e o método de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do contrato Enterprise

O dinheiro é adicionado à alocação monetária para trocas e reembolsos se a compra original tiver sido feita com uma. Se o termo de alocação monetária que usa a reserva comprado já não estiver ativo, o crédito será adicionado ao seu termo de alocação monetária do contrato Enterprise atual. O crédito é válido durante 90 dias a partir da data de reembolso. O crédito não utilizado expira após 90 dias.

Se a compra original tiver sido feita como utilização excedida, a fatura original na qual a reserva foi comprada e todas as faturas posteriores são reabertas e reajustadas. A Microsoft emite um memorando de crédito para os reembolsos.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagamentos de faturas pay as you go e programa CSP

A fatura de compra da reserva original é cancelada e uma nova fatura é criada para o reembolso. Para trocas, a nova fatura mostra o reembolso e a nova compra. O valor do reembolso é ajustado em relação à compra. Se apenas reembolsou uma reserva, o valor rateado permanece com a Microsoft e é ajustado em relação a uma compra futura de uma reserva. Se tiver comprado uma reserva à taxa pay as you go e, depois, passar para CSP, essa reserva pode ser devolvida e recomprada sem penalizações.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes de cartão de crédito pay as you go

A fatura original é cancelada e cria-se uma nova. O dinheiro é reembolsado para o cartão de crédito que serviu para a compra original. Se tiver alterado o cartão, [contacte o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Políticas de cancelamento, de troca e de reembolso

O Azure tem as seguintes políticas para cancelamentos, trocas e reembolsos.

**Políticas de troca**

- Pode devolver várias reservas existentes para comprar uma nova reserva do mesmo tipo. Não é possível trocar reservas de um tipo por outro. Por exemplo, não pode devolver uma reserva de VM para comprar uma reserva de SQL. Pode alterar uma propriedade da reserva, como a família, a série, a versão, o SKU, a região, a quantidade e o termo, com uma troca.
- Apenas os proprietários da reserva podem processar uma troca. [Saiba como Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).
- Uma troca é processada como um reembolso e uma recompra, isto é, são criadas transações diferentes para o cancelamento e para a compra nova. O valor de reserva rateado é reembolsado em relação às reservas envolvidas na troca. A nova compra é-lhe cobrada na íntegra. O valor da reserva proporcional é o valor residual diário rateado da reserva a ser devolvida.
- Pode trocar ou reembolsar reservas mesmo que o contrato Enterprise que serviu para comprar a reserva tenha expirado e sido renovado como um novo contrato.
- A alocação vitalícia da reserva nova deve ser igual ou superior à alocação remanescente da reserva devolvida. Exemplo: numa reserva de três anos que custe 100 $ por mês e é trocada após o 18.º pagamento, a alocação vitalícia da reserva nova deve ser 1800 $ ou mais (paga mensalmente ou à cabeça).
- A nova reserva comprada como parte da troca tem um novo termo, com início desde o momento da troca.
- Não existe qualquer penalização nem limites anuais para trocas.

**Políticas de reembolso**

- Atualmente, não estamos a cobrar uma taxa de rescisão antecipada, mas poderá haver uma taxa de 12% para cancelamentos no futuro.
- A alocação total cancelada não pode exceder 50 000 USD num período de 12 meses para um perfil de faturação ou uma única inscrição. Por exemplo, numa reserva de três anos que custe 100 USD por mês e reembolsada no 18.º mês, a alocação cancelada é de 1800 USD. Após o reembolso, o limite disponível novo para reembolso será de 48 200 $. A partir de 365 dias a seguir ao reembolso, o limite de 48 200 será aumentado em 1800 USD e o seu conjunto novo será de 50 000 USD. Qualquer outro cancelamento de reserva para o perfil de faturação ou inscrição de EA esgotará o mesmo conjunto e será aplicada a mesma lógica de renovação.
- O Azure não processará qualquer reembolso que exceda o limite de 50 000 USD num período de 12 meses para um perfil de faturação ou inscrição de EA.
- Os reembolsos são calculados com base no preço mais baixo do preço de compra ou do preço atual da reserva.
- Apenas os proprietários de encomendas de reservas podem processar reembolsos. [Saiba como Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default).

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
    - [Reservas do Azure no programa CSP](/partner-center/azure-reservations)