---
title: Pré-pagamento para computação com capacidade reservada - Azure Cache para Redis
description: Pré-pagamento por Azure Cache para redis computa recursos com capacidade reservada
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598552"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pré-pagamento por Azure Cache para redis computa recursos com capacidade reservada

A azure Cache para Redis agora ajuda-o a economizar dinheiro ao pré-pagar recursos compute em comparação com os preços de pay-as-you-go. Com a Azure Cache para a capacidade reservada da Redis, você assume um compromisso inicial em cache por um período de um ou três anos para obter um desconto significativo sobre os custos de cálculo. Para comprar a Cache Azure para a capacidade reservada redis, você precisa especificar a região de Azure, nível de serviço e termo.

Não precisa de atribuir a reserva a cache Azure específico para instâncias Redis. Um Cache Azure já em execução para Redis ou os recém-implantados obterão automaticamente o benefício de preços reservados, até ao tamanho da cache reservada. Ao comprar uma reserva, está a pré-pagar os custos do cálculo por um período de um ou três anos. Assim que comprar uma reserva, o Azure Cache para Redis calcula os encargos que correspondem aos atributos da reserva já não são cobrados nas tarifas pagas. Uma reserva não cobre taxas de networking ou armazenamento associadas à cache. No final do período de reserva, o benefício de faturação expira e o Azure Cache para Redis é cobrado pelo preço de pagamento como você vai. As reservas não renovam automaticamente. Para obter informações sobre preços, consulte a [Cache Azure para a oferta de capacidade reservada redis.](https://azure.microsoft.com/pricing/details/cache)

Pode comprar Azure Cache para a capacidade reservada redis no [portal Azure.](https://portal.azure.com/) Para comprar a capacidade reservada:

* Você deve estar na função de proprietário para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
* Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem adquirir a Cache Azure para a capacidade reservada redis.

Para obter os detalhes sobre como os clientes da empresa e os clientes Pay-As-You-Go são cobrados para compras de reservas, consulte [a compreensão do uso da reserva Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) e [compreenda o uso da reserva Azure para a sua subscrição Pay-As-You-Go.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)


## <a name="determine-the-right-cache-size-before-purchase"></a>Determine o tamanho certo da cache antes da compra

O tamanho da reserva deve basear-se na quantidade total de tamanho de memória utilizada pela cache existente ou em breve implantada numa região específica e utilizando o mesmo nível de serviço.

Por exemplo, suponhamos que esteja a executar dois caches - um a 13 GB e o outro a 26 GB. Vai precisar dos dois por pelo menos um ano. Além disso, vamos supor que planeia escalar os caches de 13 GB existentes para 26 GB durante um mês para satisfazer a sua procura sazonal, e depois reduzir. Neste caso, pode adquirir cache de 1 P2 e cache 1 P3 ou 3 caches P2 numa reserva de um ano para maximizar a poupança. Receberá desconto na quantidade total de memória de cache que reserva, independentemente da forma como esse valor é atribuído através dos seus caches.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Compre Azure Cache para a capacidade reservada da Redis

Pode comprar uma instância VM reservada no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/). Pague pela reserva [antecipadamente ou com pagamentos mensais](../cost-management-billing/reservations/prepare-buy-reservation.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. **Selecione Adicionar** e, em seguida, no painel de reservas de compra, selecione **Azure Cache para Redis** para comprar uma nova reserva para os seus caches.
4. Preencha os campos necessários. Bases de dados existentes ou novas que correspondam aos atributos que seleciona qualificam-se para obter o desconto de capacidade reservado. O número real da sua Cache Azure para instâncias Redis que obtêm o desconto dependem do âmbito e quantidade selecionados.


![Visão geral dos preços reservados](media/cache-reserved-pricing/cache-reserved-price.png)


A tabela a seguir descreve os campos necessários.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A subscrição utilizada para pagar a Cache Azure para reserva de capacidade reservada redis. O método de pagamento da subscrição é cobrado os custos iniciais da Cache Azure para reserva de capacidade reservada redis. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um acordo individual com preços pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição de empresa, os encargos são deduzidos do saldo do pré-pagamento Azure (anteriormente chamado de compromisso monetário) ou cobrados como excesso de idade. Para uma subscrição individual com preços pay-as-you-go, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.
| Âmbito | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: </br></br> **Partilhado**, o desconto de reserva é aplicado à Azure Cache para instâncias Redis que estão a decorrer em quaisquer subscrições dentro do seu contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Subscrição única**, o desconto de reserva é aplicado à Azure Cache para instâncias Redis nesta subscrição. </br></br> **Grupo de recursos únicos**, o desconto de reserva é aplicado à Cache Azure para instâncias Redis na subscrição selecionada e no grupo de recursos selecionado dentro dessa subscrição.
| Região | A região de Azure que está coberta pela Cache Azure para reserva de capacidade reservada redis.
| Escalão de preço | O nível de serviço para o Cache Azure para servidores Redis.
| Termo | Um ano ou três anos
| Quantidade | A quantidade de recursos computacionares que estão a ser adquiridos dentro da Cache Azure para reserva de capacidade reservada redis. A quantidade é uma série de caches na região de Azure selecionada e nível de serviço que estão sendo reservados e receberá o desconto de faturação. Por exemplo, se estiver a executar ou a planear executar um Cache Azure para servidores Redis com a capacidade total de cache de 26 GB na região leste dos EUA, então especificaria a quantidade que lhe dá o equivalente a 26 GB para maximizar o benefício para todos os caches. Isto pode ser 1 caches P3 ou 2 P2.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="cache-size-flexibility"></a>Flexibilidade do tamanho da cache

A flexibilidade do tamanho da cache ajuda-o a escalar para cima ou para baixo dentro de um nível de serviço e região, sem perder o benefício de capacidade reservada.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva é aplicado automaticamente na Cache Azure para instâncias Redis que correspondem ao âmbito e atributos da reserva. Pode atualizar o âmbito da reserva através do portal Azure, PowerShell, Azure CLI ou da API.

*  Para saber como são aplicados descontos de capacidade reservados à Azure Cache para Redis, consulte [o desconto de reserva Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

    * [O que são as reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Gerir o Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Compreender o desconto das Reservas do Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [Compreender a utilização de reservas na sua subscrição Pay As You Go](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [Compreender a utilização de reservas na inscrição Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](/partner-center/azure-reservations) (Reservas do Azure no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros)