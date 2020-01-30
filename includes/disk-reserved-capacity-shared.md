---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847137"
---
Poupe no seu uso premium de unidade de estado sólido (SSD) com capacidade reservada, combinado com instâncias de máquina virtual reservadas, pode diminuir os seus custos totais de VM. O desconto de reserva é aplicado automaticamente aos discos correspondentes no âmbito de reserva selecionado, não precisa de atribuir uma reserva a um disco gerido para obter os descontos. Os descontos são aplicados de hora em hora na utilização do disco e qualquer capacidade reservada não utilizada não é reativada. O desconto de reserva de disco gerido não se aplica a discos não geridos, discos ultra ou consumo de blob de página.

## <a name="determine-your-storage-needs"></a>Determine as suas necessidades de armazenamento

Antes de comprar uma reserva, deve determinar quais são as suas necessidades de armazenamento. Atualmente, a reserva de disco só está disponível para SSD SKUs premium selecionado. O SKU de um SSD premium determina o tamanho e desempenho do disco. Ao determinar as suas necessidades de armazenamento, não recomendamos pensar em discos apenas como uma capacidade total, não pode usar uma reserva para um disco maior (como um P40) e usá-lo para pagar dois discos menores (P30). Ao comprar uma reserva, está apenas a adquirir o número total de discos por SKU.

A reserva do disco é feita por SKU de disco, daí o consumo de reserva ser baseado na unidade do Disk SKUs em vez do tamanho fornecido. Por exemplo, se reservou 1 P40 de 2 tib capacidade provisionada mas apenas atribuído 2 discos P30, o consumo p30 não será contabilizado para reserva P40 e pagará a taxa de pagamento como você-vai-se.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerações de compra

Recomendamos as seguintes melhores práticas ao considerar a compra de reservas em disco:

- Analise suas informações de uso para ajudar a determinar quais reservas devem ser compradas. Certifique-se de que está a monitorizar a utilização em SKUs de disco em vez de capacidade de disco aprovisionado ou usado. 
- Examine a sua Reserva de Disco juntamente com a sua reserva VM. Recomendamos vivamente fazer reservas tanto para a utilização de VM como para o disco para uma poupança máxima. Pode começar por determinar a reserva VM certa e, em seguida, avaliar a reserva do disco em conformidade. Geralmente, terá uma configuração padrão para cada uma das suas cargas de trabalho, por exemplo, um servidor SQL pode ter dois discos de dados P40 e um disco P30 OS. Este tipo de padrão pode ajudá-lo a determinar a quantidade de reservas que pode comprar. Esta abordagem pode simplificar o processo de avaliação e também garantir que tem um plano alinhado tanto para VM como para Discos em termos de subscrições, regiões e outros. 

## <a name="purchase-restrictions"></a>Restrições de compra

Os descontos de reserva não estão atualmente disponíveis para os seguintes discos:
- Discos/bolhas de página não geridos
- SSD padrão ou HDD padrão
- SKUs Premium inferior ao P30 – As reservas não estão disponíveis para P1/P2/P3/P4/P4/P6/P10/P15/P20/P20 Premium SSD SKUs.
- Clouds - As reservas não estão disponíveis para compra em regiões de Azure Gov, Alemanha ou China.
- Restrições de capacidade - Em circunstâncias raras, o Azure limita a compra de novas reservas para o subconjunto de SKUs do disco, devido à baixa capacidade numa região.

## <a name="buy-a-disk-reservation"></a>Comprar uma Reserva de Disco

Pode adquirir reservas de disco Azure através do [portal Azure.](https://portal.azure.com/) Pode pagar a reserva antecipadamente ou com pagamentos mensais. Para mais informações sobre compras com pagamentos mensais, consulte [reservas de compra com pagamentos mensais.](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)

Siga estas etapas para comprar a capacidade reservada:

1. Navegue para a lâmina de [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.
1. Selecione **Discos Geridos Azure** para comprar uma reserva.

    ![discos reservados-reserva-reserva.png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Preencha os campos obrigatórios, conforme descrito na tabela a seguir:

   |Campo  |Descrição  |
   |---------|---------|
   |**Âmbito**   |  Indica quantas assinaturas podem usar o benefício de cobrança associado à reserva. Ele também controla como a reserva é aplicada a assinaturas específicas. <br/><br/> Se você selecionar **compartilhado**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure em qualquer assinatura no contexto de cobrança. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes corporativos, o escopo compartilhado é o registro e inclui todas as assinaturas dentro do registro. Para clientes pagos conforme o uso, o escopo compartilhado inclui todas as assinaturas individuais com tarifas pagas conforme o uso criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **assinatura única**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada. <br/><br/> Se você selecionar **um único grupo de recursos**, o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo de reserva depois de comprar a reserva.  |
   |**Subscrição**  | A assinatura que é usada para pagar pela reserva de armazenamento do Azure. O método de pagamento na assinatura selecionada é usado para cobrar os custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): para uma assinatura corporativa, os encargos são deduzidos do saldo de compromisso monetário do registro ou cobrados como excedentes. <br/><br/> Assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): para uma assinatura individual com tarifas pagas conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.    |
   | **Discos** | O SKU que procura criar. |
   | **Região** | A região em que a reserva está em vigor. |
   | **Frequência de cobrança** | Indica com que frequência a conta é cobrada pela reserva. As opções incluem *mensalmente* ou *antecipadamente*. |

    ![premium-ssd-reservado-selecção de compra.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Depois de selecionar os parâmetros para sua reserva, o portal do Azure exibirá o custo. O portal também mostra a porcentagem de desconto sobre a cobrança paga conforme o uso. Selecione **Next** para proceder à lâmina de **reservas de compra.**

1. No painel de **reservas de compra,** pode nomear a sua reserva e selecionar a quantidade total de reservas que deseja fazer. O número de reservas mapeia o número de discos. Por exemplo, se quisesse saquear cem discos, mudaria **a quantidade** para 100.
1. Reveja o custo total da reserva.

    ![premium-ssd-reservado-selecting-sku-total-purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Depois de adquirir uma reserva, é automaticamente aplicada a quaisquer recursos de armazenamento do Disco Azure existentes que correspondam aos termos da reserva. Se ainda não criou quaisquer recursos de armazenamento do Disco Azure, a reserva será aplicável sempre que criar um recurso que corresponda aos termos da reserva. Em ambos os casos, o termo da reserva começa imediatamente após uma compra bem-sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Trocar ou reembolsar uma reserva

Com certas limitações, pode trocar ou reembolsar uma reserva.

Para trocar ou reembolsar uma reserva, navegue até os detalhes de reserva na portal do Azure. Selecione **Troca ou Reembolso**e siga as instruções para submeter um pedido de apoio. Quando a solicitação tiver sido processada, a Microsoft lhe enviará um email para confirmar a conclusão da solicitação.

Para obter mais informações sobre as políticas de reservas do Azure, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Trocar uma reserva

A troca de uma reserva permite que você receba um reembolso rateado com base na parte não usada da reserva. Em seguida, pode aplicar o reembolso ao preço de compra de uma nova reserva de disco Azure.
Não existe nenhum limite no número de trocas realizadas. Além disso, não existem honorários associados a uma troca. A nova reserva que você compra deve ser de valor igual ou maior do que o crédito rateado da reserva original. Uma reserva de disco Azure só pode ser trocada por outra reserva de disco Azure, e não para uma reserva para qualquer outro serviço Azure.

### <a name="refund-a-reservation"></a>Reembolsar uma reserva

Pode cancelar uma reserva de disco Azure a qualquer momento. Se você cancelar, você receberá um reembolso prorated com base no restante prazo da reserva, menos uma taxa de rescisão antecipada de 12 por cento. O reembolso máximo por ano é de $50000.
O cancelamento de uma reserva termina imediatamente a reserva e retorna os meses restantes à Microsoft. O saldo rateado restante, menos a taxa, será reembolsado em sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade do Disco Azure que você está usando sob essa reserva é faturada à taxa de pagamento-como-você-go. As reservas não se renovam automaticamente.
Você receberá uma notificação por email 30 dias antes da expiração da reserva e novamente na data de validade. Para continuar aproveitando a economia de custos que uma reserva fornece, renove-a não depois da data de expiração.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [O que são as reservas do Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Compreenda como o seu desconto de reserva é aplicado ao Armazenamento de Discos Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)