---
title: Compreender o desconto de instâncias de VM reservadas do Azure | Documentos da Microsoft
description: Saiba como o desconto de instância de VM reservada do Azure é aplicado a máquinas virtuais em execução.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: e2ad086e767afc47fe72b52f934cb3bfc7cc146e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849810"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Compreender a forma como o desconto de reserva do Azure é aplicado às máquinas virtuais

Depois de comprar uma instância de Máquina Virtual reservada do Azure, o desconto de reserva é automaticamente aplicado às máquinas virtuais que coincidem com os atributos e a quantidade da reserva. Uma reserva aborda os custos de computação das suas máquinas virtuais.

Para a capacidade de base de dados de SQL reservadas, consulte [compreender instâncias reservadas do Azure de desconto](billing-understand-reservation-charges.md).

A tabela seguinte ilustra os custos para a máquina virtual depois de comprar uma instância de VM reservada. Em todos os casos, é-lhe cobrado para armazenamento e de rede às tarifas normais.

| Tipo de máquina virtual  | Custos com a instância VM reservadas |
|-----------------------|--------------------------------------------|
|VMs do Linux sem software adicional | A reserva aborda os custos de infraestrutura VM.|
|VMs do Linux com custos de software (por exemplo, Red Hat) | A reserva aborda os custos de infraestrutura. É-lhe cobrado para software adicional.|
|VMs do Windows sem software adicional |A reserva aborda os custos de infraestrutura. É-lhe cobrado para software do Windows.|
|VMs do Windows com software adicional (por exemplo, o SQL server) | A reserva aborda os custos de infraestrutura. Será cobrado de software do Windows e de software adicional.|
|VMs do Windows com [benefício híbrido do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | A reserva aborda os custos de infraestrutura. Os custos de software do Windows estão abrangidos pelo Azure Hybrid Benefit. Software adicional é cobrada em separado.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Aplicação de desconto de reserva para VMs não Windows

 O desconto de reserva do Azure é aplicado a execução de instâncias VM numa base horária. As reservas que adquiriu são correspondidas com a utilização emitida por VMs em execução para aplicar o desconto de reserva. Para as VMs que não podem ser executadas a hora completa, será preenchida a reserva de outras VMs que não a utilizar uma reserva, incluindo VMs em execução em simultâneo. No final da hora, o aplicativo de reserva para as VMs na hora está bloqueado. No caso de uma VM não é executado por uma hora ou VMs em simultâneo dentro da hora não o preencha a hora da reserva, a reserva está subutilizada durante essa hora. O gráfico seguinte mostra o aplicativo de uma reserva para utilização da VM faturável. A ilustração baseia-se a compra de reserva de uma e duas instâncias VM correspondentes.

![Captura de ecrã da reserva aplicada um e dois correspondentes a instâncias de VM](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Qualquer utilização que está acima da linha de reserva é cobrada às tarifas pay as you go normais. Não é cobrada qualquer utilização abaixo da linha de reservas, uma vez que tem sido já pagou como parte da compra de reserva.
2. Na hora 1, instância 1 é executado durante horas 0,75 e instância 2 é executada para 0,5 horas. Utilização total de 1 de hora é 1.25 horas. É-lhe cobrado as tarifas pay as you go para as restantes horas 0,25.
3. Para a hora, 2 e 3 de hora, ambas as instâncias tiver sido executado por uma hora cada. Uma instância é abrangida pela reserva e o outro é cobrado às tarifas pay as you go.
4. Hora 4, 1 de instância é executada para 0,5 horas e instância 2 é executada durante uma hora. Instância 1 totalmente é abrangida pela reserva e 0,5 horas de instância 2 está abrangida. É-lhe cobrado a tarifa pay as you go para as horas de 0,5 restantes.

Para compreender e ver a aplicação das reservas de Azure no relatórios de utilização de faturação, veja [compreender a utilização de reserva](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Aplicação de desconto de reserva para VMs do Windows

Quando estiver a executar instâncias de VM do Windows, a reserva se aplica para cobrir os custos de infraestrutura. A aplicação da reserva para os custos de infraestrutura VM para VMs do Windows é o mesmo para VMs não Windows. É-lhe cobrado separadamente para o software do Windows numa base por vCPU. Ver [os custos de software do Windows com as reservas](https://go.microsoft.com/fwlink/?linkid=862756). O Windows, os custos com de licenciamento para cobrir [Azure híbrido benefício para o Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Desconto aplica-se a tamanhos diferentes com a flexibilidade de tamanho de instância

Quando adquire uma instância de VM reservada, se selecionar **otimizado para**: **flexibilidade de tamanho de instância**, a cobertura de desconto depende de selecionar o tamanho da VM. A reserva pode aplicar os tamanhos de máquinas virtuais (VMs) no mesmo grupo de séries de tamanho. Para obter mais informações, consulte [flexibilidade de tamanho de Máquina Virtual com as instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reserva para subscrições de CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
