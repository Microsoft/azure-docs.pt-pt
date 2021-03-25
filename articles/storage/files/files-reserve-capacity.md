---
title: Otimizar custos para ficheiros Azure com capacidade reservada
titleSuffix: Azure Files
description: Saiba como economizar custos em implementações de partilha de ficheiros Azure utilizando a capacidade reservada dos Ficheiros Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027665"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Otimizar custos para ficheiros Azure com capacidade reservada
Você pode economizar dinheiro nos custos de armazenamento para ações de arquivo Azure com reservas de capacidade. A capacidade reservada da Azure Files oferece-lhe um desconto na capacidade de armazenamento quando se compromete a uma reserva por um ano ou três anos. Uma reserva proporciona uma capacidade de armazenamento fixa para o termo da reserva.

A capacidade reservada da Azure Files pode reduzir significativamente os seus custos de capacidade para armazenar dados nas suas ações de ficheiroS Azure. Quanto economizar dependerá da duração da sua reserva, da capacidade total que escolhe reservar, e das definições de nível e redundância que escolheu para si ações de ficheiros Azure. A capacidade reservada proporciona um desconto de faturação e não afeta o estado das suas ações de ficheiros Azure.

Para obter informações sobre a capacidade de reserva para ficheiros Azure, consulte [os preços dos Ficheiros Azure.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="reservation-terms-for-azure-files"></a>Termos de reserva para Ficheiros Azure
As secções seguintes descrevem os termos de uma reserva de capacidade dos Ficheiros Azure.

### <a name="reservation-capacity"></a>Capacidade de reserva
Pode adquirir a capacidade reservada da Azure Files em unidades de 10 TiB e 100 TiB por mês para um período de um ano ou três anos.

### <a name="reservation-scope"></a>Âmbito de reserva
A capacidade reservada do Azure Files está disponível para uma única subscrição ou para várias subscrições (âmbito partilhado). Quando se aplica a uma única subscrição, o desconto de reserva é aplicado apenas à subscrição selecionada. Quando traçado para várias subscrições, o desconto de reserva é partilhado entre essas subscrições dentro do contexto de faturação do cliente. Uma reserva aplica-se à sua utilização dentro do âmbito adquirido e não pode limitar-se a uma conta de armazenamento, contentor ou objeto específico dentro da subscrição.

Uma reserva de capacidade para Azure Files cobre apenas a quantidade de dados que é armazenado em um grupo de subscrição ou recursos partilhados. As taxas de transação, largura de banda e transferência de dados não estão incluídas na reserva. Assim que comprar uma reserva, os encargos de capacidade que correspondam aos atributos da reserva são cobrados às taxas de desconto em vez das tarifas de pagamento. Para mais informações sobre reservas da Azure, veja [o que são Reservas Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)

### <a name="supported-tiers-and-redundancy-options"></a>Níveis suportados e opções de redundância
A capacidade reservada da Azure Files só está disponível para ações de ficheiros padrão implantadas nas contas de armazenamento da versão 2 (GPv2). A capacidade reservada não está disponível para ações de ficheiros Azure nos níveis otimizados de prémio ou transação.

Atualmente, apenas as ações de ficheiros Azure nos níveis quentes e frescos suportam reservas. Todos os despedimentos de armazenamento apoiam reservas. Para obter mais informações sobre as opções de despedimento, consulte [a redundância da Azure Files](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Requisitos de segurança para compra
Para adquirir a capacidade reservada:

- Você deve estar na função **Proprietário** para pelo menos uma Enterprise ou subscrição individual com taxas pay-as-you-go.
- Para as subscrições da Enterprise, **adicionar instâncias reservadas** deve ser ativado no portal EA. Ou, se essa definição estiver desativada, deve ser um Administrador EA na subscrição.
- Para o programa Cloud Solution Provider (CSP), apenas os agentes administrativos ou agentes de vendas podem comprar capacidade reservada a Azure Files.

## <a name="determine-required-capacity-before-purchase"></a>Determinar a capacidade necessária antes da compra
Ao adquirir uma reserva Azure Files, deve escolher a opção de região, nível e redundância para a reserva. A sua reserva é válida apenas para os dados armazenados nessa região, nível de nível e redundância. Por exemplo, suponha que você compra uma reserva de dados em EUA West para o nível quente usando armazenamento redundante de zona (ZRS). Essa reserva não se aplicará a dados no Leste dos EUA, dados no nível cool, ou dados em armazenamento geo-redundante (GRS). No entanto, pode adquirir outra reserva para as suas necessidades adicionais.  

As reservas estão disponíveis para 10 blocos TiB ou 100 TiB, com descontos mais elevados para 100 blocos TiB. Ao adquirir uma reserva no portal Azure, a Microsoft poderá fornecer-lhe recomendações baseadas no seu uso anterior para ajudar a determinar que reserva deve comprar.

## <a name="purchase-azure-files-reserved-capacity"></a>Comprar Ficheiros Azure reservados
Pode adquirir a capacidade reservada da Azure Files através do [portal Azure.](https://portal.azure.com) Pague pela reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre a compra com pagamentos mensais, consulte [as reservas do Buy Azure com pagamentos antecipados ou mensais.](../../cost-management-billing/reservations/prepare-buy-reservation.md)

Para ajudar a identificar os termos de reserva adequados para o seu cenário, consulte [o desconto de capacidade reservado para o Azure Storage.](../../cost-management-billing/reservations/understand-storage-charges.md)

Siga estes passos para adquirir a capacidade reservada:

1. Navegue para a lâmina [de reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.  
1. Selecione **Azure Files** para comprar uma nova reserva.  
1. Preencha os campos necessários, conforme descrito no quadro seguinte:

    ![Screenshot mostrando como comprar capacidade reservada](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Campo  |Descrição  |
   |---------|---------|
   |**Âmbito**   |  Indica quantas subscrições podem usar o benefício de faturação associado à reserva. Também controla a forma como a reserva é aplicada a subscrições específicas. <br/><br/> Se selecionar **Shared,** o desconto de reserva é aplicado à capacidade do Azure Files em qualquer subscrição dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para os clientes pay-as-you-go, o âmbito partilhado inclui todas as subscrições individuais com taxas pay-as-you-go criadas pelo administrador da conta.  <br/><br/>  Se selecionar **uma subscrição única,** o desconto de reserva é aplicado à capacidade de Azure Files na subscrição selecionada. <br/><br/> Se selecionar **um grupo de recursos único,** o desconto de reserva é aplicado à capacidade do Azure Files na subscrição selecionada e ao grupo de recursos selecionado dentro dessa subscrição. <br/><br/> Pode alterar o âmbito da reserva depois de comprar a reserva.  |
   |**Subscrição**  | A subscrição que é usada para pagar a reserva Azure Files. O método de pagamento na subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos: <br/><br/>  Acordo de Empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma subscrição da Enterprise, os encargos são deduzidos do saldo Azure Prepayment (anteriormente chamado compromisso monetário) ou cobrados como overage. <br/><br/> Subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma subscrição individual com taxas pay-as-you-go, os encargos são cobrados no cartão de crédito ou método de pagamento de fatura na subscrição.    |
   | **Região** | A região onde a reserva está em vigor. |
   | **Escalão de serviço** | O nível em que a reserva está em vigor. As opções incluem *Hot* and *Cool*. |
   | **Redundância** | A opção de despedimento para a reserva. As opções incluem *LRS,* *ZRS,* *GRS* e *GZRS*. Para obter mais informações sobre as opções de despedimento, consulte [a redundância da Azure Files](storage-files-planning.md#redundancy). |
   | **Frequência de faturação** | Indica com que frequência a conta é faturada para a reserva. As opções incluem *Mensal ou* *Inicial.* |
   | **Tamanho** | A quantidade de capacidade de reserva. |
   |**Termo**  | Um ano ou três anos.   |

1. Depois de selecionar os parâmetros para a sua reserva, o portal Azure apresenta o custo. O portal também mostra a percentagem de descontos sobre a faturação paga.

1. Na lâmina **de reservas de compra,** reveja o custo total da reserva. Também pode fornecer um nome para a reserva.

Após a compra de uma reserva, é automaticamente aplicada a quaisquer ações de ficheiroS Azure existentes que correspondam aos termos da reserva. Se ainda não criou nenhuma ação de ficheiroS Azure, a reserva aplicar-se-á sempre que criar um recurso que corresponda aos termos da reserva. Em qualquer dos casos, o prazo da reserva começa imediatamente após uma compra bem sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva
Pode trocar ou reembolsar uma reserva, com determinadas limitações. Estas limitações são descritas nas seguintes secções.

Para trocar ou reembolsar uma reserva, navegue para os detalhes da reserva no portal Azure. Selecione **Troca** ou **Reembolso**, e siga as instruções para enviar um pedido de apoio. Quando o pedido tiver sido processado, a Microsoft enviar-lhe-á um e-mail para confirmar a conclusão do pedido.

Para obter mais informações sobre as políticas de Reservas Azure, consulte [trocas de self-service e reembolsos para Reservas Azure.](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

### <a name="exchange-a-reservation"></a>Trocar uma reserva
A troca de uma reserva permite-lhe receber um reembolso proscente com base na parte não utilizada da reserva. Em seguida, pode aplicar o reembolso ao preço de compra de uma nova reserva Azure Files.

Não há limite para o número de trocas que pode fazer. Além disso, não há nenhuma taxa associada a uma troca. A nova reserva que comprar deve ter um valor igual ou superior ao crédito proscintado da reserva original. Uma reserva Azure Files só pode ser trocada por outra reserva de Ficheiros Azure, e não para reserva para qualquer outro serviço Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva
Pode cancelar uma reserva de Ficheiros Azure a qualquer momento. Quando você cancelar, você receberá um reembolso prorated com base no prazo restante da reserva, menos uma taxa de rescisão antecipada de 12%. O reembolso máximo por ano é $50.000.

Cancelar uma reserva termina imediatamente a reserva e devolve os meses restantes à Microsoft. O restante saldo prostado, menos a taxa, será reembolsado à sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva
Quando uma reserva expira, qualquer capacidade de Azure Files que esteja a utilizar ao abrigo dessa reserva é faturada à taxa de pagamento como vai. As reservas não se renovam automaticamente.

Receberá uma notificação por e-mail 30 dias antes do termo da reserva, e novamente na data de validade. Para continuar a tirar partido das economias de custos que uma reserva proporciona, renove-a o mais tardar na data de validade.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos
Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [O que são as reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como os descontos de reserva são aplicados aos serviços de armazenamento Azure](../../cost-management-billing/reservations/understand-storage-charges.md)