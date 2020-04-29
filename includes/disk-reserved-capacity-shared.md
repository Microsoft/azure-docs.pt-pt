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
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77590748"
---
Guarde no seu armazenamento de disco Azure com capacidade reservada. Reservas de armazenamento de discos Azure combinadas com instâncias de máquina virtual reservadas Azure permitem-lhe baixar os custos totais da sua máquina virtual (VM). O desconto de reserva é aplicado automaticamente aos discos correspondentes no âmbito de reserva selecionado. Devido a esta aplicação automática, não precisa de atribuir uma reserva a um disco gerido para obter os descontos.

Os descontos são aplicados de hora em hora, dependendo da utilização do disco. A capacidade reservada não utilizada não se transporta. Os descontos de reserva de armazenamento de discos Azure Dsinam não se aplicam a discos não geridos, discos ultra ou consumo de página blob.

## <a name="determine-your-storage-needs"></a>Determine as suas necessidades de armazenamento

Antes de comprar uma reserva, determine as suas necessidades de armazenamento. Atualmente, as reservas de armazenamento de discos Azure estão disponíveis apenas para sKUs premium Azure. O SKU de um SSD premium determina o tamanho e desempenho do disco.

Ao determinar as suas necessidades de armazenamento, não pense em discos baseados apenas na capacidade. Por exemplo, não pode ter reserva para um disco P40 e usá-lo para pagar dois discos P30 menores. Ao comprar uma reserva, está apenas a comprar uma reserva para o número total de discos por SKU.

Uma reserva de disco é feita por disco SKU. Como resultado, o consumo de reserva baseia-se na unidade do disco SKUs em vez do tamanho fornecido.

Por exemplo, assuma que reserva um disco P40 que tem 2 TiB de capacidade de armazenamento provisionado. Também assuma que aloca apenas dois discos P30. A reserva P40 nesse caso não explica o consumo de P30, e você paga a taxa de pagamento nos discos P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Considerações de compra

Recomendamos as seguintes práticas ao considerar a compra de reservas em disco:

- Analise as suas informações de utilização para ajudar a determinar quais as reservas que deve comprar. Certifique-se de que rastreia a utilização em SKUs do disco em vez da capacidade do disco aprovisionado ou utilizado.
- Examine a sua reserva de disco juntamente com a sua reserva VM. Recomendamos vivamente fazer reservas tanto para o uso de VM como para a utilização do disco para uma poupança máxima. Pode começar por determinar a reserva vm certa e, em seguida, avaliar a reserva do disco. Geralmente, terá uma configuração padrão para cada uma das suas cargas de trabalho. Por exemplo, um servidor SQL Server pode ter dois discos de dados P40 e um disco de sistema operativo P30.
  
  Este tipo de padrão pode ajudá-lo a determinar o valor reservado que pode comprar. Esta abordagem pode simplificar o processo de avaliação e garantir que tem um plano alinhado tanto para o seu VM como para os discos. O plano contém considerações como subscrições ou regiões.

## <a name="purchase-restrictions"></a>Restrições de compra

Os descontos de reserva não estão atualmente disponíveis para o seguinte:

- Discos não geridos ou bolhas de página.
- SSDs padrão ou unidades de disco rígido padrão (HDDs).
- SSD SKUs premium menor que P30: P1, P2, P3, P4, P6, P10, P15 e P20 SSD SKUs.
- Discos nas regiões azure, Azure Germany ou Azure China.

Em circunstâncias raras, o Azure limita a compra de novas reservas a um subconjunto de SKUs do disco devido à baixa capacidade numa região.

## <a name="buy-a-disk-reservation"></a>Comprar uma reserva de disco

Pode adquirir reservas de armazenamento de discos Azure através do [portal Azure.](https://portal.azure.com/) Pode pagar a reserva antecipadamente ou com pagamentos mensais. Para mais informações sobre compras com pagamentos mensais, consulte [reservas de compra com pagamentos mensais.](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)

Siga estes passos para adquirir a capacidade reservada:

1. Vá ao painel de [reservas de compra](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.

1. Selecione **Discos Geridos Azure** para comprar uma reserva.

    ![Painel para reservas de compra](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Especifique os valores exigidos descritos no quadro seguinte:

   |Elemento  |Descrição  |
   |---------|---------|
   |**Âmbito**   |  Quantas subscrições podem usar o benefício de faturação associado à reserva. Este valor também especifica como a reserva é aplicada a subscrições específicas. <br/><br/> Se selecionar **Shared**, o desconto de reserva é aplicado à capacidade de Armazenamento Azure em todas as subscrições dentro do seu contexto de faturação. O contexto de faturação baseia-se na forma como se inscreveu no Azure. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições dentro da inscrição. Para clientes pay-as-you-go, o âmbito partilhado inclui todas as subscrições individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se selecionar **subscrição Única,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada. <br/><br/> Se selecionar um grupo de **recursos únicos,** o desconto de reserva é aplicado à capacidade de Armazenamento Azure na subscrição selecionada e no grupo de recursos selecionados da subscrição. <br/><br/> Pode alterar o âmbito da reserva depois de comprar a reserva.  |
   |**Subscrição**  | A subscrição que usa para pagar a reserva de Armazenamento Azure. O método de pagamento da subscrição selecionada é utilizado para cobrar os custos. A subscrição deve ser um dos seguintes tipos:<br/><ul><li> Acordo Empresarial (números de oferta MS-AZR-0017P e MS-AZR-0148P). Para uma subscrição da Enterprise, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como overage.</li><br/><li>Subscrição individual com taxas pay-as-you-go (números de oferta MS-AZR-0003P e MS-AZR-0023P). Para uma subscrição individual com taxas de pagamento, os encargos são cobrados para o cartão de crédito ou método de pagamento de fatura na subscrição.</li></ul>    |
   | **Discos** | O SKU que quer criar. |
   | **Região** | A região onde a reserva está em vigor. |
   | **Frequência de faturação** | Quantas vezes a conta é cobrada para a reserva. As opções incluem **Mensal e** **Frontal**. |

    ![Faça uma panorâmica para selecionar o produto que pretende comprar.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Depois de especificar os valores para a sua reserva, o portal Azure exibe o custo. O portal também mostra a percentagem de desconto sobre a faturação de pagamento. Selecione **Next** para continuar no painel de **reservas de compra.**

1. No painel de **reservas de compra,** pode nomear a sua reserva e selecionar a quantidade total de reservas que pretende fazer. O número de mapas de reservas para o número de discos. Por exemplo, se quiser reservar uma centena de discos, introduza o valor **de quantidade** **100**.

1. Reveja o custo total da reserva.

    ![O painel de reservas de compra](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Depois de adquirir uma reserva, é automaticamente aplicada a quaisquer recursos de Armazenamento de Discos existentes que correspondam aos termos de reserva. Se ainda não criou quaisquer recursos de Armazenamento de Discos, a reserva aplica-se sempre que cria um recurso que corresponda aos termos de reserva. Em qualquer dos casos, o prazo de reserva começa imediatamente após uma compra bem sucedida.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas dentro de determinadas limitações. Para mais informações, consulte [trocas de self-service e reembolsos para Reservas Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de Armazenamento de Disco Azure que você usa sob essa reserva é faturada à taxa de pagamento-como-você-go. As reservas não se renovam automaticamente.

Receberá uma notificação por e-mail 30 dias antes da expiração da reserva e novamente na data de validade. Para continuar a tirar partido das poupanças de custos que uma reserva proporciona, renove-a o mais tardar na data de validade.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [O que são Reservas Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Compreender como o desconto de reserva é aplicado ao Armazenamento de Discos do Azure](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
