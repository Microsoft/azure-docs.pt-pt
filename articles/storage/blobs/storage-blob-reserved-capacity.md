---
title: Otimizar os custos do Armazenamento de blobs com a capacidade reservada
titleSuffix: Azure Storage
description: Saiba mais sobre a compra da Azure Storage reservada capacidade para economizar custos em blob de bloco e recursos Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6fd791495602846b95e2dd3e99423db5505f5307
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600919"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Otimizar os custos do Armazenamento de blobs com a capacidade reservada

Você pode economizar dinheiro em custos de armazenamento para dados blob com capacidade reservada Azure Storage. A capacidade reservada para o Azure Storage oferece-lhe um desconto na capacidade de blobs de blocos e para dados da Azure Data Lake Storage Gen2 em contas de armazenamento padrão quando se compromete a uma reserva por um ano ou três anos. Uma reserva proporciona uma capacidade de armazenamento fixa para o termo da reserva.

A capacidade reservada para o Azure Storage pode reduzir significativamente os seus custos de capacidade para blobs de blocos e dados da Azure Data Lake Storage Gen2. As economias de custos alcançadas dependem da duração da sua reserva, da capacidade total que escolhe reservar, e do nível de acesso e tipo de redundância que escolheu para a sua conta de armazenamento. A capacidade reservada proporciona um desconto de faturação e não afeta o estado dos seus recursos de Armazenamento Azure.

