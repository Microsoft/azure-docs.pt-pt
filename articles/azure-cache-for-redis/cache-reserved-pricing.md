---
title: Pré-pagamento para computação com capacidade reservada - Azure Cache for Redis
description: Pré-pagamento para Azure Cache para recursos computacionais Redis com capacidade reservada
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77530305"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pré-pagamento para Azure Cache para recursos computacionais Redis com capacidade reservada

O Azure Cache para o Redis ajuda-o agora a economizar dinheiro pagando os recursos da computação em comparação com os preços de pagamento. Com a Azure Cache para a redis capacidade reservada, você assume um compromisso inicial na cache por um período de um ou três anos para obter um desconto significativo nos custos do cálculo. Para adquirir o Azure Cache para a capacidade reservada redis, é necessário especificar a região azure, o nível de serviço e o termo.

Não precisa de atribuir a reserva a instâncias específicas do Azure Cache para os casos redis. Um Azure Cache já em execução para Redis ou aqueles que são recentemente implantados receberá automaticamente o benefício de preços reservados, até ao tamanho reservado da cache. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, as taxas de computação Azure Cache para Redis que correspondam aos atributos de reserva já não são cobradas às taxas de pagamento. Uma reserva não cobre taxas de rede ou armazenamento associadas à cache. No final do prazo de reserva, o benefício de faturação expira e o Azure Cache for Redis é cobrado ao preço de pagamento. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a oferta de capacidade reservada ao [Azure Cache for Redis.](https://azure.microsoft.com/pricing/details/cache)

Pode comprar O Cache Azure para a capacidade reservada redis no [portal Azure.](https://portal.azure.com/) Para comprar a capacidade reservada:

* Você deve estar no papel de proprietário para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes de administração ou agentes de vendas podem adquirir o Azure Cache para a capacidade reservada da Redis.

Para obter os detalhes sobre como os clientes empresariais e clientes Pay-As-You-Go são cobrados para compras de reservas, consulte o uso da [reserva Azure para](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) a sua inscrição na Enterprise e compreenda o uso da reserva Azure para a sua [subscrição Pay-As-You-Go](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-cache-size-before-purchase"></a>Determine o tamanho certo da cache antes da compra

A dimensão da reserva deve basear-se na quantidade total de cálculo utilizado pela cache existente ou em breve implantada numa região específica e utilizando o mesmo nível de serviço.

Por exemplo, suponhamos que está a executar um propósito geral, Gen5 – 32 vCore cache, e duas memórias otimizadas, Gen5 – 16 caches vCore. Além disso, supõe-se que planeia implementar dentro do próximo mês um propósito geral adicional, o servidor de base de dados Gen5 – 32 vCore, e uma memória otimizada, gen5 – 16 vCore servidor de base de dados. Suponhamos que saiba que vai precisar destes recursos por pelo menos 1 ano. Neste caso, deve adquirir uma reserva de 64 (2x32) vCores, 1 ano de reserva para fins gerais de base de dados únicas - Gen5 e um 48 (2x16 + 16) vCore 1 ano reserva para memória única de base de dados otimizada - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Comprar Cache Azure para a capacidade reservada redis

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** e, em seguida, no painel de reservas comprar, selecione **Azure Cache para Redis** para comprar uma nova reserva para os seus caches.
4. Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real do seu Cache Azure para os casos Redis que obtêm o desconto depende do âmbito e quantidade selecionados.


![Visão geral dos preços reservados](media/cache-reserved-pricing/cache-reserved-price.png)


A tabela seguinte descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição usada para pagar a Reserva de Capacidade Reservada Azure Cache para Redis. O método de pagamento da subscrição é cobrado os custos iniciais para o Azure Cache para a reserva de capacidade reservada redis. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma subscrição individual com preços de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Partilhado,** o desconto de reserva é aplicado ao Azure Cache para os casos Redis que executam em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Assinatura única,** o desconto de reserva é aplicado aos casos Azure Cache para Redis nesta subscrição. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado aos casos Azure Cache para Redis na subscrição selecionada e no grupo de recursos selecionados dentro dessa subscrição.
| Região | A região azure que é coberta pelo Azure Cache para redis reserva de capacidade reservada.
| Escalão de preço | O nível de serviço para o Azure Cache para servidores Redis.
| Termo | Um ano ou três anos
| Quantidade | A quantidade de recursos computacionais que estão a ser adquiridos dentro do Azure Cache para a reserva de capacidade reservada à Redis. A quantidade é uma série de caches na região azure selecionada e nível de serviço que estão sendo reservados e receberão o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar um Azure Cache para servidores Redis com a capacidade total de cache de 26 GB na região leste dos EUA, então especificaria a quantidade como 26 para maximizar o benefício para todos os caches.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) (Trocas e reembolsos self-service para Reservas do Azure).

## <a name="cache-size-flexibility"></a>Flexibilidade do tamanho do cache

A flexibilidade do tamanho da cache ajuda-o a escalar para cima ou para baixo dentro de um nível de serviço e região, sem perder o benefício de capacidade reservado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva é aplicado automaticamente ao Azure Cache para casos Redis que correspondam ao âmbito de reserva e atributos. Pode atualizar o âmbito da reserva através do portal Azure, PowerShell, Azure CLI ou da API.

*  Para saber como são aplicados descontos de capacidade reservados ao Azure Cache for Redis, consulte Compreender o desconto de [reserva azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

    * [O que são as reservas do Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Gerir o Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Compreender o desconto das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Compreender a utilização de reservas na sua subscrição Pay As You Go](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)

