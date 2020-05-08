---
title: Otimizar os custos para o armazenamento blob com capacidade reservada - Armazenamento Azure
description: Saiba mais sobre a aquisição de capacidade reservada ao Azure Storage para economizar custos em blocos blob e recursos Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4ba54736abe335b53a3b8c6733063efcb34ea29a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856824"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Otimizar os custos do Armazenamento de blobs com a capacidade reservada

Pode economizar dinheiro em custos de armazenamento para dados blob com capacidade reservada ao Armazenamento Azure. A capacidade reservada ao Azure Storage oferece-lhe um desconto na capacidade de blocos blobs e para os dados do Azure Data Lake Storage Gen2 em contas de armazenamento padrão quando se compromete a uma reserva por um ano ou três anos. Uma reserva fornece uma quantidade fixa de capacidade de armazenamento para o período da reserva.

A capacidade reservada ao Azure Storage pode reduzir significativamente os seus custos de capacidade para blocos de bolhas e dados do Azure Data Lake Storage Gen2. As economias de custos alcançadas dependem da duração da sua reserva, da capacidade total que escolhe reservar, do nível de acesso e do tipo de redundância que escolheu para a sua conta de armazenamento. A capacidade reservada proporciona um desconto de faturação e não afeta o estado dos seus recursos de Armazenamento Azure.