Para obter mais informações sobre os preços de reserva do Armazenamento do Microsoft Azure, veja os [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e os [Preços do Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Termos de reserva para armazenamento Azure

As secções seguintes descrevem os termos de uma reserva de armazenamento Azure.

### <a name="reservation-capacity"></a>Capacidade de reserva

Você pode comprar a capacidade reservada a Azure Storage em unidades de 100 TiB e 1 PiB por mês para um período de um ano ou três anos.

### <a name="reservation-scope"></a>Âmbito de reserva

A capacidade reservada para o Azure Storage está disponível para uma única subscrição ou para várias subscrições (âmbito partilhado). Quando se aplica a uma única subscrição, o desconto de reserva é aplicado apenas à subscrição selecionada. Quando traçado para várias subscrições, o desconto de reserva é partilhado entre essas subscrições dentro do contexto de faturação do cliente.

Ao adquirir a capacidade reservada para o Azure Storage, pode utilizar a sua reserva tanto para os dados block blob como para os dados do Azure Data Lake Storage Gen2. Uma reserva é aplicada ao seu uso dentro do âmbito adquirido e não pode ser limitada a uma conta de armazenamento, contentor ou objeto específico dentro da subscrição.

Uma reserva de Armazenamento Azure cobre apenas a quantidade de dados que é armazenado em um grupo de subscrição ou recursos partilhados. A eliminação precoce, as operações, a largura de banda e os encargos de transferência de dados não estão incluídos na reserva. Assim que comprar uma reserva, os encargos de capacidade que correspondam aos atributos da reserva são cobrados às taxas de desconto em vez de às tarifas pagas. Para mais informações sobre reservas da Azure, veja [o que são Reservas Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Tipos de conta suportados, níveis e opções de redundância

A capacidade reservada para o Armazenamento Azure está disponível para recursos em contas de armazenamento padrão, incluindo contas de armazenamento v2 (GPv2) e Blob.

Todos os níveis de acesso (quentes, frescos e arquivos) são suportados para reservas. Para obter mais informações sobre os níveis de acesso, consulte [o armazenamento do Azure Blob: níveis de acesso quentes, frescos e de arquivo.](storage-blob-storage-tiers.md)

Todos os tipos de despedimentos são apoiados para reservas. Para obter mais informações sobre as opções de despedimento, consulte [a redundância do Azure Storage](../common/storage-redundancy.md).

> [!NOTE]
> A capacidade reservada para o Armazenamento Azure não está disponível para contas de armazenamento premium, contas de armazenamento v1 (GPv1) de uso geral, Azure Data Lake Storage Gen1, blobs de página, armazenamento de fila Azure, armazenamento de mesa Azure ou Arquivos Azure.  

### <a name="security-requirements-for-purchase"></a>Requisitos de segurança para compra

Para adquirir a capacidade reservada:

- Você deve estar na função **Proprietário** para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
- Para as subscrições da Enterprise, **adicionar instâncias reservadas** deve ser ativado no portal EA. Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem comprar capacidade reservada ao Armazenamento Azure Blob.

## <a name="determine-required-capacity-before-purchase"></a>Determinar a capacidade necessária antes da compra

Ao adquirir uma reserva de Armazenamento Azure, deve escolher a região, o nível de acesso e a opção de redundância para a reserva. A sua reserva é válida apenas para os dados armazenados naquela região, nível de acesso e redundância. Por exemplo, suponha que você compra uma reserva de dados em EUA West para o nível quente usando armazenamento redundante de zona (ZRS). Não é possível utilizar a mesma reserva para dados no Us East, dados no nível de arquivo ou dados em armazenamento geo-redundante (GRS). No entanto, pode adquirir outra reserva para as suas necessidades adicionais.  

As reservas estão disponíveis hoje para 100 blocos TiB ou 1 PiB, com descontos mais elevados para 1 blocos PiB. Ao adquirir uma reserva no portal Azure, a Microsoft poderá fornecer-lhe recomendações baseadas no seu uso anterior para ajudar a determinar que reserva deve comprar.

## <a name="purchase-azure-storage-reserved-capacity"></a>Comprar Capacidade reservada para armazenamento Azure

Pode adquirir a capacidade reservada para o Azure Storage através do [portal Azure.](https://portal.azure.com) Pague pela reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre a compra com pagamentos mensais, consulte [as reservas do Buy Azure com pagamentos antecipados ou mensais.](../../cost-management-billing/reservations/prepare-buy-reservation.md)

Para obter ajuda na identificação dos termos de reserva adequados para o seu cenário, consulte [o desconto de capacidade reservado para o Azure Storage.](../../cost-management-billing/reservations/understand-storage-charges.md)

Siga estes passos para adquirir a capacidade reservada:

1. Navegue para o painel [de reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.  
1. Selecione **Azure Blob Storage** para comprar uma nova reserva.  
1. Preencha os campos necessários, conforme descrito no quadro seguinte:

    ![Screenshot mostrando como comprar capacidade reservada](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Campo  |Descrição  |
   |---------|---------|
   |**Âmbito**   |  Indica quantas subscrições podem usar o benefício de faturação associado à reserva. Também controla a forma como a reserva é aplicada a subscrições específicas. <br/><br/> Se selecionar **Shared,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure em qualquer subscrição dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para os clientes pay-as-you-go, o âmbito partilhado inclui todas as subscrições individuais com taxas pay-as-you-go criadas pelo administrador da conta.  <br/><br/>  Se selecionar **uma subscrição única,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada. <br/><br/> Se selecionar **um grupo de recursos único,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada e ao grupo de recursos selecionado dentro dessa subscrição. <br/><br/> Pode alterar o âmbito da reserva depois de comprar a reserva.  |
   |**Subscrição**  | A subscrição que é usada para pagar a reserva de armazenamento Azure. O método de pagamento na subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos: <br/><br/>  Acordo de Empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma subscrição da Enterprise, os encargos são deduzidos do saldo Azure Prepayment (anteriormente chamado compromisso monetário) ou cobrados como overage. <br/><br/> Subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma subscrição individual com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou método de pagamento de fatura na subscrição.    |
   | **Região** | A região onde a reserva está em vigor. |
   | **Camada de acesso** | O nível de acesso onde está em vigor a reserva. As opções incluem *Hot,* *Cool* ou *Archive*. Para obter mais informações sobre os níveis de acesso, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo.](storage-blob-storage-tiers.md) |
   | **Redundância** | A opção de despedimento para a reserva. As opções incluem *LRS*, *ZRS,* *GRS,* *GZRS,* *RA-GRS* e *RA-GZRS*. Para obter mais informações sobre as opções de despedimento, consulte [a redundância do Azure Storage](../common/storage-redundancy.md). |
   | **Frequência de faturação** | Indica com que frequência a conta é faturada para a reserva. As opções incluem *Mensal ou* *Inicial.* |
   | **Tamanho** | A quantidade de capacidade de reserva. |
   |**Termo**  | Um ano ou três anos.   |

1. Depois de selecionar os parâmetros para a sua reserva, o portal Azure apresenta o custo. O portal também mostra a percentagem de descontos sobre a faturação paga.

1. No painel **de reservas de compras,** reveja o custo total da reserva. Também pode fornecer um nome para a reserva.

    ![Screenshot mostrando como comprar uma reserva](media/storage-blob-reserved-capacity/purchase-reservations.png)

Depois de adquirir uma reserva, é automaticamente aplicada a qualquer blob de bloco de armazenamento Azure ou recursos Azure Data Lake Storage Gen2 que correspondam aos termos da reserva. Se ainda não criou quaisquer recursos de Armazenamento Azure, a reserva aplicar-se-á sempre que criar um recurso que corresponda aos termos da reserva. Em qualquer dos casos, o prazo da reserva começa imediatamente após uma compra bem sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva

Pode trocar ou reembolsar uma reserva, com determinadas limitações. Estas limitações são descritas nas seguintes secções.

Para trocar ou reembolsar uma reserva, navegue para os detalhes da reserva no portal Azure. Selecione **Troca** ou **Reembolso**, e siga as instruções para enviar um pedido de apoio. Quando o pedido tiver sido processado, a Microsoft enviar-lhe-á um e-mail para confirmar a conclusão do pedido.

Para obter mais informações sobre as políticas de Reservas Azure, consulte [trocas de self-service e reembolsos para Reservas Azure.](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

### <a name="exchange-a-reservation"></a>Trocar uma reserva

A troca de uma reserva permite-lhe receber um reembolso proscente com base na parte não utilizada da reserva. Em seguida, pode aplicar o reembolso ao preço de compra de uma nova reserva de Armazenamento Azure.

Não há limite para o número de trocas que pode fazer. Além disso, não há nenhuma taxa associada a uma troca. A nova reserva que comprar deve ter um valor igual ou superior ao crédito proscintado da reserva original. Uma reserva de Armazenamento Azure pode ser trocada apenas por outra reserva de Armazenamento Azure, e não para reserva para qualquer outro serviço Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva

Pode cancelar uma reserva de Armazenamento Azure a qualquer momento. Quando você cancelar, você receberá um reembolso prorated com base no prazo restante da reserva, menos uma taxa de rescisão antecipada de 12%. O reembolso máximo por ano é $50.000.

Cancelar uma reserva termina imediatamente a reserva e devolve os meses restantes à Microsoft. O restante saldo prostado, menos a taxa, será reembolsado à sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de Armazenamento Azure que você está usando sob essa reserva é faturada à taxa de pagamento como você vai. As reservas não se renovam automaticamente.

Receberá uma notificação por e-mail 30 dias antes do termo da reserva, e novamente na data de validade. Para continuar a tirar partido das economias de custos que uma reserva proporciona, renove-a o mais tardar na data de validade.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [O que são as reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Compreender como o desconto de reserva é aplicado ao Armazenamento do Microsoft Azure](../../cost-management-billing/reservations/understand-storage-charges.md)