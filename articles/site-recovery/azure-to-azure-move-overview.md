---
title: Mover VMs de IaaS do Azure para outra região do Azure com o serviço Azure Site Recovery | Documentos da Microsoft
description: Utilize o Azure Site Recovery para mover VMs de IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: dc49b33fd3e6d582b31af5fe0507884e60205757
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078011"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

O Azure cresce, juntamente com o cliente de base e adiciona suporte para novas regiões manter o ritmo com necessidades de aumento. Novos recursos são também adicionados mensalmente em todos os serviços. Pode querer mover as suas máquinas virtuais (VMs) para uma região diferente ou em zonas de disponibilidade para aumentar a disponibilidade.

Este tutorial descreve os diferentes cenários em que gostaria de mover as suas VMs. Também descreve como configurar a arquitetura na região de destino para alcançar a elevada disponibilidade. 

Neste tutorial, irá aprender:

> [!div class="checklist"]
> 
> * Motivos para mover as VMs
> * Arquiteturas típicas
> * Mover VMs como está para uma região de destino
> * Mover VMs para aumentar a disponibilidade

## <a name="reasons-to-move-azure-vms"></a>Motivos para mover as VMs do Azure

Pode mover as VMs pelos seguintes motivos:

- Já implementada numa única região, e um novo suporte de região foi adicionado o que é mais próxima para os utilizadores finais de serviço ou aplicação. Neste cenário, iria querer mover as suas VMs como é a nova região para reduzir a latência. Utilize a mesma abordagem se de que pretende consolidar subscrições ou se existem regras de governação ou organização que requerem a transferência.
- A VM foi implementada como uma VM de instância única ou como parte de um conjunto de disponibilidade. Se pretender aumentar a disponibilidade SLAs, pode mover as VMs numa zona de disponibilidade.

## <a name="steps-to-move-azure-vms"></a>Passos para mover as VMs do Azure

Mover VMs envolve os seguintes passos:

1. Verifique os pré-requisitos.
2. Prepare as VMs de origem.
3. Prepare-se a região de destino.
4. Copie dados para a região de destino. Utilize a tecnologia de replicação do Azure Site Recovery para copiar dados de VM de origem para a região de destino.
5. Teste a configuração. Depois de concluída a replicação, teste a configuração através de uma ativação pós-falha de teste a uma rede de não produção.
6. Efetue a mudança.
7. Elimine os recursos na região de origem.

> [!NOTE]
> Os detalhes sobre estes passos são fornecidos nas secções seguintes.
> [!IMPORTANT]
> Atualmente, o Azure Site Recovery suporta mover VMs de uma região para outra mas não suporta a movimentação dentro de uma região.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implementação de várias camada

Esta secção descreve as arquiteturas de implementação mais comuns para uma aplicação multicamada no Azure. O exemplo é uma aplicação de três camadas com um IP público. Cada uma das camadas (web, aplicação e base de dados) tem duas VMs e estão ligados por um balanceador de carga do Azure para os outros escalões. O escalão de base de dados tem o SQL Server Always On replicação entre as VMs de elevada disponibilidade.

* **VMs de instância única implementadas em vários escalões**: Cada VM numa camada está configurada como uma VM de instância única e está ligado por balanceadores de carga para as outras camadas. Esta configuração é o mais simples de adotar.

     ![Implementação da VM de instância única em camadas](media/move-vm-overview/regular-deployment.png)

* **As VMs em cada escalão implementado em conjuntos de disponibilidade**: Cada VM numa camada está configurada num conjunto de disponibilidade. [Conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) garantem que as VMs que implementa no Azure são distribuídas por vários nós de hardware isolados num cluster. Isto garante que se ocorrer uma falha de hardware ou software no Azure, apenas um subconjunto das suas VMs são afetados e a solução global permanece disponível e operacional.

     ![Implementação de VMS em conjuntos de disponibilidade](media/move-vm-overview/avset.png)

* **As VMs em cada escalão implementado em zonas de disponibilidade**: Cada VM numa camada está configurada entre [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Uma zona de disponibilidade na região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs por zonas de três numa região do Azure, as suas VMs com eficiência são distribuídas entre três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição em vários domínios de atualização para se certificar de que as VMs em diferentes horários não são atualizadas ao mesmo tempo.

     ![Implementação de zona de disponibilidade](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Mover VMs como está para uma região de destino

Com base na [arquiteturas](#typical-architectures-for-a-multi-tier-deployment) mencionado anteriormente, eis o que as implementações serão semelhante depois de efetuar a migração, pois é a região de destino.

* **VMs de instância única implementadas em vários escalões**

     ![Implementação da VM de instância única em camadas](media/move-vm-overview/single-zone.png)

* **VMs em cada escalão implementado em conjuntos de disponibilidade**

     ![Entre conjuntos de disponibilidade de região](media/move-vm-overview/crossregionaset.png)

* **VMs em cada escalão implementado em zonas de disponibilidade**

     ![Implementação de VMS por zonas de disponibilidade](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Mover VMs para aumentar a disponibilidade

* **VMs de instância única implementadas em vários escalões**

     ![Implementação da VM de instância única em camadas](media/move-vm-overview/single-zone.png)

* **As VMs em cada escalão implementado em conjuntos de disponibilidade**: Pode configurar as VMs num conjunto em zonas de disponibilidade separados, quando ativa a replicação para a sua VM com o Azure Site Recovery de disponibilidade. Depois de concluir a operação de movimentação, o SLA de disponibilidade será 99,9%.

     ![Implementação de VMS em conjuntos de disponibilidade e zonas de disponibilidade](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> 
> * [Mover as VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
> 
> * [Mover VMs do Azure para zonas de disponibilidade](move-azure-vms-avset-azone.md)