Para obter mais informações sobre os preços de reserva do Armazenamento do Microsoft Azure, veja os [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e os [Preços do Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Termos de reserva para Armazenamento Azure

As seguintes secções descrevem os termos de uma reserva de armazenamento Azure.

### <a name="reservation-capacity"></a>Capacidade de reserva

Pode adquirir capacidade reservada ao Azure Storage em unidades de 100 TB e 1 PB por mês para um período de um ano ou três anos.

### <a name="reservation-scope"></a>Âmbito de reserva

A capacidade reservada ao Azure Storage está disponível para uma única subscrição ou para várias subscrições (âmbito partilhado). Quando se aplica a uma única subscrição, o desconto de reserva é aplicado apenas à subscrição selecionada. Quando consultado em várias subscrições, o desconto de reserva é partilhado através dessas subscrições dentro do contexto de faturação do cliente.

Ao adquirir a capacidade reservada ao Azure Storage, pode utilizar a sua reserva tanto para os dados do Block Blob como do Azure Data Lake Storage Gen2. Uma reserva é aplicada ao seu uso dentro do âmbito adquirido e não pode ser limitada a uma conta de armazenamento específica, recipiente ou objeto dentro da subscrição. Uma reserva não pode ser dividida em várias subscrições.

Uma reserva de armazenamento azure cobre apenas a quantidade de dados que são armazenados em um grupo de subscrição ou recursos partilhados. A eliminação precoce, operações, largura de banda e taxas de transferência de dados não estão incluídas na reserva. Assim que comprar uma reserva, os custos de capacidade que correspondem aos atributos de reserva são cobrados às tarifas de desconto em vez das tarifas de pagamento. Para mais informações sobre reservas do Azure, consulte [o Que são as Reservas Azure?](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Tipos de conta suportados, níveis e opções de despedimento

A capacidade reservada ao Azure Storage está disponível para recursos em contas de armazenamento padrão, incluindo contas de armazenamento v2 (GPv2) e Blob.

Todos os níveis de acesso (quentes, frescos e arquivados) são suportados para reservas. Para obter mais informações sobre os níveis de acesso, consulte o [armazenamento do Azure Blob: hot, cool e archive access tiers](storage-blob-storage-tiers.md).

Todos os tipos de despedimentosão apoiados para reservas. Para obter mais informações sobre opções de despedimento, consulte o [despedimento do Azure Storage.](../common/storage-redundancy.md)

> [!NOTE]
> A capacidade reservada ao Azure Storage não está disponível para contas de armazenamento premium, contas de armazenamento v1 (GPv1) de uso geral, Armazenamento de Lagos Azure Gen1, blobs de página, armazenamento de fila Azure, armazenamento de mesa azure ou Ficheiros Azure.  

### <a name="security-requirements-for-purchase"></a>Requisitos de segurança para compra

Para adquirir a capacidade reservada:

- Você deve estar no papel **de Proprietário** para pelo menos uma Empresa ou subscrição individual com taxas pay-as-you-go.
- Para as subscrições da Enterprise, **adicionar instâncias reservadas** deve ser ativado no portal EA. Ou, se essa definição for desativada, deve ser um Administrador EA na subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas agentes de administração ou agentes de vendas podem comprar capacidade reservada ao Armazenamento Azure Blob.

## <a name="determine-required-capacity-before-purchase"></a>Determinar a capacidade necessária antes da compra

Ao comprar uma reserva de Armazenamento Azure, deve escolher a região, o nível de acesso e a opção de despedimento para a reserva. A sua reserva é válida apenas para dados armazenados naquela região, nível de acesso e nível de despedimento. Por exemplo, suponha que você compra uma reserva para dados no US West para o nível quente usando armazenamento de zona redundante (ZRS). Não é possível utilizar a mesma reserva para dados no Leste dos EUA, dados no nível de arquivo ou dados em armazenamento geo-redundante (GRS). No entanto, pode comprar outra reserva para as suas necessidades adicionais.  

As reservas estão disponíveis hoje para 100 tb ou 1 blocos PB, com descontos mais elevados para 1 blocos PB. Ao adquirir uma reserva no portal Azure, a Microsoft poderá fornecer-lhe recomendações com base no seu uso anterior para ajudar a determinar qual a reserva que deve comprar.

## <a name="purchase-azure-storage-reserved-capacity"></a>Adquirir capacidade reservada ao Armazenamento Azure

Pode adquirir a capacidade reservada ao Azure Storage através do [portal Azure.](https://portal.azure.com) Pague pela reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre a compra com pagamentos mensais, consulte [as reservas do Purchase Azure com pagamentos antecipados ou mensais.](/azure/billing/billing-monthly-payments-reservations)

Para ajudar na identificação dos termos de reserva que estão certos para o seu cenário, consulte Compreender o desconto de [capacidade reservado ao Azure Storage.](../../cost-management-billing/reservations/understand-storage-charges.md)

Siga estes passos para adquirir a capacidade reservada:

1. Navegue para o painel de [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.  
1. Selecione **Armazenamento Azure Blob** para comprar uma nova reserva.  
1. Preencha os campos necessários, conforme descrito no quadro seguinte:

    ![Screenshot mostrando como comprar capacidade reservada](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Campo  |Descrição  |
   |---------|---------|
   |**Âmbito**   |  Indica quantas subscrições podem usar o benefício de faturação associado à reserva. Também controla a forma como a reserva é aplicada a subscrições específicas. <br/><br/> Se selecionar **Shared**, o desconto de reserva é aplicado à capacidade de Armazenamento Azure em qualquer subscrição dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes pay-as-you-go, o âmbito partilhado inclui todas as subscrições individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se selecionar **subscrição Única,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada. <br/><br/> Se selecionar o **grupo de recursos Single,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada e ao grupo de recursos selecionados dentro dessa subscrição. <br/><br/> Pode alterar o âmbito da reserva depois de comprar a reserva.  |
   |**Subscrição**  | A subscrição que é usada para pagar a reserva de armazenamento Azure. O método de pagamento da subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos: <br/><br/>  Acordo empresarial (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma subscrição da Enterprise, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como overage. <br/><br/> Subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma subscrição individual com taxas de pagamento, os encargos são cobrados para o cartão de crédito ou método de pagamento de fatura na subscrição.    |
   | **Região** | A região onde a reserva está em vigor. |
   | **Escalão de acesso** | O nível de acesso onde a reserva está em vigor. As opções incluem *Hot,* *Cool*ou *Archive*. Para obter mais informações sobre os níveis de acesso, consulte o [armazenamento do Azure Blob: hot, cool e archive access tiers](storage-blob-storage-tiers.md). |
   | **Redundância** | A opção de redundância para a reserva. As opções incluem *LRS,* *ZRS,* *GRS,* *GZRS,* *RA-GRS*e *RA-GZRS*. Para obter mais informações sobre opções de despedimento, consulte o [despedimento do Azure Storage.](../common/storage-redundancy.md) |
   | **Frequência de faturação** | Indica quantas vezes a conta é cobrada para a reserva. As opções incluem *Mensal ou* *Frontal*. |
   | **Tamanho** | A região onde a reserva está em vigor. |
   |**Termo**  | Um ano ou três anos.   |

1. Depois de selecionar os parâmetros para a sua reserva, o portal Azure exibe o custo. O portal também mostra a percentagem de desconto sobre a faturação de pagamento.

1. No painel **de reservas de compra,** reveja o custo total da reserva. Também pode fornecer um nome para a reserva.

    ![Screenshot mostrando como comprar uma reserva](media/storage-blob-reserved-capacity/purchase-reservations.png)

Depois de adquirir uma reserva, é automaticamente aplicada a qualquer bloco de armazenamento Azure existente ou recursos Azure Data Lake Storage Gen2 que correspondam aos termos da reserva. Se ainda não criou quaisquer recursos de Armazenamento Azure, a reserva será aplicável sempre que criar um recurso que corresponda aos termos da reserva. Em qualquer dos casos, o prazo da reserva começa imediatamente após uma compra bem sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva

Pode trocar ou reembolsar uma reserva, com determinadas limitações. Estas limitações são descritas nas seguintes secções.

Para trocar ou reembolsar uma reserva, navegue para os detalhes da reserva no portal Azure. Selecione **Troca** ou **Reembolso**e siga as instruções para submeter um pedido de apoio. Quando o pedido tiver sido processado, a Microsoft enviar-lhe-á um e-mail para confirmar a conclusão do pedido.

Para mais informações sobre as políticas de Reservas Azure, consulte [trocas de self-service e reembolsos para Reservas Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Trocar uma reserva

Trocar uma reserva permite-lhe receber um reembolso prostrado com base na parte não utilizada da reserva. Em seguida, pode aplicar o reembolso ao preço de compra de uma nova reserva de armazenamento Azure.

Não há limite para o número de trocas que pode fazer. Além disso, não há nenhuma taxa associada a uma troca. A nova reserva que compra deve ser de valor igual ou superior ao crédito provalorizado da reserva original. Uma reserva de armazenamento Azure só pode ser trocada por outra reserva de Armazenamento Azure, e não para uma reserva para qualquer outro serviço Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva

Pode cancelar uma reserva de armazenamento Azure a qualquer momento. Quando cancelar, receberá um reembolso prostílico com base no restante prazo da reserva, menos uma taxa de rescisão antecipada de 12%. O reembolso máximo por ano é $50.000.

O cancelamento de uma reserva termina imediatamente a reserva e devolve os restantes meses à Microsoft. O restante saldo, menos a taxa, será reembolsado à sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de Armazenamento Azure que você está usando sob essa reserva é faturada à taxa de pagamento como você ir. As reservas não se renovam automaticamente.

Receberá uma notificação por e-mail 30 dias antes da expiração da reserva e novamente na data de validade. Para continuar a tirar partido das poupanças de custos que uma reserva proporciona, renove-a o mais tardar na data de validade.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [O que é o Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Compreender como o desconto de reserva é aplicado ao Armazenamento do Microsoft Azure](../../cost-management-billing/reservations/understand-storage-charges.md)
