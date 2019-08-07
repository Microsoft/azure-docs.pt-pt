---
title: Trocas e reembolsos de autoatendimento para reservas do Azure
description: Saiba como você pode trocar ou reembolsar reservas do Azure.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 5e38684500520d4565835456b94200aea399c938
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814112"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Trocas e reembolsos de autoatendimento para reservas do Azure

As reservas do Azure fornecem flexibilidade para ajudar a atender às suas necessidades em evolução. Pode trocar uma reserva por outra reserva do mesmo tipo. Também pode reembolsar uma reserva, até 50 000 $ por ano, se já não precisar da mesma.

A capacidade de troca self-service e de cancelamento não está disponível para os clientes com o Contrato Enterprise US Government. Outros tipos de assinatura do governo dos EUA, incluindo pagamento conforme o uso e o CSP, têm suporte.

Você deve ter acesso de proprietário na ordem de reserva para trocar ou reembolsar uma reserva existente.

## <a name="exchange-an-existing-reserved-instance"></a>Trocar uma instância reservada existente

Você pode trocar sua reserva com três etapas rápidas no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas que você deseja reembolsar e clique em **Exchange**.  
    ![Imagem de exemplo mostrando reservas a serem retornadas](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Selecione o produto de VM que pretende comprar e introduza uma quantidade Verifique se o novo total de compra é maior que o total de retorno. [Determine o tamanho correto antes de comprar](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Imagem de exemplo mostrando o produto da VM para comprar com um Exchange](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Examine e conclua a transação.  
    ![Imagem de exemplo mostrando o produto da VM para comprar com uma troca, concluindo o retorno](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Para reembolsar uma reserva, acesse **detalhes de reserva** e clique em **reembolso**.

## <a name="how-transactions-are-processed"></a>Como as transações são processadas

Em primeiro lugar, a Microsoft cancela a reserva existente e refinancia o valor proporcional para essa reserva. Se houver uma troca, a nova compra será processada. A Microsoft processa reembolsos usando um dos seguintes métodos, dependendo do tipo de conta e do método de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do contrato Enterprise

O Money é adicionado ao compromisso monetário para trocas e reembolsos se a compra original foi feita usando uma. Qualquer fatura excedente, desde que as compras originais, sejam reabertas e reclassificadas para garantir que o compromisso monetário seja usado. Se o termo de compromisso monetário que usa a reserva foi comprado não estiver mais ativo, o crédito será adicionado ao seu atual termo de compromisso monetário do Enterprise Agreement.

Se a compra original foi feita como excedente, a Microsoft emite um memorando de crédito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Pagamentos de fatura pagos conforme o uso e programa CSP

A nota fiscal de compra de reserva original é cancelada e uma nova fatura é criada para o reembolso. Para trocas, a nova fatura mostra o reembolso e a nova compra. O valor do reembolso é ajustado em relação à compra. Se você reembolsau uma reserva, o valor rateado permanece com a Microsoft e é ajustado em relação a uma compra futura de reserva.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes do cartão de crédito pago pelo uso

A nota fiscal original é cancelada e uma nova nota fiscal é criada. O dinheiro é reembolsado para o cartão de crédito que foi usado para a compra original. Se você tiver alterado o cartão, [entre em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>As políticas de cancelamento, troca e reembolso

O Azure tem as seguintes políticas para cancelamentos, trocas e reembolsos.

**Políticas de cancelamento**

- Se você cancelar uma reserva, poderá haver uma taxa de rescisão de 12% no início.
- O reembolso que você recebe para um cancelamento é o saldo proporcional restante menos a taxa de rescisão inicial de 12%. Para cancelar, vá para a reserva no portal do Azure e selecione **reembolso**.

**Políticas do Exchange**

- Você pode retornar várias reservas existentes para comprar uma nova reserva do mesmo tipo. Não é possível trocar reservas de um tipo para outro. Por exemplo, você não pode retornar uma reserva de VM para comprar uma reserva SQL.
- Somente proprietários de reserva podem processar uma troca. [Saiba como adicionar ou alterar usuários que podem gerenciar uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Um Exchange é processado como um reembolso e adquirir novamente – transações diferentes são criadas para o cancelamento e a nova compra. O valor de reserva rateado é reembolsado para as reservas que você Trade-in. Você é totalmente cobrado pela nova compra. O valor de reserva rateada é o valor residual diário rateado da reserva que está sendo retornada.
- Você pode trocar ou reembolsar reservas mesmo que o contrato Enterprise usado para comprar a reserva esteja expirado e renovado como um novo contrato.
- Você pode alterar qualquer propriedade de reserva, como tamanho, região, quantidade e termo com uma troca.
- O novo total de compra deve ser igual ou maior que o valor retornado.
- A nova reserva adquirida como parte do Exchange tem um novo termo desde o momento do Exchange.
- Não há nenhuma penalidade ou limites anuais para trocas.

**Políticas de reembolso**
- O valor total de reembolso não pode exceder $50000 USD em uma janela sem interrupção de 12 meses.
- Os reembolsos são calculados com base no preço mais baixo de seu preço de compra ou no preço atual da reserva.
- Somente proprietários de reserva podem processar um reembolso. [Saiba como adicionar ou alterar usuários que podem gerenciar uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- A Microsoft se reserva o direito de cobrar uma penalidade de 12% por qualquer retorno, embora a penalidade não seja cobrada no momento.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Armazenamento não Premium do Exchange para armazenamento Premium

Você pode trocar uma reserva adquirida para um tamanho de VM que não dá suporte ao armazenamento Premium para um tamanho de VM correspondente que o faz. Por exemplo, um _F1_ para um _D11_. Para fazer a troca, acesse detalhes de reserva e clique em **Exchange**. O Exchange não redefine o termo da instância reservada ou cria uma nova transação.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerenciar uma reserva, consulte [gerenciar reservas do Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:
    - [O que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
    - [Gerenciar reservas no Azure](billing-manage-reserved-vm-instance.md)
    - [Entenda como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
    - [Entenda o uso de reserva para sua assinatura pré-paga](billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa CSP (provedor de soluções na nuvem) do Partner Center](/partner-center/azure-reservations)
