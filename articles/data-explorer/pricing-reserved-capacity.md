---
title: Pré-pagamento para marcação do Explorador de Dados Azure para economizar dinheiro
description: Saiba como comprar o Azure Data Explorer capacidade reservada para economizar nos custos do Seu Explorador de Dados Azure.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969272"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Pré-pagamento para unidades de marcação do Explorador de Dados Azure com capacidade reservada do Azure Data Explorer

Poupe dinheiro com o Azure Data Explorer pré-pagando as unidades de marcação em comparação com os preços de pagamento. Com a capacidade reservada ao Azure Data Explorer, compromete-se a utilizar o Azure Data Explorer por um período de um ou três anos para obter um desconto significativo nos custos de marcação do Azure Data Explorer. Para adquirir a capacidade reservada do Azure Data Explorer, basta especificar o termo, aplicando-se a todas as implementações do Azure Data Explorer em todas as regiões.

Ao comprar uma reserva, está a pré-pagar os custos de marcação por um período de um ou três anos. Assim que comprar uma reserva, as taxas de marcação do Azure Data Explorer que correspondem aos atributos de reserva já não são cobradas pelas tarifas pagas. Os clusters do Azure Data Explorer que já estão em execução ou os recém-implantados terão automaticamente o benefício. Esta reserva não cobre a computação, a rede ou os custos de armazenamento associados aos clusters. A capacidade reservada para estes recursos tem de ser adquirida separadamente. No final do prazo de reserva, o benefício de faturação expira e as unidades de marcação do Azure Data Explorer são faturadas ao preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a página de preços do [Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/).

Pode comprar capacidade reservada ao Azure Data Explorer no [portal Azure.](https://portal.azure.com) Para comprar capacidade reservada ao Azure Data Explorer:

* Você deve ser o proprietário de pelo menos uma assinatura Enterprise ou Pay-As-You-Go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Em alternativa, se essa definição for desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem adquirir a capacidade reservada do Azure Data Explorer.

Para mais detalhes sobre como os clientes empresariais e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte:
* [Compreenda o uso da reserva Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Compreenda o uso da reserva Azure para a sua subscrição Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Determine o uso certo de marcação antes da compra

O tamanho da reserva deve basear-se no número total de unidades de marcação do Azure Data Explorer utilizadas pelos aglomerados existentes ou em breve implantados do Azure Data Explorer. O número de unidades de marcação é igual ao número de núcleos de cluster de motor do Azure Data Explorer em produção (sem incluir o dev/teste SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Comprar capacidade reservada ao Azure Data Explorer

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços** > **Reservations** > **Comprar Reservas Agora**. Selecione **Adicionar**
1. No painel **Select Product Type,** selecione **Azure Data Explorer** para adquirir uma nova reserva para unidades de marcação do Azure Data Explorer. 
1. Selecione **Comprar**
1. Preencha os campos necessários. 

    ![Página de compra](media/pricing-reserved-capacity/purchase-page.png)

1. Reveja o custo da marcação azure Data Explorer reserva da reserva de capacidade reservada na secção **Custos.**
1. Selecione **Comprar**.
1. Selecione **Ver esta Reserva** para ver o estado da sua compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se precisar cancelar a reserva de capacidade reservada ao Azure Data Explorer, pode haver uma taxa de rescisão antecipada de 12%. Os reembolsos baseiam-se no preço mais baixo do seu preço de compra ou no preço atual da reserva. Os reembolsos estão limitados a 50 000 $ por ano. O reembolso que receber corresponde ao saldo rateado restante menos a taxa de cessação antecipada de 12%. Para solicitar um cancelamento, vá à reserva no portal Azure e selecione **Reembolso** para criar um pedido de apoio.

Se precisar de alterar a reserva de capacidade reservada do Azure Data Explorer para outro termo, pode trocá-la por outra reserva que seja de valor igual ou superior. A data de início do período da nova reserva não transita da reserva trocada. O prazo de 1 ou 3 anos começa a partir do momento em que cria a nova reserva. Para solicitar uma troca, vá à reserva no portal Azure e selecione **Exchange** para criar um pedido de apoio.

Para mais informações sobre como trocar ou reembolsar reservas, consulte [as trocas de reservas e reembolsos.](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

## <a name="manage-your-reserved-capacity-reservation"></a>Gerir a sua reserva de capacidade reservada

O desconto de reserva das unidades de marcação do Azure Data Explorer é aplicado automaticamente ao número de unidades de marcação que correspondem ao âmbito e atributos reservados ao Azure Data Explorer. 


> [!NOTE]
> * Pode atualizar o âmbito da reserva de capacidade reservada do Azure Data Explorer através do [portal Azure,](https://portal.azure.com)PowerShell, CLI ou através da API.
> * Para aprender a gerir a reserva de capacidade reservada ao Azure Data Explorer, consulte a capacidade reservada do [Azure Data Explorer.](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são Reservas Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gerir Reservas do Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Compreender o desconto das Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio.
