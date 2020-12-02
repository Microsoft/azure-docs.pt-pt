---
title: Compreender o desconto de Azure Reserved VM Instances
description: Saiba como o desconto de Azure Reserved VM Instance é aplicado à execução de máquinas virtuais.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: bda93712dd9a7501fbfddf0e75b8ae3c0088ed55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341245"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Como o desconto de reserva do Azure é aplicado a máquinas virtuais

Depois de comprar a Instância de Máquina Virtual Reservada do Azure, o desconto de reserva é automaticamente aplicado às máquinas virtuais que correspondam aos atributos e à quantidade da reserva. Uma reserva abrange os custos de computação das suas máquinas virtuais.

Aplica-se um desconto de reserva às máquinas virtuais de base que compra no Azure Marketplace.

Para a capacidade reservada da Base de Dados SQL, veja [Compreender o desconto de Instâncias Reservadas do Azure](../reservations/understand-reservation-charges.md).

A tabela a seguir ilustra os custos para a sua máquina virtual após a compra de uma Reserved VM Instance. Em todos os casos, são-lhe cobradas tarifas normais por armazenamento e rede.

| Tipo de máquina virtual  | Custos da Reserved VM Instance |
|-----------------------|--------------------------------------------|
|Máquinas virtuais de Linux sem software adicional | A reserva abrange os custos de infraestrutura da sua máquina virtual.|
|Máquinas virtuais do Linux com custos de software (por exemplo, Red Hat) | A reserva abrange os custos de infraestrutura. É-lhe cobrado software adicional.|
|Máquinas virtuais do Windows sem software adicional |A reserva abrange os custos de infraestrutura. É-lhe cobrado software do Windows.|
|Máquinas virtuais do Windows com software adicional (por exemplo, SQL Server) | A reserva abrange os custos de infraestrutura. É-lhe cobrado software Windows e software adicional.|
|Máquinas virtuais do Windows com o [Benefício Híbrido do Azure](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A reserva abrange os custos de infraestrutura. Os custos de software do Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado em separado.|

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="reservation-discount-for-non-windows-vms"></a>Desconto de reserva para máquinas virtuais não Windows

 O desconto de reserva do Azure é aplicado à execução de instâncias de máquina virtual por hora. As reservas que comprou são correspondidas à utilização emitida pelas máquinas virtuais em execução para aplicar o desconto de reserva. Para máquinas virtuais que não podem executar a hora completa, a reserva será preenchida de outras máquinas virtuais que não usam uma reserva, incluindo máquinas virtuais em execução simultânea. No final da hora, a aplicação de reserva para máquinas virtuais na hora é bloqueada. No caso de uma máquina virtual não ser executada por uma hora ou máquinas virtuais simultâneas dentro da hora não preencherem a hora da reserva, a reserva será subutilizada para essa hora. O grafo a seguir ilustra a aplicação de uma reserva para a utilização de máquina virtual faturável. A ilustração baseia-se numa compra de reserva e duas instâncias de máquina virtual correspondentes.

![Captura de ecrã de uma reserva aplicada e duas instâncias de máquina virtual correspondentes](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. Qualquer utilização acima da linha de reserva é cobrada à tarifa normal pay as you go. Não será faturado pela utilização abaixo da linha de reservas, pois já foi pago como parte da compra de reserva.
2. Na hora 1, a instância 1 é executada por 0,75 horas e a instância 2 é executada por 0,5 horas. A utilização total para a hora 1 é de 1,25 horas. É cobrado tarifas pay as you go relativamente às 0,25 horas restantes.
3. Para a hora 2 e a hora 3, ambas as instâncias foram executadas por 1 hora. Uma instância é coberta pela reserva e a outra é cobrada de acordo à tarifa pay as you go.
4. Para a hora 4, a instância 1 é executada por 0,5 horas e a instância 2 é executada por 1 hora. A instância 1 é totalmente coberta pela reserva e 0,5 horas da instância 2 é coberta. É-lhe cobrada a tarifa pay as you go pela 0,5 hora restante.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Desconto de reserva para máquinas virtuais do Windows

Quando estiver a executar instâncias de máquina virtual do Windows, a reserva será aplicada para cobrir os custos de infraestrutura. A aplicação da reserva para os custos de infraestrutura de máquina virtual para máquinas virtuais do Windows é o mesmo para máquinas virtuais não Windows. É cobrado separadamente para software do Windows por vCPU. Veja [Custos de software do Windows com Reservas](../reservations/reserved-instance-windows-software-costs.md). Pode cobrir os custos de licenciamento do Windows com o [Benefício Híbrido do Azure para Windows Server](../../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>O desconto aplica-se a diferentes tamanhos

Ao comprar uma Instância de VM Reservada e selecionar **Otimizada para flexibilidade de tamanho de instância**, a cobertura do desconto será aplicada ao tamanho da VM que selecionar. Também poderá ser aplicada a outros tamanhos de VMs que estão na mesma série do grupo de flexibilidade de tamanho de instância. Para obter mais informações, veja [Flexibilidade de tamanho da máquina virtual com Reserved VM Instances](../../virtual-machines/reserved-vm-instance-size-flexibility.md).

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>As VMs de armazenamento Premium não usufruem dos descontos não Premium

Eis um exemplo. Suponha que comprou uma reserva para cinco VMs Standard_D1, o desconto da reserva aplicar-se-á apenas às VMs Standard_D1 ou às outras VMs na mesma família de instâncias. O desconto não serão aplicados à VM Standard_DS1 ou a outros tamanhos no grupo de flexibilidade de tamanho de instância DS1.

A aplicação de desconto da reserva ignora o medidor utilizado para as VMs e só avalia ServiceType. Observe o valor `ServiceType` em `AdditionalInfo` para determinar as informações da série ou do grupo de flexibilidade de instância das VMs. Os valores estão no ficheiro CSV de utilização.

Não pode alterar diretamente a série ou o grupo de flexibilidade de instância da reserva após a compra. No entanto, pode *trocar* uma reserva de VM de uma série para outra ou de um grupo de flexibilidade de instância para outro.

## <a name="services-that-get-vm-reservation-discounts"></a>Serviços que recebem descontos de reserva de máquina virtual

As suas reservas de máquina virtual podem aplicar-se à utilização de máquina virtual emitida de vários serviços e não apenas para implementações de máquina virtual. Os recursos que recebem descontos de reserva mudam dependendo da definição de flexibilidade de tamanho de instância.

### <a name="instance-size-flexibility-setting"></a>Definição de flexibilidade de tamanho de instância

A definição de flexibilidade de tamanho de instância determina quais serviços recebem os descontos da instância reservada.

Quer a definição esteja ativada ou desativada, os descontos de reserva serão aplicados automaticamente a qualquer utilização de máquina virtual quando o *ConsumedService* for `Microsoft.Compute`. Portanto, verifique os dados de utilização para o valor *ConsumedService*. Alguns exemplos incluem:

- Máquinas virtuais
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de contentores
- Implementações do Azure Batch (no modo de subscrições do utilizador)
- Serviço de Kubernetes do Azure (AKS)
- Service Fabric

Quando a definição está ativada, os descontos de reserva aplicam-se automaticamente à utilização correspondente da máquina virtual quando o *ConsumedService* é qualquer um dos seguintes itens:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Verifique o valor de *ConsumedService* nos seus dados de utilização para determinar se a utilização está qualificada para descontos de reserva.

Para obter mais informações sobre a flexibilidade de tamanho de instância, veja [Flexibilidade de tamanho de máquina virtual com Instâncias de Máquina Virtual Reservadas](../../virtual-machines/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](../reservations/save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerir reservas do Azure](../reservations/manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](../reservations/understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](../reservations/understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations)
- [Custos de software Windows não incluídos nas reservas](../reservations/reserved-instance-windows-software-costs.md)