---
title: Otimizar os custos para o Armazenamento de Disco Azure com reservas
description: Saiba mais sobre a compra de reservas de armazenamento de disco Azure para economizar custos em discos geridos premium SSD.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: e3642a621253b79fdfcdd8cb2b648dee534c6758
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968690"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Reduzir custos com a Reserva de Discos do Azure

Economize no seu uso de armazenamento de disco Azure com capacidade reservada. As reservas de armazenamento de disco Azure combinadas com as instâncias de máquina virtual reservadas Azure permitem-lhe baixar os custos totais da máquina virtual (VM). O desconto de reserva é aplicado automaticamente aos discos correspondentes no âmbito de reserva selecionado. Por causa desta aplicação automática, não precisa de atribuir uma reserva a um disco gerido para obter os descontos.

Os descontos são aplicados de hora em hora, dependendo da utilização do disco. A capacidade reservada não é desusada. Os descontos de reserva de armazenamento de disco Azure não se aplicam a discos não geridos, discos ultra ou consumo de blob de página.

## <a name="determine-your-storage-needs"></a>Determine as suas necessidades de armazenamento

Antes de comprar uma reserva, determine as suas necessidades de armazenamento. Atualmente, as reservas de armazenamento de disco Azure estão disponíveis apenas para SKUs SSD premium Azure. O SKU de um SSD premium determina o tamanho e desempenho do disco.

Ao determinar as suas necessidades de armazenamento, não pense em discos baseados apenas na capacidade. Por exemplo, não pode ter uma reserva para um disco P40 e usá-lo para pagar dois discos P30 menores. Ao comprar uma reserva, você está apenas comprando uma reserva para o número total de discos por SKU.

Uma reserva de disco é feita por disco SKU. Como resultado, o consumo de reserva baseia-se na unidade dos SKUs do disco em vez do tamanho fornecido.

Por exemplo, assuma que reserva um disco P40 que tem 2 TiB de capacidade de armazenamento a provisionada. Também assuma que aloca apenas dois discos P30. A reserva P40, nesse caso, não explica o consumo de P30, e você paga a taxa de pagamento nos discos P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerações relacionadas com a compra

Recomendamos as seguintes práticas ao considerar a compra de reserva de disco:

- Analise as suas informações de utilização para ajudar a determinar quais as reservas que deve adquirir. Certifique-se de que rastreia a utilização em SKUs de disco em vez de capacidade de disco a provisionada ou usada.
- Examine a sua reserva de disco juntamente com a sua reserva VM. Recomendamos vivamente fazer reservas tanto para a utilização de VM como para a utilização máxima do disco. Pode começar por determinar a reserva VM certa e depois avaliar a reserva do disco. Geralmente, terá uma configuração padrão para cada uma das suas cargas de trabalho. Por exemplo, um servidor SQL Server pode ter dois discos de dados P40 e um disco de sistema operativo P30.
  
  Este tipo de padrão pode ajudá-lo a determinar o valor reservado que pode comprar. Esta abordagem pode simplificar o processo de avaliação e garantir que tem um plano alinhado tanto para o seu VM como para os discos. O plano contém considerações como assinaturas ou regiões.

## <a name="purchase-restrictions"></a>Restrições de compra

Descontos de reserva não estão disponíveis para o seguinte:

- Discos não geridos ou bolhas de página.
- SSDs padrão ou unidades de disco rígido padrão (HDDs).
- SKUs Premium SSD menor que P30: P1, P2, P3, P4, P6, P10, P15 e P20 SSD SKUs.
- Discos em regiões do Governo de Azure, Azure Alemanha ou Azure China.

Em circunstâncias raras, a Azure limita a compra de novas reservas a um subconjunto de SKUs de disco devido à baixa capacidade de uma região.

## <a name="buy-a-disk-reservation"></a>Comprar uma reserva de disco

Pode adquirir reservas de Armazenamento de Disco Azure através do [portal Azure.](https://portal.azure.com/) Pode pagar a reserva adiantadamente ou com pagamentos mensais. Para obter mais informações sobre a compra com pagamentos mensais, consulte [reservas de compra com pagamentos mensais.](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)

Siga estes passos para adquirir a capacidade reservada:

1. Aceda ao painel [de reservas de compras](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.

1. Selecione **Discos Geridos Azure** para comprar uma reserva.

    ![Painel para compra de reservas](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Especificar os valores exigidos descritos no quadro seguinte:

   |Elemento  |Descrição  |
   |---------|---------|
   |**Âmbito**   |  Quantas subscrições podem utilizar o benefício de faturação associado à reserva. Este valor também especifica como a reserva é aplicada a subscrições específicas. <br/><br/> Se selecionar **Shared,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure em cada subscrição dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para os clientes pay-as-you-go, o âmbito partilhado inclui todas as subscrições individuais com taxas pay-as-you-go criadas pelo administrador da conta.  <br/><br/>  Se selecionar **uma subscrição única,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada. <br/><br/> Se selecionar **um grupo de recursos único,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada e no grupo de recursos selecionado dessa subscrição. <br/><br/> Pode alterar o âmbito da reserva depois de comprar a reserva.  |
   |**Subscrição**  | A subscrição que utiliza para pagar a reserva de Armazenamento Azure. O método de pagamento na subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos:<br/><ul><li> Contrato de Empresa (números de oferta MS-AZR-0017P e MS-AZR-0148P). Para uma subscrição da Enterprise, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como excesso.</li><br/><li>Subscrição individual com tarifas pay-as-you-go (números de oferta MS-AZR-0003P e MS-AZR-0023P). Para uma subscrição individual com taxas de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na subscrição.</li></ul>    |
   | **Discos** | O SKU que quer criar. |
   | **Região** | A região onde a reserva está em vigor. |
   | **Frequência de faturação** | Quantas vezes a conta é cobrada para a reserva. As opções incluem **Mensal e** **Inicial.** |

    ![Painel para selecionar o produto que deseja purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Depois de especificar os valores para a sua reserva, o portal Azure apresenta o custo. O portal também mostra a percentagem de descontos sobre a faturação paga. Selecione **Next** para continuar o painel **de reservas de compra.**

1. No painel **de reservas de compras,** pode nomear a sua reserva e selecionar a quantidade total de reservas que pretende efetuar. O número de reservas mapeia para o número de discos. Por exemplo, se pretender reservar cem discos, insira o valor **quantidade** **100**.

1. Reveja o custo total da reserva.

    ![O painel de reservas de compra](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Depois de adquirir uma reserva, é automaticamente aplicada a quaisquer recursos existentes de Armazenamento de Disco que correspondam aos termos da reserva. Se ainda não criou quaisquer recursos de Armazenamento de Discos, a reserva aplica-se sempre que cria um recurso que corresponda aos termos da reserva. Em qualquer dos casos, o termo de reserva começa imediatamente após uma compra bem sucedida.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas dentro de certas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de Armazenamento de Disco Azure que utilize sob essa reserva é faturada à taxa de pagamento como você-go. As reservas não se renovam automaticamente.

Receberá uma notificação por e-mail 30 dias antes do termo da reserva e novamente na data de validade. Para continuar a tirar partido das economias de custos que uma reserva proporciona, renove-a o mais tardar na data de validade.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [O que são as reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Compreenda como o seu desconto de reserva é aplicado ao Azure Disk Storage](../cost-management-billing/reservations/understand-disk-reservations.md)