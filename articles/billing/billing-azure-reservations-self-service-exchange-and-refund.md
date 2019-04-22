---
title: Trocas de Self-serviços e os reembolsos para as reservas do Azure | Documentos da Microsoft
description: Saiba como pode trocar ou o reembolso de reservas de Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/5/2019
ms.author: banders
ms.openlocfilehash: aa1a218fbf0bc7eacac65b50e4ee1f86791e2b3b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281986"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Trocas de Self-serviços e os reembolsos para as reservas do Azure

Reservas do Azure oferecem a flexibilidade para o ajudar a atender às suas necessidades em constante evolução. Podem trocar uma reserva para a reserva outra do mesmo tipo. Também pode reembolsar uma reserva, até 50 000 USD por ano, se já não precisa dele.

Capacidade de exchange e cancelar self-service não está disponível para clientes do US Government Enterprise Agreement. São suportados outros tipos de subscrição administração pública dos EUA, incluindo o pay as you go e o CSP.

## <a name="exchange-an-existing-reserved-instance"></a>Uma instância reservada existente do Exchange

Podem trocar sua reserva com três passos rápidos no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecione as reservas que deseja reembolsar e clique em **Exchange**.  
    ![Imagem de exemplo que mostra as reservas para retornar](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Selecione o produto VM que pretende comprar e escreva uma quantidade. Certifique-se de que o novo total de compra é mais do que o total de retorno. [Determinar o tamanho correto antes de comprar](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Imagem de exemplo que mostra o produto VM para aquisição com uma troca](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Reveja e conclua a transação.  
    ![Imagem de exemplo que mostra o produto VM para aquisição com uma troca, concluindo o retorno](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Para reembolso de uma reserva, aceda a **detalhes da reserva** e clique em **reembolso**.

## <a name="how-return-and-exchange-transactions-are-processed"></a>Como retornar e transações do exchange são processadas

Em primeiro lugar, a Microsoft cancela a reserva existente e reembolsos a quantidade de pro-rata para esse reserva. Se houver uma troca, a compra de novos é processada. Microsoft processa reembolsos através de um dos seguintes métodos, dependendo do tipo de conta e método de pagamento:

### <a name="enterprise-agreement-customers"></a>Clientes do contrato Enterprise

Dinheiro é adicionado para o compromisso monetário para trocas e reembolsos se a compra original foi efetuada através de um. Qualquer utilização excessiva notas fiscais, pois as compras originais são reabertas e rerated para se certificar de que a alocação monetária é utilizado. Se o termo de compromisso monetário, utilizando que a reserva foi comprada já não estiver ativo, em seguida, crédito é adicionado ao seu termo atual de compromisso monetário do contrato enterprise.

Se a compra original foi feita como utilização excedida, a Microsoft emite uma nota de crédito.

### <a name="pay-as-you-go-invoice-payment-customers-and-cloud-solution-provider-program"></a>Os clientes de pagamento de nota fiscal de pay as you go e o programa de fornecedor de soluções de Cloud

Nota fiscal de compra de reserva original é cancelada e, em seguida, uma nova nota fiscal é criada para o reembolso. Para trocas, a nova nota fiscal mostra o reembolso e a compra de novo. A quantidade de reembolso é ajustada em relação a compra. Se reembolsada apenas uma reserva, em seguida, a quantidade de rateada mantém-se com a Microsoft e ele será ajustado em relação a uma compra de reserva futuras.

### <a name="pay-as-you-go-credit-card-customers"></a>Clientes de cartão de crédito pay as you go

A nota fiscal original é cancelada, e é criada uma nova nota fiscal. O dinheiro é reembolsado para o cartão de crédito que foi utilizado para a compra original. Se alterou seu cartão [contacte o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Políticas do Exchange

- Pode retornar vários reservas existentes para comprar uma reserva nova do mesmo tipo. Não é possível trocar as reservas de um tipo para outro. Por exemplo, não é possível devolver uma reserva de VM para comprar uma reserva de SQL.
- Apenas os proprietários de reserva podem processar uma troca. [Saiba como adicionar ou alterar os utilizadores que podem gerir uma reserva](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Uma troca é processada como um reembolso e repurchase – transações diferentes são criadas para o cancelamento e a compra de novo. A quantidade de reserva é calculado é reembolsada para as reservas essa troca. É-lhe cobrado totalmente para a compra de novo. A quantidade de reserva é calculado é o valor de residual é calculado de diário da reserva a ser devolvido.
- Pode trocar ou reservas de reembolso, mesmo se o contrato enterprise utilizados para adquirir a reserva expirou e foi renovado como um novo contrato.
- Pode alterar qualquer propriedade de reserva como tamanho, região, quantidade e o termo com uma troca.
- O total de compra de novos deve ser igual ou de ser maior que o valor retornado.
- A nova reserva adquirida como parte do exchange tem um novo termo começando pelo momento do exchange.
- Não existe nenhuma penalidade ou limites de anuais para trocas.

## <a name="refund-policies"></a>Políticas de reembolso

- A quantidade de reembolso total não pode exceder os 50 000 USD numa janela de 12 meses sem interrupção.
- Apenas os proprietários de reserva podem processar um reembolso. [Saiba como adicionar ou alterar os utilizadores que podem gerir uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- A Microsoft se reserva o direito a cobrar uma penalidade de 12% para qualquer devolve, embora a penalidade atualmente não é cobrada.

## <a name="exchange-a-non-premium-storage-vm-reservation-for-a-premium-storage-reservation"></a>Uma reserva de VM para uma reserva de armazenamento premium de armazenamento não premium do Exchange

Podem trocar uma reserva comprada para um tamanho de VM que não suporta o armazenamento premium para um tamanho VM correspondente que faz. Por exemplo, um _F1_ para um _F1s_. Para fazer com que o exchange, vá para detalhes de reserva e clique em **Exchange**. O exchange não repor o termo da instância reservada ou criar uma nova transação.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- Para saber como gerir uma reserva, consulte [Gerir reservas de Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as reservas do Azure, veja os artigos seguintes:
    - [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
    - [Gerir reservas no Azure](billing-manage-reserved-vm-instance.md)
    - [Compreender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
    - [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
    - [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de fornecedor de soluções (CSP) do parceiro Center na nuvem](/partner-center/azure-reservations)
