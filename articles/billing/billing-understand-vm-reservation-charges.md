---
title: Compreender o desconto de instâncias de VM reservadas do Azure | Documentos da Microsoft
description: Saiba como o desconto de instância de VM reservada do Azure é aplicado a máquinas virtuais em execução.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370091"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Como o desconto de reserva do Azure é aplicado às máquinas virtuais

Depois de comprar uma instância de Máquina Virtual reservada do Azure, o desconto de reserva é automaticamente aplicado às máquinas virtuais que coincidem com os atributos e a quantidade da reserva. Uma reserva aborda os custos de computação das suas máquinas virtuais.

Um desconto de reserva aplica-se para as VMs de base que o utilizador compra do Azure Marketplace.

Para a capacidade de base de dados de SQL reservadas, consulte [compreender instâncias reservadas do Azure de desconto](billing-understand-reservation-charges.md).

A tabela seguinte ilustra os custos para a máquina virtual depois de comprar uma instância de VM reservada. Em todos os casos, é-lhe cobrado para armazenamento e de rede às tarifas normais.

| Tipo de máquina virtual  | Custos com a instância VM reservadas |
|-----------------------|--------------------------------------------|
|VMs do Linux sem software adicional | A reserva aborda os custos de infraestrutura VM.|
|VMs do Linux com custos de software (por exemplo, Red Hat) | A reserva aborda os custos de infraestrutura. É-lhe cobrado para software adicional.|
|VMs do Windows sem software adicional |A reserva aborda os custos de infraestrutura. É-lhe cobrado para software do Windows.|
|VMs do Windows com software adicional (por exemplo, o SQL server) | A reserva aborda os custos de infraestrutura. Será cobrado de software do Windows e de software adicional.|
|VMs do Windows com [benefício híbrido do Azure](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A reserva aborda os custos de infraestrutura. Os custos de software do Windows estão abrangidos pelo Azure Hybrid Benefit. Software adicional é cobrada em separado.|

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é "*utilização-it-ou-perder-it*". Por isso, se não tiver recursos correspondentes para qualquer hora, em seguida, perderá uma quantidade de reserva para essa hora. Não pode transportar reencaminhar horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente para outro recurso correspondente no âmbito especificado. Se não existem recursos correspondentes encontram-se no âmbito especificado, são as horas reservadas *perdido*.

## <a name="reservation-discount-for-non-windows-vms"></a>Desconto de reserva para VMs não Windows

 O desconto de reserva do Azure é aplicado a execução de instâncias VM numa base horária. As reservas que adquiriu são correspondidas com a utilização emitida por VMs em execução para aplicar o desconto de reserva. Para as VMs que não podem ser executadas a hora completa, será preenchida a reserva de outras VMs que não a utilizar uma reserva, incluindo VMs em execução em simultâneo. No final da hora, o aplicativo de reserva para as VMs na hora está bloqueado. No caso de uma VM não é executado por uma hora ou VMs em simultâneo dentro da hora não o preencha a hora da reserva, a reserva está subutilizada durante essa hora. O gráfico seguinte mostra o aplicativo de uma reserva para utilização da VM faturável. A ilustração baseia-se a compra de reserva de uma e duas instâncias VM correspondentes.

![Captura de ecrã da reserva aplicada um e dois correspondentes a instâncias de VM](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Qualquer utilização que está acima da linha de reserva é cobrada às tarifas pay as you go normais. Não é cobrada qualquer utilização abaixo da linha de reservas, uma vez que tem sido já pagou como parte da compra de reserva.
2. Na hora 1, instância 1 é executado durante horas 0,75 e instância 2 é executada para 0,5 horas. Utilização total de 1 de hora é 1.25 horas. É-lhe cobrado as tarifas pay as you go para as restantes horas 0,25.
3. Para a hora, 2 e 3 de hora, ambas as instâncias tiver sido executado por uma hora cada. Uma instância é abrangida pela reserva e o outro é cobrado às tarifas pay as you go.
4. Hora 4, 1 de instância é executada para 0,5 horas e instância 2 é executada durante uma hora. Instância 1 totalmente é abrangida pela reserva e 0,5 horas de instância 2 está abrangida. É-lhe cobrado a tarifa pay as you go para as horas de 0,5 restantes.

Para compreender e ver a aplicação das reservas de Azure no relatórios de utilização de faturação, veja [compreender a utilização de reserva](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Desconto de reserva para VMs do Windows

Quando estiver a executar instâncias de VM do Windows, a reserva se aplica para cobrir os custos de infraestrutura. A aplicação da reserva para os custos de infraestrutura VM para VMs do Windows é o mesmo para VMs não Windows. É-lhe cobrado separadamente para o software do Windows numa base por vCPU. Ver [os custos de software do Windows com as reservas](billing-reserved-Instance-windows-software-costs.md). O Windows, os custos com de licenciamento para cobrir [Azure híbrido benefício para o Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Desconto pode aplicar a tamanhos diferentes

Quando adquire uma instância de VM reservada, se selecionar **otimizado para**: **flexibilidade de tamanho de instância**, a cobertura de desconto depende de selecionar o tamanho da VM. A reserva pode aplicar os tamanhos de máquinas virtuais (VMs) no mesmo grupo de séries de tamanho. Para obter mais informações, consulte [flexibilidade de tamanho de Máquina Virtual com as instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Desconto aplica-se com a correspondência ServiceType apenas

Um desconto de reserva aplica-se apenas à utilização de VM em que o `ServiceType` valor em `AdditionalInfo` corresponde a reserva adquirida. Aplicação de desconto de reserva ignora o medidor utilizado para as VMs e apenas avalia `ServiceType`. Sabe qual serviço escreva que tiver adquirido a VM. Podem trocar uma reserva de VM para uma reserva de armazenamento premium, ou da forma oposta de armazenamento não premium.

## <a name="classic-vms-and-cloud-services"></a>As VMs clássicas e de serviços em nuvem

Instâncias de VM reservada aplicam-se a ambas as VMs clássicas e serviços cloud quando a flexibilidade de tamanho de instância é ativada automaticamente. Serviços cloud, o desconto de reserva aplica-se apenas ao custo de computação. Quando o desconto de reserva é aplicado a serviços em nuvem, os custos de utilização são divididos em custos de computação (medidor de Linux) e serviços em nuvem no custos (medidor de gestão de serviços cloud). Para obter mais informações, consulte [como o desconto de reserva se aplica aos serviços em nuvem](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas para o Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir reservas para o Azure](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reserva para subscrições de CSP](/partner-center/azure-reservations)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
