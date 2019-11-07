---
title: Pagar antecipadamente pela marcação de Data Explorer do Azure para economizar dinheiro
description: Saiba como comprar a capacidade reservada do Azure Data Explorer para economizar em seus custos de Data Explorer do Azure.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681632"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Pagar antecipadamente pelas unidades de marcação de Data Explorer do Azure com a capacidade reservada do Azure Data Explorer

Economize dinheiro com o Azure Data Explorer por prepagar pelas unidades de marcação comparadas aos preços pagos conforme o uso. Com o Azure Data Explorer capacidade reservada, você faz um compromisso com o uso do Azure Data Explorer por um período de um ou três anos para obter um desconto significativo nos custos de marcação de Data Explorer do Azure. Para comprar a capacidade reservada do Azure Data Explorer, você só precisa especificar o termo, ela será aplicada a todas as implantações do Azure Data Explorer em todas as regiões.

Ao comprar uma reserva, você está pagando pelos custos de marcação por um período de um ou três anos. Assim que você comprar uma reserva, os encargos de marcação do Azure Data Explorer que correspondam aos atributos de reserva não serão mais cobrados com as tarifas pagas conforme o uso. Os clusters do Azure Data Explorer que já estão em execução ou que foram implantados recentemente obterão o benefício automaticamente. Essa reserva não abrange os encargos de computação, rede ou armazenamento associados aos clusters. A capacidade reservada para esses recursos precisa ser adquirida separadamente. No final do prazo de reserva, o benefício de cobrança expira e as unidades de marcação do Azure Data Explorer são cobradas pelo preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [página de preços do data Explorer do Azure](https://azure.microsoft.com/pricing/details/data-explorer/).

Você pode comprar o Azure Data Explorer capacidade reservada no [portal do Azure](https://portal.azure.com). Para comprar a capacidade reservada do Azure Data Explorer:

* Você deve ser o proprietário de pelo menos uma assinatura de empresa ou de pagamento conforme o uso.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com). Como alternativa, se essa configuração estiver desabilitada, você deverá ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar a capacidade reservada do Azure Data Explorer.

Para obter detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte:
* [Entender o uso de reserva do Azure para seu registro corporativo](../billing/billing-understand-reserved-instance-usage-ea.md) 
* [Entenda o uso de reserva do Azure para sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Determinar o uso correto da marcação antes da compra

O tamanho da reserva deve ser baseado no número total de unidades de marcação de Data Explorer do Azure usadas pelos clusters de Data Explorer do Azure existentes ou em breve para ser implantados. O número de unidades de marcação é igual ao número de núcleos de cluster do mecanismo de Data Explorer do Azure em produção (sem incluir o SKU de desenvolvimento/teste). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Comprar a capacidade reservada do Azure Data Explorer

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços** > **reservas** > **comprar agora**. Selecione **Adicionar**
1. No painel **Selecionar tipo de produto** , selecione **Azure data Explorer** para comprar uma nova reserva para as unidades de marcação de data Explorer do Azure. 
1. Selecione **comprar**
1. Preencha os campos obrigatórios. 

    ![Página de compra](media/pricing-reserved-capacity/purchase-page.png)

1. Examine o custo da reserva de capacidade reservada de marcação de Data Explorer do Azure na seção de **custos** .
1. Selecione **Comprar**.
1. Selecione **exibir esta reserva** para ver o status de sua compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se você precisar cancelar sua reserva de capacidade reservada do Azure Data Explorer, poderá haver uma taxa de rescisão de 12% no início. Os reembolsos são baseados no preço mais baixo do seu preço de compra ou no preço atual da reserva. Os reembolsos estão limitados a 50 000 $ por ano. O reembolso que receber corresponde ao saldo rateado restante menos a taxa de cessação antecipada de 12%. Para solicitar um cancelamento, vá para a reserva no portal do Azure e selecione **reembolso** para criar uma solicitação de suporte.

Se você precisar alterar a reserva de capacidade reservada do Azure Data Explorer para outro termo, poderá Exchange para outra reserva que seja de valor igual ou maior. A data de início do período da nova reserva não transita da reserva trocada. O termo de 1 ou 3 anos começa com quando você cria a nova reserva. Para solicitar uma troca, vá para a reserva no portal do Azure e selecione **Exchange** para criar uma solicitação de suporte.

Para obter mais informações sobre como trocar ou reembolsar reservas, consulte trocas [e reembolsos de reserva](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Gerenciar sua reserva de capacidade reservada

O desconto de reserva das unidades de marcação do Azure Data Explorer é aplicado automaticamente ao número de unidades de marcação que correspondem ao escopo e aos atributos de reserva de capacidade reservada do Azure Data Explorer. 


> [!NOTE]
> * Você pode atualizar o escopo do Azure Data Explorer reserva de capacidade reservada por meio do [portal do Azure](https://portal.azure.com), do PowerShell, da CLI ou da API.
> * Para saber como gerenciar a reserva de capacidade reservada do Azure Data Explorer, consulte [gerenciar a capacidade reservada do azure data Explorer](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)
* [Gerir o Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
* [Compreender o desconto das Reservas do Azure](../billing/billing-understand-reservation-charges.md)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](../billing/billing-understand-reserved-instance-usage.md)
* [Compreender a utilização de reservas na inscrição Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
